# CLI Directory Scan Reference

Full flag reference for `wizcli dir scan`.

## Required Flags

| Flag | Description |
|------|-------------|
| `--path` | Directory path to scan (required) |

## Scan Type Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--secrets` | Scan for hardcoded secrets | false |
| `--sensitive-data` | Scan for PII/PHI/PCI data patterns | false |
| `--file-hashes-scan` | Scan for malware via file hash signatures | false |

## Policy Flags

| Flag | Description |
|------|-------------|
| `--policy <name>` | Apply named policy (can repeat) |
| `--policy-hits-only` | Only show results matching policies |

### Harrison.ai Standard Policies

- `cicd-secrets` - Hardcoded credentials, API keys, tokens
- `cicd-vulnerabilities` - Known CVEs in dependencies
- `cicd-sensitive-data` - PII, PHI, PCI data patterns
- `cicd-malware` - Malicious patterns, suspicious code

## SBOM Generation

| Flag | Description |
|------|-------------|
| `--sbom-format <format>` | SBOM output format: `cyclonedx-json`, `cyclonedx-xml`, `spdx-json` |
| `--sbom-output-file <path>` | Path for SBOM output file |

## Tagging & Metadata

| Flag | Description |
|------|-------------|
| `--tag <key=value>` | Add metadata tag (can repeat) |

Common tags:
```bash
--tag "branch=$(git rev-parse --abbrev-ref HEAD)"
--tag "commit=$(git rev-parse --short HEAD)"
--tag "repo=$(basename $(git rev-parse --show-toplevel))"
```

## Output Formats

| Flag | Description |
|------|-------------|
| `--output human` | Human-readable output (default) |
| `--output json` | JSON output for automation |
| `--output sarif` | SARIF format for IDE integration |

## Example Commands

### Quick Pre-Commit Scan
```bash
wizcli dir scan \
  --secrets \
  --policy cicd-secrets \
  --policy-hits-only \
  --path .
```

### Full Compliance Scan
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
  --sbom-output-file scan-sbom.json \
  --tag "branch=$(git rev-parse --abbrev-ref HEAD)" \
  --policy-hits-only \
  --path .
```

### CI Pipeline Scan (JSON output)
```bash
wizcli dir scan \
  --secrets \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --output json \
  --path . > scan-results.json
```
