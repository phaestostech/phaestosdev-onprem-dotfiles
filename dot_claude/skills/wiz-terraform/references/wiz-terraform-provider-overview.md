# Wiz Terraform Provider - Complete Documentation

## Overview

The Wiz Terraform Provider enables you to manage and configure resources within your Wiz environment using Terraform. This provider facilitates resource management automation, allowing you to define infrastructure as code and seamlessly integrate it into your existing Terraform workflows.

**Version:** 1.8.1154+ (January 2026)

**Popular use cases:**
- Managing custom policies: Controls, Cloud Configuration Rules, and Ignore Rules
- Wiz Projects and user management
- Integrations and automations  
- Cloud connector management (AWS, GCP, Azure, Kubernetes)
- Security frameworks and compliance

## Provider Configuration

### Prerequisites

- Access to Wiz with Write permissions on Settings > Service Accounts
- Terraform 0.14+ CLI installed locally
- Supported architectures:
  - Darwin amd64
  - Darwin arm64
  - Linux amd64
  - Linux arm64
  - Windows amd64
- Basic knowledge of Terraform language

### Setup Steps

#### Step 1: Create Wiz Service Account

1. Navigate to Settings > Access Management > Service Accounts in Wiz
2. Click "Add Service Account"
3. Select type: "Custom Integration (GraphQL API)"
4. Assign necessary permissions based on resources you want to manage

**Best practice:** Create separate service accounts for different resources rather than one common service account, unless they will be used in the same Terraform configuration file.

#### Step 2: Configure Provider Files

Create `versions.tf`:
```hcl
terraform {
  required_providers {
    wiz = {
      source = "tf.app.wiz.io/wizsec/wiz"
    }
  }
}
```

Create `providers.tf`:
```hcl
provider "wiz" {
  # Credentials can be set via environment variables:
  # WIZ_CLIENT_ID and WIZ_CLIENT_SECRET
}
```

For FedRAMP tenants:
```hcl
provider "wiz" {
  environment = "fedramp"
}
```

#### Step 3: Set Credentials

**Recommended: Environment Variables**
```bash
export WIZ_CLIENT_ID="<your-client-id>"
export WIZ_CLIENT_SECRET="<your-client-secret>"
```

**Not Recommended: Hard-coding**
```hcl
provider "wiz" {
  client_id = "<your-client-id>"
  secret    = "<your-client-secret>"
}
```

⚠️ **Warning:** Avoid hard-coding credentials to prevent credential leaks.

### Provider Schema

#### Required Arguments

- **`client_id`** (String) - Service account Client ID for the Wiz portal
  - Can use environment variable: `WIZ_CLIENT_ID`
  
- **`secret`** (String, Sensitive) - Service account Secret to connect to Wiz
  - Can use environment variable: `WIZ_CLIENT_SECRET`

#### Optional Arguments

- **`environment`** (String) - Type of Wiz environment
  - Default: `prod`
  - Possible values: `prod`, `gov`, `fedramp`
  - Can use environment variable: `WIZ_ENVIRONMENT`
  
- **`disable_entity_validations`** (Boolean) - Disable validations for referential Wiz ID inputs
  - Default: `false`
  - Can use environment variable: `WIZ_DISABLE_ENTITY_VALIDATIONS`
  - **Warning:** Disabling validations may allow insertion of non-existent IDs, leading to errors

### Example Usage
```hcl
terraform {
  required_providers {
    wiz = {
      source = "tf.app.wiz.io/wizsec/wiz"
    }
  }
}

provider "wiz" {
  client_id = "your-client-id"
  secret    = "your-secret"
}

# FedRAMP environment
provider "wiz" {
  environment = "fedramp"
}
```

## Terraform Modules

The Wiz Terraform Provider includes 4 pre-built modules for common workflows:

### 1. Custom Cloud Configuration Rules (CCRs) Module

Manage custom Cloud Configuration Rules as code using GitOps workflows.

**Module Source:**
```hcl
module "custom-ccr" {
  source   = "https://downloads.wiz.io/customer-files/terraform/terraform-wiz-custom-ccr.zip//terraform-wiz-custom-ccr"
  base_dir = "./Cloud_Configuration_Rules"
}
```

