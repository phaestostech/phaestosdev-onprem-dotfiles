# CLI IaC Scan Reference

Full flag reference for `wizcli iac scan`.

## Required Flags

| Flag | Description |
|------|-------------|
| `--path <dir>` | Path to scan (can repeat for multiple paths) |

## Type Selection

| Flag | Description |
|------|-------------|
| `--types <type>` | IaC types to scan (comma-separated or repeat flag) |

### Supported Types

| Type | Files |
|------|-------|
| `Terraform` | `.tf`, `.tfvars` |
| `CloudFormation` | `.yaml`, `.yml`, `.json` (CFN templates) |
| `Kubernetes` | `.yaml`, `.yml` (K8s manifests) |
| `Dockerfile` | `Dockerfile`, `*.dockerfile` |
| `AzureResourceManager` | Azure ARM templates |
| `Bicep` | `.bicep` files |
| `GitHubActions` | `.github/workflows/*.yml` |
| `Ansible` | Ansible playbooks |

## Scan Options

| Flag | Description | Default |
|------|-------------|---------|
| `--secrets` | Scan for hardcoded secrets | false |

## CloudFormation Specific

| Flag | Description |
|------|-------------|
| `--expand-cloudformation-intrinsics` | Expand CFN intrinsic functions |
| `--parameter-files <path>` | Parameter files for CFN (can repeat) |

## Policy Flags

| Flag | Description |
|------|-------------|
| `--policy <name>` | Apply named policy (can repeat) |
| `--policy-hits-only` | Only show results matching policies |

## Example Commands

### Terraform Scan
```bash
wizcli iac scan \
  --types Terraform \
  --secrets \
  --policy cicd-secrets \
  --policy-hits-only \
  --path ./infra
```

### Kubernetes Manifests
```bash
wizcli iac scan \
  --types Kubernetes \
  --secrets \
  --policy-hits-only \
  --path ./k8s
```

### Dockerfile Analysis
```bash
wizcli iac scan \
  --types Dockerfile \
  --policy-hits-only \
  --path .
```

### CloudFormation with Parameters
```bash
wizcli iac scan \
  --types CloudFormation \
  --expand-cloudformation-intrinsics \
  --parameter-files ./params/prod.json \
  --secrets \
  --policy-hits-only \
  --path ./cfn
```

### GitHub Actions Workflows
```bash
wizcli iac scan \
  --types GitHubActions \
  --secrets \
  --policy cicd-secrets \
  --path .github/workflows
```

### Multiple Types
```bash
wizcli iac scan \
  --types Terraform,Kubernetes,Dockerfile \
  --secrets \
  --policy-hits-only \
  --path .
```

### Multiple Paths
```bash
wizcli iac scan \
  --types Terraform \
  --secrets \
  --path ./infra/aws \
  --path ./infra/gcp \
  --path ./modules
```

## Common Misconfigurations Detected

| Category | Examples |
|----------|----------|
| **Access Control** | Public S3 buckets, open security groups |
| **Encryption** | Unencrypted storage, missing KMS |
| **Logging** | Disabled CloudTrail, missing access logs |
| **Network** | Overly permissive CIDRs, missing VPC flow logs |
| **Secrets** | Hardcoded credentials in templates |
| **Container** | Running as root, privileged containers |
