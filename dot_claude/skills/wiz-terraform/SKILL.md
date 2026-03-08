---
name: wiz-terraform
description: Generate and review OpenTofu/Terraform HCL for Wiz Cloud security platform. Use when creating Wiz resources (connectors, controls, CCRs, HCRs, projects, automation rules, integrations), reviewing existing Wiz HCL for best practices, or working with Wiz Terraform provider configuration. Triggers include "wiz terraform", "wiz tofu", "wiz connector", "wiz control", "wiz CCR", "wiz HCR", "wiz automation rule", "wiz project", "cloud configuration rule", "host configuration rule", or any Wiz infrastructure-as-code task.
---

# Wiz Terraform/OpenTofu Skill

Generate and review HCL configurations for the Wiz Terraform provider.

## Quick Reference

**Provider source:** `tf.app.wiz.io/wizsec/wiz`
**Current version:** 1.8.1154+ (January 2026)
**Supported runtimes:** Terraform 0.14+ or OpenTofu

## Workflow

### When Generating New Configurations

1. **Identify resources needed** - Map requirements to Wiz resources/data sources
2. **Check reference docs** - Read relevant sections from `references/` for schema details
3. **Generate HCL** - Follow file organization and security conventions below
4. **Validate** - Verify required permissions and cross-resource references

### When Reviewing Existing Configurations

1. **Security audit** - Check for hardcoded credentials, overly permissive configs
2. **Schema validation** - Verify required fields and valid enum values
3. **Best practices** - Check file organization, naming, and tagging
4. **Permission check** - Ensure service account has required permissions

## File Organization Convention

```
project/
├── versions.tf      # Provider requirements
├── providers.tf     # Provider configuration
├── variables.tf     # Input variables
├── outputs.tf       # Output values
├── main.tf          # Primary resources (or split by domain below)
├── connectors.tf    # Cloud connectors (AWS, GCP, K8s, GitHub, GitLab)
├── controls.tf      # Controls and custom controls
├── rules.tf         # CCRs, HCRs, ignore rules, threat detection
├── projects.tf      # Projects and project structure
├── automation.tf    # Automation rules and actions
├── access.tf        # Users, roles, service accounts, SAML
└── terraform.tfvars # Variable values (gitignored)
```

For smaller configurations, consolidate into `main.tf`.

## Security Requirements (Strict)

### Credentials

**REQUIRED:** Use environment variables for authentication:

```hcl
# providers.tf - CORRECT
provider "wiz" {
  # Credentials from WIZ_CLIENT_ID and WIZ_CLIENT_SECRET env vars
}
```

```hcl
# NEVER DO THIS - hardcoded credentials
provider "wiz" {
  client_id = "abc123"        # SECURITY VIOLATION
  secret    = "secret-value"  # SECURITY VIOLATION
}
```

### Permission Validation

Before generating resources, verify the service account has required permissions. Each resource documents its required permissions in the reference files.

Common permission patterns:
- Connectors: `create:connectors`, `read:connectors`, `update:connectors`, `delete:connectors`
- Controls: `create:controls`, `read:controls`, `update:controls`, `delete:controls`
- CCRs: `create:cloud_configuration`, `read:cloud_configuration`, `update:cloud_configuration`, `delete:cloud_configuration`
- Projects: `admin:projects`, `read:projects`, `read:users`

### Scope Minimization

Prefer scoped configurations over global:
- Use `scope_project_id` to limit rules to specific projects
- Use `scope_account_ids` to limit to specific cloud accounts
- Assign minimum necessary permissions to service accounts

## Standard Provider Setup

```hcl
# versions.tf
terraform {
  required_version = ">= 1.0"

  required_providers {
    wiz = {
      source = "tf.app.wiz.io/wizsec/wiz"
    }
  }
}

# providers.tf
provider "wiz" {
  # Authentication via environment variables:
  # export WIZ_CLIENT_ID="your-client-id"
  # export WIZ_CLIENT_SECRET="your-client-secret"

  # For FedRAMP tenants, uncomment:
  # environment = "fedramp"
}
```