**Purpose:** Create and manage CCRs from a Git repository structure with metadata and Rego policies.

**Required Folder Structure:**
```
Cloud_Configuration_Rules/
├── CCR-001/
│   ├── metadata/
│   │   ├── metadata.json      # Required
│   │   ├── description.md     # Optional
│   │   └── remediation.md     # Optional
│   └── cloud/                 # Matcher directory
│       └── query.rego         # Required
├── CCR-002/
│   ├── metadata/
│   │   └── metadata.json
│   └── terraform/             # IaC Matcher
│       └── policy.rego
```

**Supported Matchers:**
- `cloud` - Cloud resources
- `terraform` - Terraform IaC
- `cloud_formation` - CloudFormation IaC
- `kubernetes` - Kubernetes manifests
- `docker_file` - Dockerfiles
- `azure_resource_manager` - ARM templates
- `admission_controller` - Kubernetes admission controller

**Required Permissions:**
- `create:cloud_configuration`
- `read:cloud_configuration`
- `update:cloud_configuration`
- `delete:cloud_configuration`

**metadata.json Fields:**

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `name` | String | Yes | - | Rule name |
| `target_native_types` | [String] | Yes (for cloud) | - | Resource types to match (e.g., "bucket", "ec2") |
| `severity` | String | No | MEDIUM | CRITICAL, HIGH, MEDIUM, LOW, INFORMATIONAL |
| `enabled` | Boolean | No | true | Whether rule is enabled |
| `function_as_control` | Boolean | No | false | Create Issues for findings |
| `security_sub_categories` | [String] | No | [] | Link to compliance frameworks |
| `scope_account_ids` | [String] | No | [] | Scope to specific subscriptions |
| `scope_project_id` | String | No | null | Scope to specific project |
| `tags` | [{key, value}] | No | [] | Tags for the rule |
| `admission_controller_operation` | [String] | No | [CREATE, UPDATE] | Operations for admission controller |

**metadata.json Example:**
```json
{
  "name": "S3 Bucket logging should be enabled",
  "enabled": true,
  "function_as_control": true,
  "scope_account_ids": [],
  "security_sub_categories": ["wsct-id-824"],
  "severity": "HIGH",
  "target_native_types": ["bucket"],
  "tags": [
    { "key": "owner", "value": "admin" },
    { "key": "team", "value": "CCOE" }
  ]
}
```

**Deployment:**
```bash
terraform init
terraform plan   # Preview changes
terraform apply  # Deploy CCRs
```

**Modify Built-in Rules:** (Version ≥1.8.1104)
```hcl
import {
  id = "2ce49437-0647-4c45-8f46-a76d0aa149eb"
  to = wiz_cloud_configuration_rule.sub_074
}
```

Then run:
```bash
terraform plan -generate-config-out=generated.tf
```

---

### 2. Custom Controls Module

Define and manage custom Graph Controls via Infrastructure as Code.

**Module Source:**
```hcl
module "custom-control" {
  source   = "https://wizio-public.s3.us-east-2.amazonaws.com/deployment-v2/terraform/terraform-wiz-custom-control.zip//terraform-wiz-custom-control"
  base_dir = "./Controls"
}
```

**Purpose:** Create Controls from Security Graph queries with version control and CI/CD automation.

**Required Folder Structure:**
```
Controls/
├── Control-001/
│   ├── metadata/
│   │   ├── metadata.json       # Required
│   │   ├── description.md      # Optional
│   │   └── remediation.md      # Optional
│   └── query/
│       ├── query.json          # Required - Graph query
│       └── scope_query.json    # Required - Scope query
```

**Required Permissions:**
- `create:controls`
- `read:controls`
- `update:controls`
- `delete:controls`

**Required Files per Control:**
1. **`metadata.json`** - Control configuration
2. **`query.json`** - Security Graph query to execute
3. **`scope_query.json`** - Scope query defining applicable resources

**metadata.json Fields:**

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `name` | String | Yes | - | Control name |
| `severity` | String | Yes | - | CRITICAL, HIGH, MEDIUM, LOW, INFORMATIONAL |
| `enabled` | Boolean | No | true | Mark control as enabled |
| `project_id` | String | No | null | Scope to specific project |
| `security_sub_categories` | [String] | No | [] | Link to compliance frameworks |
| `tags` | [{key, value}] | No | [] | Tags for the control |

