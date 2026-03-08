# CLI Docker Scan Reference

Full flag reference for `wizcli docker scan`.

## Required Flags

| Flag | Description |
|------|-------------|
| `--image <ref>` | Image reference: tag, digest, or .tar file path |

## Image Reference Formats

```bash
# By tag
--image myapp:latest
--image registry.example.com/myapp:v1.2.3

# By digest
--image myapp@sha256:abc123...

# From tar file
--image ./myapp-image.tar
```

## Scan Type Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--secrets` | Scan for hardcoded secrets in layers | false |
| `--sensitive-data` | Scan for PII/PHI/PCI data patterns | false |

## Analysis Flags

| Flag | Description |
|------|-------------|
| `--dockerfile <path>` | Dockerfile for additional context analysis |
| `--group-by layer` | Group findings by image layer |

## Policy Flags

| Flag | Description |
|------|-------------|
| `--policy <name>` | Apply named policy (can repeat) |
| `--policy-hits-only` | Only show results matching policies |

## SBOM Generation

| Flag | Description |
|------|-------------|
| `--sbom-format <format>` | SBOM output format: `cyclonedx-json`, `cyclonedx-xml`, `spdx-json` |
| `--sbom-output-file <path>` | Path for SBOM output file |

## Example Commands

### Quick Container Scan
```bash
wizcli docker scan \
  --secrets \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy-hits-only \
  --image myapp:latest
```

### Scan with Dockerfile Context
```bash
wizcli docker scan \
  --secrets \
  --dockerfile ./Dockerfile \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --image myapp:latest
```

### Layer Analysis
```bash
wizcli docker scan \
  --secrets \
  --group-by layer \
  --image myapp:latest
```

### Full Compliance Scan with SBOM
```bash
wizcli docker scan \
  --secrets \
  --sensitive-data \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy cicd-sensitive-data \
  --policy cicd-malware \
  --sbom-format cyclonedx-json \
  --sbom-output-file container-sbom.json \
  --policy-hits-only \
  --image myapp:latest
```

### Scan Exported Image
```bash
# Export image first
docker save myapp:latest -o myapp.tar

# Scan tar file
wizcli docker scan \
  --secrets \
  --policy cicd-secrets \
  --image ./myapp.tar
```
