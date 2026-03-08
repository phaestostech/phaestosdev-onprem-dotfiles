---
name: wiz-security
description: |
  Use when scanning code, containers, or IaC for secrets and vulnerabilities (wizcli),
  or investigating threats, issues, and CVEs in the Wiz platform (MCP).
  Triggers: "scan for secrets", "check vulnerabilities", "security scan", "find threats",
  "query CVEs", "security posture", "attack surface", pre-commit security checks,
  container image scanning, Terraform/Kubernetes misconfigurations.
---

# Wiz Security

Comprehensive security workflows: **proactive scanning** (Wiz CLI) and **reactive investigation**
(Wiz MCP Server).

## Quick Reference

| I want to... | Tool | Command/Query |
|--------------|------|---------------|
| Scan source code before commit | CLI | `wizcli dir scan` |
| Scan a container image | CLI | `wizcli docker scan` |
| Scan Terraform/K8s configs | CLI | `wizcli iac scan` |
| Find active threats | MCP | `mcp__wiz__list_threats` |
| Query CVEs in production | MCP | `mcp__wiz__graph_search` ⚠️ |
| Search security graph | MCP | `mcp__wiz__graph_search` |
| Check project security score | MCP | `mcp__wiz__get_project` |

⚠️ = See [Known Limitations](#known-limitations) for workarounds

## Authentication

### CLI Authentication

```bash
# Check auth status
wizcli auth --print-token > /dev/null 2>&1 || wizcli auth --use-device-code

# Or use environment variables
export WIZ_CLIENT_ID=<id>
export WIZ_CLIENT_SECRET=<secret>
```

### MCP Authentication

MCP server uses headers configured in `~/.claude/mcp.json`. No action required if already
configured.

---

## Part 1: CLI Scanning (Proactive)

### Directory Scan - Quick

```bash
wizcli dir scan \
  --secrets \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy-hits-only \
  --path .
```

### Directory Scan - Full Compliance

```bash
wizcli dir scan \
  --secrets \
  --sensitive-data \
  --file-hashes-scan \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy cicd-sensitive-data \
  --policy cicd-malware \
  --sbom-format cyclonedx-json \
  --sbom-output-file "$(basename $(pwd))-sbom.json" \
  --tag "branch=$(git rev-parse --abbrev-ref HEAD)" \
  --tag "commit=$(git rev-parse --short HEAD)" \
  --policy-hits-only \
  --path .
```

### Docker Scan

```bash
wizcli docker scan \
  --secrets \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy-hits-only \
  --image myapp:latest
```

### IaC Scan - Terraform

```bash
wizcli iac scan \
  --types Terraform \
  --secrets \
  --policy cicd-secrets \
  --policy-hits-only \
  --path ./infra
```

### IaC Scan - Kubernetes

```bash
wizcli iac scan \
  --types Kubernetes \
  --secrets \
  --policy-hits-only \
  --path ./k8s
```

---

## Part 2: MCP Platform Queries (Reactive)

### Investigate Active Threats

```
Use mcp__wiz__list_threats with:
- severity: ["CRITICAL", "HIGH"]
- status: ["OPEN", "IN_PROGRESS"]
- first: 10
```

### Query Security Issues

```
Use mcp__wiz__list_issues with:
- severity: ["CRITICAL", "HIGH"]
- status: ["OPEN"]
- cloud_provider: ["AWS"] (optional)
```

### Find CVEs

> ⚠️ **Note**: `mcp__wiz__list_vulnerability_findings` may return GraphQL errors. Use
> `graph_search` as a reliable alternative (see [Known Limitations](#known-limitations)).

```
# Primary method (if working):
Use mcp__wiz__list_vulnerability_findings with:
- severity: ["CRITICAL", "HIGH"]
- hasExploit: true
- hasFix: true

# Workaround via graph_search:
Use mcp__wiz__graph_search with:
- query: "Find EC2 instances with critical vulnerabilities"
- query: "Show containers with CVEs that have exploits"
```

### Graph Search (Free Text)

```
Use mcp__wiz__graph_search with natural language:
- "Show me all S3 buckets that are publicly accessible"
- "Find EC2 instances with critical vulnerabilities"
- "List containers running as root"
```

### Get Project Security Posture

```
Use mcp__wiz__get_project with:
- project_search: "my-project-name"
- fetch_overdue_and_created_vs_resolved_trend: true
```

---

## Harrison.ai Standard Policies

| Policy | Detects | When to Use |
|--------|---------|-------------|
| `cicd-secrets` | Hardcoded credentials, API keys, tokens | Always - every scan |
| `cicd-vulnerabilities` | Known CVEs in dependencies | Always - every scan |
| `cicd-sensitive-data` | PII, PHI, PCI data patterns | Before releases, compliance |
| `cicd-malware` | Malicious patterns, suspicious code | Container images, external code |

---

## Common Workflows

### Pre-Commit Security Check

1. Run `wizcli dir scan --secrets --policy-hits-only --path .`
2. If findings, fix before committing
3. Re-scan to verify fixes

### Container Build Security

1. Build image: `docker build -t myapp:latest .`
2. Scan image: `wizcli docker scan --image myapp:latest --secrets`
3. If findings, update Dockerfile and rebuild

### Incident Investigation

1. Query threats: `mcp__wiz__list_threats` with CRITICAL/HIGH severity
2. Get threat details: `mcp__wiz__get_threat` with issue_id
3. Find similar resolved: `mcp__wiz__list_resolved_threats` for remediation guidance
4. Get assignee suggestions: `mcp__wiz__get_issue_assignee_suggestions`

### Vulnerability Triage

1. List CVEs: Use `graph_search` with "Find vulnerabilities with exploits" (see workaround below)
2. Check if fix available: Include "that have fixes available" in graph query
3. Get SBOM context: `mcp__wiz__list_sbom` for affected packages

**Workaround Example:**

```text
mcp__wiz__graph_search with query:
"Show me critical vulnerabilities with known exploits on production EC2 instances"
```

---

## Known Limitations

Some MCP tools may experience intermittent GraphQL errors due to Wiz API schema changes.

### Affected Tools (as of 2026-01-28)

| Tool | Status | Workaround |
|------|--------|------------|
| `mcp__wiz__list_vulnerability_findings` | ❌ GraphQL errors | Use `mcp__wiz__graph_search` |
| `mcp__wiz__list_posture_issues` | ❌ GraphQL errors | Use `mcp__wiz__list_issues` |

### Working Tools (Verified)

| Tool | Status | Notes |
|------|--------|-------|
| `mcp__wiz__list_threats` | ✅ Working | Threat detection queries |
| `mcp__wiz__list_issues` | ✅ Working | Cloud config & toxic combo issues |
| `mcp__wiz__list_resolved_threats` | ✅ Working | Historical threat data |
| `mcp__wiz__graph_search` | ✅ Working | **Recommended for vulnerability queries** |
| `mcp__wiz__list_projects` | ✅ Working | Project listing |
| `mcp__wiz__get_project` | ✅ Working | Project details & security scores |

### Workaround: Using Graph Search for Vulnerabilities

Instead of `list_vulnerability_findings`, use `graph_search` with natural language:

```text
# Find critical vulnerabilities with exploits
mcp__wiz__graph_search: "Show critical vulnerabilities that have known exploits"

# Find vulnerabilities on specific resource types
mcp__wiz__graph_search: "Find EC2 instances with critical CVEs"

# Find fixable vulnerabilities
mcp__wiz__graph_search: "List containers with high severity vulnerabilities that have fixes"
```

### Workaround: Using list_issues for Posture Issues

Instead of `list_posture_issues`, use `list_issues` with filters:

```text
mcp__wiz__list_issues with:
- severity: ["CRITICAL", "HIGH"]
- status: ["OPEN"]
- security_risk: ["VULNERABILITY", "UNPROTECTED_DATA"] (optional)
```

---

## Reference Files

For detailed command options and examples:

- **CLI Directory Scan**: See `references/cli-dir-scan.md`
- **CLI Docker Scan**: See `references/cli-docker-scan.md`
- **CLI IaC Scan**: See `references/cli-iac-scan.md`
- **MCP Threats & Issues**: See `references/mcp-threats-issues.md`
- **MCP Vulnerabilities**: See `references/mcp-vulnerabilities.md`
- **MCP Graph Search**: See `references/mcp-graph-search.md`