**metadata.json Example:**
```json
{
  "name": "Publicly Exposed S3 Bucket with Sensitive Data",
  "enabled": true,
  "project_id": "",
  "security_sub_categories": ["wsct-id-824"],
  "severity": "HIGH",
  "tags": [
    { "key": "owner", "value": "admin" },
    { "key": "team", "value": "CCOE" }
  ]
}
```

**Example Control (Terraform):**
```hcl
resource "wiz_custom_control" "public_vms_no_edr" {
  name        = "Public VMs with exploitable vulnerabilities without EDR"
  enabled     = true
  severity    = "CRITICAL"
  description = "VMs without EDR agents that are internet facing with exploitable vulns"
  resolution_recommendation = "Cut off internet access, install EDR, patch vulns"
  
  query = jsonencode({
    "relationships": [
      {
        "negate": true,
        "type": [{"type": "RUNS"}],
        "with": {
          "relationships": [
            {
              "type": [{"type": "HAS_TECH"}],
              "with": {
                "type": ["TECHNOLOGY"],
                "where": {
                  "categories": {
                    "EQUALS": ["Endpoint Protection Platform"]
                  }
                }
              }
            }
          ],
          "type": ["HOSTED_TECHNOLOGY"]
        }
      },
      {
        "type": [{"type": "SERVES"}],
        "with": {
          "select": true,
          "type": ["ENDPOINT"]
        }
      },
      {
        "type": [{"reverse": true, "type": "ALERTED_ON"}],
        "with": {
          "blockExpanded": true,
          "blockName": "Has vulnerabilities",
          "relationships": [
            {
              "type": [{"reverse": true, "type": "CAUSES"}],
              "with": {
                "select": true,
                "type": ["VULNERABILITY"],
                "where": {
                  "effectiveAttackVector": {"EQUALS": ["Network"]},
                  "hasExploit": {"EQUALS": true}
                }
              }
            }
          ],
          "select": true,
          "type": ["SECURITY_TOOL_FINDING"],
          "where": {
            "severity": {
              "EQUALS": ["VulnerabilitySeverityCritical", "VulnerabilitySeverityHigh"]
            }
          }
        }
      }
    ],
    "select": true,
    "type": ["VIRTUAL_MACHINE"]
  })
  
  scope_query = jsonencode({
    "type": ["VIRTUAL_MACHINE"]
  })
}
```

---

### 3. Custom Host Configuration Rules (HCRs) Module

Manage custom Host Configuration Rules using OVAL (Open Vulnerability and Assessment Language).

**Module Source:**
```hcl
module "custom-hcr" {
  source   = "https://downloads.wiz.io/customer-files/terraform/terraform-wiz-custom-hcr.zip//terraform-wiz-custom-hcr"
  base_dir = "./Host_Configuration_Rules"
}
```

**Purpose:** Create HCRs from a Git repository structure using OVAL XML for host compliance checks.

**Required Folder Structure:**
```
Host_Configuration_Rules/
├── HCR-001/
│   ├── metadata/
│   │   ├── metadata.json       # Required
│   │   ├── description.md      # Optional
│   │   └── remediation.md      # Optional
│   └── oval/                   # Required
│       └── oval.xml            # Required - OVAL definition
```

**Required Permissions:**
- `create:host_configuration`
- `read:host_configuration`
- `update:host_configuration`
- `delete:host_configuration`

**metadata.json Fields:**

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `name` | String | Yes | - | Rule name |
| `enabled` | Boolean | Yes | - | Enable the rule |
| `severity` | String | No | MEDIUM | CRITICAL, HIGH, MEDIUM, LOW, INFORMATIONAL |
| `target_operating_systems` | [String] | No | [] | OS identifiers (e.g., "2681##" for Windows) |
| `target_technologies` | [String] | No | [] | Technology identifiers |
| `scope_project_id` | String | No | null | Scope to specific project |
| `security_sub_categories` | [String] | No | [] | Link to compliance frameworks |
| `workload_scanner_description` | String | No | null | Description for workload scanner |

