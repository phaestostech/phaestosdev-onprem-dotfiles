# MCP Vulnerabilities Reference

MCP tools for CVE and vulnerability queries.

## Vulnerability Finding Tools

### mcp__wiz__list_vulnerability_findings

Query CVEs across cloud resources.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `severity` | string[] | Filter: `["CRITICAL", "HIGH", "MEDIUM", "LOW"]` |
| `hasExploit` | bool | Only exploitable CVEs |
| `hasFix` | bool | Only CVEs with available fixes |
| `first` | int | Limit results |

**Example - Actionable Critical CVEs:**
```
mcp__wiz__list_vulnerability_findings with:
- severity: ["CRITICAL", "HIGH"]
- hasExploit: true
- hasFix: true
- first: 50
```

**Example - All Critical CVEs:**
```
mcp__wiz__list_vulnerability_findings with:
- severity: ["CRITICAL"]
- first: 100
```

### mcp__wiz__list_sast_findings

Query static analysis findings (code vulnerabilities).

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `severity` | string[] | Filter by severity |
| `first` | int | Limit results |

### mcp__wiz__get_sast_finding

Get detailed SAST finding with code context.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `finding_id` | string | SAST finding ID |

**Returns:** Code location, vulnerability details, fix recommendations.

### mcp__wiz__list_attack_surface_findings

Query externally exploitable vulnerabilities.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `severity` | string[] | Filter by severity |
| `first` | int | Limit results |

## Supporting Tools

### mcp__wiz__list_sbom

Get Software Bill of Materials for resources.

**Use case:** Understanding which packages are affected by a CVE.

### mcp__wiz__list_container_images

List container images with vulnerability counts.

**Use case:** Identifying images needing updates.

## Common Vulnerability Workflows

### Critical CVE Triage

1. Find exploitable CVEs with fixes:
   ```
   mcp__wiz__list_vulnerability_findings with:
   - severity: ["CRITICAL"]
   - hasExploit: true
   - hasFix: true
   ```
2. Prioritize by exploit availability
3. Generate remediation tickets

### Zero-Day Response

1. Search for specific CVE patterns via graph search
2. List affected resources
3. Check for available patches

### Container Security Review

1. List images: `mcp__wiz__list_container_images`
2. Query vulnerabilities per image
3. Generate SBOM: `mcp__wiz__list_sbom`

### Code Vulnerability Review

1. List SAST findings: `mcp__wiz__list_sast_findings`
2. Get details: `mcp__wiz__get_sast_finding`
3. Review code context and fix recommendations

## Severity Mapping

| Wiz Severity | CVSS Range | Action |
|--------------|------------|--------|
| CRITICAL | 9.0 - 10.0 | Immediate remediation |
| HIGH | 7.0 - 8.9 | Remediate within 7 days |
| MEDIUM | 4.0 - 6.9 | Remediate within 30 days |
| LOW | 0.1 - 3.9 | Track and plan |
