# Security Scanning Reference

Detailed security scanning commands and patterns for pre-commit validation.

## Trivy - Secret & Misconfiguration Scanning

Use Trivy for secrets detection and misconfiguration scanning.

### Secrets and Hardcoded Credentials

```bash
trivy fs . --scanners secret --severity HIGH,CRITICAL
```

### Sensitive Files and Misconfigurations

```bash
trivy fs . --scanners config --severity HIGH,CRITICAL
```

### Common Secret Patterns to Detect

- API keys: `[A-Za-z0-9_-]{20,}`
- AWS keys: `AKIA[0-9A-Z]{16}`
- Private keys: `-----BEGIN (RSA|EC|OPENSSH) PRIVATE KEY-----`
- Tokens: `ghp_`, `gho_`, `github_pat_`, `xox[baprs]-`
- Connection strings: `mongodb://`, `postgres://`, `mysql://`

### Sensitive Files to Never Commit

- `.env`, `.env.local`, `.env.production`
- `credentials.json`, `secrets.yaml`
- `*.pem`, `*.key`, `id_rsa`, `id_ed25519`
- `serviceAccountKey.json`
- `.npmrc` with auth tokens
- `kubeconfig` files

## Git - Large Files & History Pollution

Use git commands to detect large files and binaries that should not be committed.

### Check for Large Files (>1MB)

```bash
git diff --staged --stat | awk '$3 > 1000000 {print "WARNING: Large file:", $1}'
```

### Check for Binary Files

```bash
git diff --staged --numstat | awk '$1 == "-" {print "WARNING: Binary file:", $3}'
```

### Verify .gitignore Compliance

```bash
# Check if sensitive patterns are ignored
grep -E '\.env|\.pem|\.key|credentials' .gitignore

# List staged files that match sensitive patterns
git diff --staged --name-only | grep -E '\.env|\.pem|\.key|credentials|secret'
```

## Wiz - Vulnerability, Secrets & License Compliance

Use Wiz for vulnerability scanning, secret detection, and license compliance. Only display
**CRITICAL** and **HIGH** severity.

### Full Security Scan with SBOM

```bash
wizcli dir scan \
  --secrets \
  --sbom-format cyclonedx-json \
  --sbom-output-file "$(basename $(pwd))-sbom.json" \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy-hits-only \
  --format human \
  --path .
```

### Vulnerability and Secrets Scan

```bash
wizcli dir scan \
  --secrets \
  --policy cicd-vulnerabilities \
  --policy cicd-secrets \
  --policy-hits-only \
  --format human \
  --path .
```

Filter and report only CRITICAL and HIGH findings from the scan output.

## Combined Security Check Workflow

Run all security checks before committing:

```bash
# Step 1: Trivy for secrets
trivy fs . --scanners secret --severity HIGH,CRITICAL

# Step 2: Wiz for vulnerabilities and secrets (CRITICAL/HIGH only)
wizcli dir scan --secrets --policy cicd-vulnerabilities --policy cicd-secrets --policy-hits-only --path .

# Step 3: Git for large files and binaries
git diff --staged --numstat | awk '$1 == "-" {print "WARNING: Binary file:", $3}'
```

If any scan reports findings, address them before proceeding with the commit.