**metadata.json Example:**
```json
{
  "name": "Wiz HCR-001 TF Test",
  "enabled": true,
  "scope_project_id": "*",
  "security_sub_categories": ["wsct-id-9257"],
  "severity": "HIGH",
  "target_operating_systems": ["2681##"],
  "target_technologies": ["2024##", "2080##"],
  "workload_scanner_description": "Workload scanner description"
}
```

**OVAL XML Example:**
```xml
<definition xmlns="https://wiz.io/XMLSchema/direct-schema">
  <criteria operator="or">
    <criterion>
      <textfilecontent54_test check_existence="at_least_one_exists" check="all"
        comment="Ensure at least one file named /dir/file.config exists and matches pattern">
        <textfilecontent54_object>
          <filepath>/dir/file.config</filepath>
          <pattern operation="pattern match">.*internal-test-pattern=value.*</pattern>
          <instance datatype="int">1</instance>
        </textfilecontent54_object>
      </textfilecontent54_test>
    </criterion>
  </criteria>
</definition>
```

**Each HCR must have exactly one OVAL matcher.**

**Modify Built-in Rules:** (Version ≥1.8.1104)
```hcl
import {
  id = "4dc38fe6-17c0-416e-b0e6-5af9807c8c73"
  to = wiz_host_configuration_rule.DebianFamilyLinux-CIS-V1_0_0-5_1_4
}
```

---

### 4. Project Import Module

Automate creation and configuration of Wiz Projects from external data sources (CMDBs, asset inventories).

**Module Source:**
```hcl
module "my_applications" {
  source      = "https://downloads.wiz.io/customer-files/terraform/terraform-wiz-projects-import.zip//terraform-wiz-projects-import"
  data_file   = "data/applications.json"
  source_name = "BizStruct"
  category    = "Application"
}
```

**Purpose:** Import projects from CSV/JSON files with automatic resource assignment, folder organization, and SAML mappings.

**Required Permissions:**
- `admin:projects`
- `read:projects`
- `read:users`
- `admin:identity_providers` (only if using SAML auto-mapping)

**Configuration Parameters:**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `preset` | String | No | Generic | Configuration profile: "Generic" or "ServiceNow" |
| `data_file` | String | One of data_file/data | - | Path to CSV/JSON file |
| `data` | [Object] | One of data_file/data | - | List of project objects |
| `source_name` | String | No | - | Descriptive name for data source |
| `identifier_key` | String | No | id | CSV column for unique project ID (Generic) |
| `name_key` | String | No | name | CSV column for project name (Generic) |
| `description_key` | String | No | description | CSV column for project description (Generic) |
| `category` | String | No | - | Category for created projects |
| `tag_keys` | [String] | No | All columns | CSV columns to use as project tags |
| `resource_assignment_rules` | [Object] | No | [] | Rules for automatic resource assignment |
| `folder_structure_config` | Object | No | - | Configuration for folder organization |
| `saml_config` | Object | No | - | Auto-create SAML group mappings |
| `advanced_properties_config` | [Object] | No | [] | Map CSV fields to project properties |
| `case_insensitive_resource_assignment_rules` | Boolean | No | false | Case sensitivity for resource rules |

**Supported Presets:**

1. **Generic** (Default) - Flexible for any CSV/JSON structure
   - Must specify `identifier_key`, `name_key`, `description_key`
   
2. **ServiceNow** - Optimized for ServiceNow CMDB exports
   - Automatically maps: `sys_id` → identifier, `name` → name, `short_description` → description

**ServiceNow Preset Example:**
```hcl
module "servicenow_apps" {
  source      = "https://downloads.wiz.io/customer-files/terraform/terraform-wiz-projects-import.zip//terraform-wiz-projects-import"
  preset      = "ServiceNow"
  data_file   = "servicenow_export.csv"
  source_name = "ServiceNow CMDB"
  category    = "Application"
}
```

**Resource Assignment Rules Example:**
```hcl
module "my_applications" {
  source      = "..."
  data_file   = "data/applications.json"
  source_name = "BizStruct"
  category    = "Application"
  
  resource_assignment_rules = [
    {
      value_source_key = "number"  # CSV column name
      key             = "AppID"     # Tag key to filter by
    }
  ]
}
```

This creates projects scoped to resources tagged with `AppID:<number>`.