## Common Patterns

### Cloud Configuration Rule (CCR)

```hcl
resource "wiz_cloud_configuration_rule" "example" {
  name                     = "Descriptive Rule Name"
  description              = "What this rule detects and why it matters"
  target_native_types      = ["bucket"]  # Resource type to evaluate
  severity                 = "HIGH"      # INFORMATIONAL, LOW, MEDIUM, HIGH, CRITICAL
  enabled                  = true
  function_as_control      = true        # Create Issues for findings
  remediation_instructions = "Step-by-step fix instructions"

  # Link to compliance frameworks
  security_sub_categories = ["wsct-id-xxx"]

  tags {
    key   = "owner"
    value = "security-team"
  }

  opa_policy = <<-EOT
    package wiz

    default result = "pass"

    result = "fail" {
      # Your Rego logic here
      input.someField == "bad-value"
    }
  EOT
}
```

### Custom Control (Graph-based)

```hcl
resource "wiz_custom_control" "example" {
  name                       = "Control Name"
  description                = "What this control detects"
  resolution_recommendation  = "How to remediate"
  severity                   = "HIGH"
  enabled                    = true

  query = jsonencode({
    "select": true,
    "type": ["VIRTUAL_MACHINE"],
    "relationships": [
      # Graph query relationships
    ]
  })

  scope_query = jsonencode({
    "type": ["VIRTUAL_MACHINE"]
  })
}
```

### AWS Connector

```hcl
resource "wiz_aws_connector" "example" {
  name = "AWS Account Name"

  auth_params {
    role_arn = "arn:aws:iam::123456789012:role/WizRole"
  }

  enabled             = true
  scan_interval_hours = 24

  tags {
    key   = "environment"
    value = "production"
  }
}
```

### Project with Resource Assignment

```hcl
resource "wiz_project" "example" {
  name        = "Project Name"
  description = "Project description"

  risk_profile {
    business_impact = "MBI"  # LBI, MBI, HBI
  }

  cloud_account_link {
    cloud_account_id = data.wiz_cloud_accounts.target.cloud_accounts[0].id
    shared           = false
  }
}
```

### Automation Rule (Slack Notification)

```hcl
resource "wiz_automation_rule" "slack_critical" {
  name         = "Critical Issues to Slack"
  description  = "Notify Slack on critical issues"
  enabled      = true
  trigger_type = ["CREATED"]

  filters = jsonencode({
    "severity": ["CRITICAL"]
  })

  actions {
    action_type    = "SLACK"
    integration_id = data.wiz_integrations.slack.integrations[0].id

    params {
      slack {
        channel = "#security-alerts"
      }
    }
  }
}
```

## Reference Documentation

For complete schemas, examples, and available values, read the appropriate reference file:

| Topic | Reference File | When to Read |
|-------|---------------|--------------|
| Provider setup, modules, overview | `references/wiz-terraform-provider-overview.md` | Initial setup, using CCR/Control/HCR/Project modules |
| Resources (35 total) | `references/wiz-terraform-resources.md` | Creating/modifying any Wiz resource |
| Data sources (22 total) | `references/wiz-terraform-data-sources.md` | Querying existing Wiz objects |

## Review Checklist

When reviewing Wiz Terraform/Tofu code, verify:

- [ ] No hardcoded credentials in provider configuration
- [ ] Environment variables used for `WIZ_CLIENT_ID` and `WIZ_CLIENT_SECRET`
- [ ] Required fields present for each resource
- [ ] Valid enum values (severity, trigger_type, action_type, etc.)
- [ ] Proper JSON encoding for `query`, `scope_query`, `filters` fields
- [ ] Tags follow organizational standards
- [ ] Resources scoped appropriately (not overly permissive)
- [ ] Service account permissions match resources being managed
- [ ] Rego policies in CCRs have correct package and result structure
- [ ] OVAL definitions in HCRs are valid XML

## Validation Commands

```bash
# Initialize and validate
tofu init
tofu validate

# Preview changes
tofu plan

# Apply (after review)
tofu apply
```