**Folder Organization Example:**
```hcl
module "my_teams" {
  source      = "..."
  data_file   = "data/teams.json"
  source_name = "BizStruct"
  category    = "Team"
  
  folder_structure_config = {
    local = {
      source_key = "portfolio"  # CSV column
      category   = "Portfolio"
    }
  }
}
```

Creates a folder for each unique value in the `portfolio` column.

**SAML Auto-Mapping Example:**
```hcl
module "teams_with_saml" {
  source      = "..."
  data_file   = "teams.csv"
  source_name = "BizStruct"
  category    = "Team"
  
  saml_config = {
    idp_id    = "my-idp-id"      # Wiz IDP ID
    group_key = "saml_group"     # CSV column with group names
    role      = "PROJECT_READER" # Role to assign
  }
}
```

**SAML Mapping Behavior:**
- If CSV row has a value in `saml_group` column → SAML mapping created
- If CSV row has empty/null `saml_group` → Project created, no SAML mapping
- Roles: `PROJECT_OWNER`, `PROJECT_CONTRIBUTOR`, `PROJECT_READER`

**Field Mapping Example:**
```hcl
module "my_applications" {
  source      = "..."
  data_file   = "data/applications.csv"
  source_name = "BizStruct"
  category    = "Application"
  
  advanced_properties_config = [
    {
      source_key   = "business_criticality"
      dest_property = "risk_profile.business_impact"
      value_mapping = {
        "low"    = "LBI"
        "medium" = "MBI"
        "high"   = "HBI"
      }
    },
    {
      source_key   = "internet_facing"
      dest_property = "risk_profile.is_internet_facing"
      value_mapping = {
        "Yes" = "YES"
        "No"  = "NO"
      }
    }
  ]
}
```

**Sample Input Files:**

JSON format:
```json
[
  {
    "id": 1,
    "name": "MyApp",
    "description": "This is my application",
    "number": "APP123456"
  },
  {
    "id": 2,
    "name": "AnotherApp",
    "description": "Another application",
    "number": "APP234567"
  }
]
```

CSV format:
```csv
id,name,description,saml_group
PROJ-01,Frontend Team,Frontend dev team,frontend-dev-group
PROJ-02,Backend Team,Backend dev team,
PROJ-03,API Gateway,API team,api-dev-group
```

**Multi-Level Project Structure:**

Yes, multiple folder levels are supported using related table structures:
```hcl
# Step 1: Create Division folders
module "my_divisions" {
  source      = "..."
  data_file   = "data/divisions.json"
  source_name = "BizStruct"
  is_folder   = true
  category    = "Division"
}

# Step 2: Create Team projects under divisions
module "my_teams" {
  source      = "..."
  data_file   = "data/teams.json"
  source_name = "BizStruct"
  category    = "Team"
  
  folder_structure_config = {
    reference = {
      source_key = "parent_id"                    # CSV column referencing division
      id_mapping = module.my_divisions.id_mapping # Pass division IDs
    }
  }
}
```

---

## Summary

**Complete Wiz Terraform Provider Includes:**

- **Provider Configuration** - Setup, authentication, environments (prod/gov/fedramp)
- **22 Data Sources** - Query existing Wiz components
- **35 Resources** - Create and manage Wiz configurations
- **4 Modules** - Pre-built workflows:
  1. Custom Cloud Configuration Rules (CCRs) - GitOps for cloud policies
  2. Custom Controls - Graph-based security controls
  3. Custom Host Configuration Rules (HCRs) - OVAL-based host compliance
  4. Project Import - Bulk project creation from CMDBs

**Permissions Model:** Fine-grained permissions per resource (create, read, update, delete)

**Import Support:** Most resources can be imported from existing Wiz configurations

**Best Practices:**
1. Use separate service accounts for different Terraform configurations
2. Store credentials in environment variables
3. Version pin the provider
4. Test in non-production first
5. Use `terraform plan` before applying
6. Leverage modules for GitOps workflows
7. Import existing resources before managing them

**Next Steps:**
- See `wiz-terraform-data-sources.md` for complete data source documentation
- See `wiz-terraform-resources.md` for complete resource documentation

**Release Notes:** https://docs.wiz.io/docs/wiz-terraform-provider#release-notes
