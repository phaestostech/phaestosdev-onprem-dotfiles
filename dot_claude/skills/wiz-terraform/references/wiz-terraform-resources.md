# Wiz Terraform Provider - Resources Reference

Complete reference for all 35 Wiz Terraform resources. Resources allow you to create, modify, and manage components within Wiz.

## Table of Contents

### Connectors (5)
1. [wiz_aws_connector](#wiz_aws_connector)
2. [wiz_gcp_connector](#wiz_gcp_connector)
3. [wiz_kubernetes_connector](#wiz_kubernetes_connector)
4. [wiz_github_connector](#wiz_github_connector)
5. [wiz_gitlab_connector](#wiz_gitlab_connector)

### Security & Compliance (10)
6. [wiz_cloud_configuration_rule](#wiz_cloud_configuration_rule)
7. [wiz_host_configuration_rule](#wiz_host_configuration_rule)
8. [wiz_ignore_rule](#wiz_ignore_rule)
9. [wiz_threat_detection_rule](#wiz_threat_detection_rule)
10. [wiz_data_classification_rule](#wiz_data_classification_rule)
11. [wiz_cicd_scan_policy](#wiz_cicd_scan_policy)
12. [wiz_runtime_response_policy](#wiz_runtime_response_policy)
13. [wiz_control](#wiz_control)
14. [wiz_custom_control](#wiz_custom_control)
15. [wiz_security_framework](#wiz_security_framework)

### Automation & Response (4)
16. [wiz_automation_rule](#wiz_automation_rule)
17. [wiz_action_template](#wiz_action_template)
18. [wiz_response_action_catalog_item](#wiz_response_action_catalog_item)
19. [wiz_remediation_and_response_deployment_v2](#wiz_remediation_and_response_deployment_v2)

### Access & Identity (8)
20. [wiz_project](#wiz_project)
21. [wiz_user](#wiz_user)
22. [wiz_user_role](#wiz_user_role)
23. [wiz_service_account](#wiz_service_account)
24. [wiz_saml_idp](#wiz_saml_idp)
25. [wiz_saml_group_mapping](#wiz_saml_group_mapping)
26. [wiz_saml_lens_mapping](#wiz_saml_lens_mapping)
27. [wiz_integration](#wiz_integration)

### Scanning & Detection (4)
28. [wiz_scanner_exclusions](#wiz_scanner_exclusions)
29. [wiz_scanner_nonos_disk_scan](#wiz_scanner_nonos_disk_scan)
30. [wiz_scanner_custom_detection_custom_ip_ranges](#wiz_scanner_custom_detection_custom_ip_ranges)
31. [wiz_image_integrity_validator](#wiz_image_integrity_validator)

### Other Resources (4)
32. [wiz_custom_rego_package](#wiz_custom_rego_package)
33. [wiz_report](#wiz_report)
34. [wiz_saved_graph_query](#wiz_saved_graph_query)
35. [wiz_resource_tagging_rule](#wiz_resource_tagging_rule)
36. [wiz_file_upload](#wiz_file_upload)

---

## Connectors

### wiz_aws_connector

Manage AWS cloud connectors.

**Available from:** Version >= 1.3.0

**Required Permissions:** `create:connectors`, `read:connectors`, `update:connectors`, `delete:connectors`

#### Example Usage
```hcl
resource "wiz_aws_connector" "production" {
  name = "Production AWS Account"
  
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

#### Schema

**Required:**
- `name` (String) - Connector name
- `auth_params` (Block Set, Min: 1, Max: 1) - Authentication parameters
  - `role_arn` (String, Required) - AWS IAM role ARN for Wiz to assume

**Optional:**
- `enabled` (Boolean) - Enable/disable connector (default: true)
- `scan_interval_hours` (Number) - Scan interval in hours (default: 24)
- `disk_analyzer_inflight_disabled` (Boolean) - Disable disk analyzer in-flight (default: false)
- `extra_config` (String) - Additional JSON configuration
- `tags` (Block Set, Max: 10) - Key/value tags
  - `key` (String, Required)
  - `value` (String, Optional)

**Read-Only:**
- `id` (String) - Connector ID
- `created_at` (String) - Creation timestamp
- `status` (String) - Connector status

---

### wiz_gcp_connector

Manage GCP cloud connectors.

**Required Permissions:** `create:connectors`, `read:connectors`, `update:connectors`, `delete:connectors`

#### Example Usage
```hcl
resource "wiz_gcp_connector" "production" {
  name = "Production GCP Project"
  
  auth_params {
    service_account_email = "wiz-scanner@project-id.iam.gserviceaccount.com"
    project_id           = "my-gcp-project"
  }
  
  enabled             = true
  scan_interval_hours = 24
}
```

#### Schema

**Required:**
- `name` (String) - Connector name
- `auth_params` (Block Set, Min: 1, Max: 1)
  - `service_account_email` (String, Required) - GCP service account email
  - `project_id` (String, Required) - GCP project ID

**Optional:**
- `enabled` (Boolean) - Enable/disable connector
- `scan_interval_hours` (Number) - Scan interval in hours
- `disk_analyzer_inflight_disabled` (Boolean)
- `extra_config` (String)
- `tags` (Block Set, Max: 10)

---

### wiz_kubernetes_connector

Manage Kubernetes cluster connectors.

**Required Permissions:** `create:connectors`, `read:connectors`, `update:connectors`, `delete:connectors`

#### Example Usage
```hcl
resource "wiz_kubernetes_connector" "production" {
  name         = "Production K8s Cluster"
  cluster_name = "prod-cluster-01"
  
  auth_params {
    server_url = "https://kubernetes.default.svc"
  }
  
  enabled = true
}
```

#### Schema

**Required:**
- `name` (String) - Connector name
- `cluster_name` (String) - Kubernetes cluster name
- `auth_params` (Block Set, Min: 1, Max: 1)
  - `server_url` (String, Required) - Kubernetes API server URL

**Optional:**
- `enabled` (Boolean)
- `tags` (Block Set, Max: 10)

---

### wiz_github_connector

Manage GitHub repository connectors.

**Required Permissions:** `create:connectors`, `read:connectors`, `update:connectors`, `delete:connectors`

#### Example Usage
```hcl
resource "wiz_github_connector" "org" {
  name = "GitHub Organization"
  
  auth_params {
    token = var.github_token
    url   = "https://api.github.com"
  }
  
  enabled = true
}
```

#### Schema

**Required:**
- `name` (String) - Connector name
- `auth_params` (Block Set, Min: 1, Max: 1)
  - `token` (String, Required, Sensitive) - GitHub personal access token
  - `url` (String, Required) - GitHub API URL

**Optional:**
- `enabled` (Boolean)
- `scan_pull_requests` (Boolean) - Enable PR scanning
- `tags` (Block Set, Max: 10)

---

### wiz_gitlab_connector

Manage GitLab repository connectors.

**Required Permissions:** `create:connectors`, `read:connectors`, `update:connectors`, `delete:connectors`

#### Example Usage
```hcl
resource "wiz_gitlab_connector" "org" {
  name = "GitLab Organization"
  
  auth_params {
    token = var.gitlab_token
    url   = "https://gitlab.com/api/v4"
  }
  
  enabled = true
}
```

#### Schema

Similar to `wiz_github_connector` with GitLab-specific parameters.

---

## Security & Compliance

### wiz_cloud_configuration_rule

Manage custom Cloud Configuration Rules.

**Required Permissions:** `create:cloud_configuration`, `read:cloud_configuration`, `update:cloud_configuration`, `delete:cloud_configuration`

#### Important Notes

**Built-in Policy Limitation:** Only these fields can be updated for built-in rules:
- `enabled`
- `function_as_control`
- `scope_account_ids`
- `name`
- `remediation_instructions`
- `tags`
- `description`
- `severity`

#### Example Usage
```hcl
# Cloud Matcher type rule
resource "wiz_cloud_configuration_rule" "s3_public_read" {
  name                     = "S3 Bucket Public Read Access"
  target_native_types      = ["bucket"]
  description              = "Detects S3 buckets with public read access"
  remediation_instructions = "Disable public access on the S3 bucket"
  severity                 = "HIGH"
  security_sub_categories  = ["276ef87c-7375-4565-a9d0-d7f3d5e95658"]
  
  tags {
    key   = "env"
    value = "production"
  }
  
  opa_policy = <<EOT
package wiz

default result = "pass"

# Fail if the bucket allows public read access
result = "fail" {
  input.publicAccessBlock.blockPublicAcls == false
}
EOT
}

# IaC Matcher types rule
resource "wiz_cloud_configuration_rule" "iac_rule" {
  name        = "IaC Multi-Matcher Rule"
  description = "Rule with multiple IaC matchers"
  severity    = "HIGH"
  
  iac_matchers {
    rego_code = <<EOT
package wiz

default result = "pass"

result = "fail" {
  input.MapPublicIpOnLaunch == true
}
EOT
    type                     = "CLOUD_FORMATION"
    remediation_instructions = "Set MapPublicIpOnLaunch to false"
  }
  
  iac_matchers {
    rego_code = <<EOT
package wiz
import data.generic.terraform as terraLib

WizPolicy[result] {
  document := input.document[i]
  resource := document.resource.aws_subnet[name]
  
  resource.map_public_ip_on_launch == true
  
  result := {
    "documentId": document.id,
    "resourceName": terraLib.get_resource_name(resource, name),
    "searchKey": sprintf("resource.aws_subnet[%s].map_public_ip_on_launch", [name]),
    "issueType": "IncorrectValue",
    "keyExpectedValue": "map_public_ip_on_launch should be false",
    "keyActualValue": "map_public_ip_on_launch is true",
    "remediation": json.marshal({
      "before": "true",
      "after": "false"
    }),
    "remediationType": "update",
    "resourceTags": object.get(resource, "tags", {})
  }
}
EOT
    type                     = "TERRAFORM"
    remediation_instructions = "Set map_public_ip_on_launch to false"
  }
}
```

#### Schema

**Required:**
- `name` (String) - Rule name

**Optional:**
- `description` (String) - Rule description
- `enabled` (Boolean) - Enable/disable rule (default: true)
- `function_as_control` (Boolean) - Create Issues for findings (default: false)
- `iac_matchers` (Block Set) - OPA rego policies for IaC rules
  - `rego_code` (String, Required) - Rego query language code
  - `type` (String, Required) - Matcher type
    - Possible values: `TERRAFORM`, `CLOUD_FORMATION`, `KUBERNETES`, `AZURE_RESOURCE_MANAGER`, `DOCKER_FILE`, `ADMISSION_CONTROLLER`, `ANSIBLE`, `GOOGLE_CLOUD_DEPLOYMENT_MANAGER`, `GITHUB_ACTIONS`, `BICEP`
  - `remediation_instructions` (String, Optional) - How to fix the issue
  - `parameters` (Block Set, Max: 1, Optional) - For ADMISSION_CONTROLLER only
    - `admission_controller` (Block Set, Max: 1)
      - `operation` (Set of String) - Operations: `CREATE`, `UPDATE`, `DELETE`, `CONNECT`
- `opa_policy` (String) - OPA rego policy for Cloud matcher (required if using cloud matcher)
- `remediation_instructions` (String) - How to fix (Cloud matcher)
- `scope_account_ids` (Set of String) - Limit to specific subscriptions/accounts
- `scope_project_id` (String) - Limit to specific project
- `security_sub_categories` (Set of String) - Link to compliance framework sub-categories
- `severity` (String) - Finding severity
  - Possible values: `INFORMATIONAL`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`
- `tags` (Block Set, Max: 10) - Key/value tags
  - `key` (String, Required)
  - `value` (String, Optional)
- `target_native_types` (Set of String) - Cloud native types to evaluate (required for cloud matcher, e.g., "ec2", "bucket")
  - See [supported resources for Cloud Configuration Rules](https://docs.wiz.io/docs/supported-resources-for-cloud-configuration-rules)

**Read-Only:**
- `built_in` (Boolean) - Is built-in by Wiz
- `id` (String) - Resource ID
- `risks` (Set of String) - Associated security risks
- `security_frameworks_managed_externally` (Set of String) - External framework associations
- `short_id` (String) - Short unique identifier

---

### wiz_host_configuration_rule

Manage custom Host Configuration Rules using OVAL.

**Required Permissions:** `read:host_configuration`, `update:host_configuration`, `create:host_configuration`, `delete:host_configuration`

#### Example Usage
```hcl
resource "wiz_host_configuration_rule" "file_check" {
  name                       = "Check Config File"
  target_operating_systems   = ["2681##"] # Windows
  severity                   = "HIGH"
  
  matchers {
    workload_scanner {
      enabled = true
      
      oval_definition = <<EOT
<definition xmlns="https://wiz.io/XMLSchema/direct-schema">
  <criteria operator="or">
    <criterion>
      <textfilecontent54_test check_existence="at_least_one_exists" check="all"
        comment="Ensure config file exists with correct pattern">
        <textfilecontent54_object>
          <filepath>/etc/app/config.ini</filepath>
          <pattern operation="pattern match">.*secure_mode=enabled.*</pattern>
          <instance datatype="int">1</instance>
        </textfilecontent54_object>
      </textfilecontent54_test>
    </criterion>
  </criteria>
</definition>
EOT
    }
  }
}
```

#### Schema

**Required:**
- `name` (String) - Rule name
- `target_operating_systems` (Set of String) - OS identifiers
- `matchers` (Block Set, Min: 1, Max: 1)
  - `workload_scanner` (Block Set, Min: 1, Max: 1)
    - `enabled` (Boolean, Required) - Enable the matcher
    - `oval_definition` (String, Required) - OVAL XML definition

**Optional:**
- `description` (String)
- `enabled` (Boolean) - Enable rule (default: true)
- `remediation_instructions` (String)
- `scope_project_id` (String)
- `security_sub_categories` (Set of String)
- `severity` (String) - INFORMATIONAL, LOW, MEDIUM, HIGH, CRITICAL
- `tags` (Block Set, Max: 10)
- `target_technologies` (Set of String) - Technology identifiers

**Read-Only:**
- `built_in` (Boolean)
- `id` (String)
- `short_id` (String)

---

### wiz_ignore_rule

Manage ignore rules to suppress findings.

**Required Permissions:** `create:ignore_rules`, `read:ignore_rules`, `update:ignore_rules`, `delete:ignore_rules`

**Additional Validation Permissions:**
- `read:cloud_configuration`
- `read:host_configuration`
- `read:cloud_event_rules`
- `read:vulnerabilities`
- `read:data_classifiers`

#### Example Usage
```hcl
resource "wiz_ignore_rule" "dev_environment" {
  name        = "Ignore Dev Environment Issues"
  description = "Suppress findings in development environments"
  enabled     = true
  
  scope {
    cloud_accounts = ["dev-account-id"]
    projects       = ["dev-project-id"]
  }
  
  target_policies {
    policy_type = "CLOUD_CONFIGURATION_RULE"
    policy_ids  = ["ccr-id-1", "ccr-id-2"]
  }
  
  expiration_date = "2025-12-31T23:59:59Z"
}
```

#### Schema

**Required:**
- `name` (String) - Rule name
- `scope` (Block Set, Min: 1, Max: 1) - Where to apply the rule
  - `cloud_accounts` (Set of String, Optional) - Cloud account IDs
  - `projects` (Set of String, Optional) - Project IDs
  - `resource_tags` (Block Set, Optional) - Filter by resource tags
    - `key` (String, Required)
    - `value` (String, Optional)
- `target_policies` (Block Set, Min: 1) - Policies to ignore
  - `policy_type` (String, Required) - Type of policy
    - Possible values: `CLOUD_CONFIGURATION_RULE`, `HOST_CONFIGURATION_RULE`, `VULNERABILITY`, `THREAT_DETECTION_RULE`, `DATA_CLASSIFICATION_RULE`
  - `policy_ids` (Set of String, Required) - Specific policy IDs

**Optional:**
- `description` (String) - Rule description
- `enabled` (Boolean) - Enable/disable (default: true)
- `expiration_date` (String) - When rule expires (ISO 8601 format)
- `note` (String) - Additional notes

**Read-Only:**
- `id` (String) - Resource ID
- `created_at` (String)
- `updated_at` (String)

---

### wiz_threat_detection_rule

Manage threat detection rules.

**Required Permissions:** `read:security_frameworks`, `read:cloud_events_cloud`, `read:cloud_event_rules`, `update:cloud_event_rules`, `create:cloud_event_rules`, `delete:cloud_event_rules`, `read:cloud_events_sensor`, `read:security_scans`

#### Schema

**Required:**
- `name` (String) - Rule name
- `type` (String) - Detection type
- `enabled` (Boolean) - Enable rule

**Optional:**
- `description` (String)
- `severity` (String) - INFORMATIONAL, LOW, MEDIUM, HIGH, CRITICAL
- `filters` (String) - JSON filters for detection
- `security_sub_categories` (Set of String)

**Read-Only:**
- `id` (String)
- `created_at` (String)

---

### wiz_data_classification_rule

Manage data classification rules.

**Required Permissions:** `create:data_classifiers`, `read:data_classifiers`, `update:data_classifiers`, `delete:data_classifiers`

#### Schema

**Required:**
- `name` (String) - Rule name
- `pattern` (String) - Regex pattern to match

**Optional:**
- `enabled` (Boolean) - Enable rule (default: true)
- `description` (String)
- `severity` (String)
- `country_codes` (Set of String) - Associated country codes

**Read-Only:**
- `id` (String)

---

### wiz_cicd_scan_policy

Manage CI/CD scan policies.

**Required Permissions:** `create:scan_policies`, `read:scan_policies`, `update:scan_policies`, `delete:scan_policies`

#### Example Usage
```hcl
resource "wiz_cicd_scan_policy" "strict_policy" {
  name        = "Strict Security Policy"
  description = "Fail builds on critical findings"
  enabled     = true
  
  fail_build_on {
    iac_critical = true
    iac_high     = true
    secrets      = true
  }
  
  scope {
    repositories = ["repo-id-1", "repo-id-2"]
  }
}
```

#### Schema

**Required:**
- `name` (String) - Policy name

**Optional:**
- `description` (String)
- `enabled` (Boolean) - Enable policy (default: true)
- `fail_build_on` (Block Set, Max: 1) - Conditions to fail builds
  - `iac_critical` (Boolean) - Fail on critical IaC issues
  - `iac_high` (Boolean) - Fail on high IaC issues
  - `iac_medium` (Boolean) - Fail on medium IaC issues
  - `secrets` (Boolean) - Fail on detected secrets
  - `vulnerabilities_critical` (Boolean) - Fail on critical vulnerabilities
  - `vulnerabilities_high` (Boolean) - Fail on high vulnerabilities
- `scope` (Block Set, Max: 1) - Where to apply policy
  - `repositories` (Set of String) - Repository IDs
  - `projects` (Set of String) - Project IDs

**Read-Only:**
- `id` (String)

---

### wiz_runtime_response_policy

Manage runtime response policies for threat detection.

**Required Permissions:** `create:runtime_response_policies`, `read:runtime_response_policies`, `update:runtime_response_policies`, `delete:runtime_response_policies`, `read:cloud_events_sensor`

#### Schema

**Required:**
- `name` (String) - Policy name
- `enabled` (Boolean) - Enable policy

**Optional:**
- `description` (String)
- `actions` (Block Set) - Actions to take on detection
- `filters` (String) - JSON filters

**Read-Only:**
- `id` (String)

---

### wiz_control

Manage security controls.

**Required Permissions:** `create:controls`, `read:controls`, `update:controls`, `delete:controls`

**Note:** For custom graph-based controls, use `wiz_custom_control` instead.

#### Schema

**Required:**
- `name` (String) - Control name
- `enabled` (Boolean) - Enable control

**Optional:**
- `description` (String)
- `severity` (String) - INFORMATIONAL, LOW, MEDIUM, HIGH, CRITICAL
- `security_sub_categories` (Set of String)

**Read-Only:**
- `id` (String)
- `built_in` (Boolean)

---

### wiz_custom_control

Manage custom graph-based security controls.

**Required Permissions:** `create:controls`, `read:controls`, `update:controls`, `delete:controls`

#### Example Usage
```hcl
resource "wiz_custom_control" "public_vms_no_edr" {
  name        = "Public VMs without EDR"
  enabled     = true
  severity    = "CRITICAL"
  description = "VMs without EDR agents that are internet facing"
  resolution_recommendation = "Install EDR agent and restrict internet access"
  
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

#### Schema

**Required:**
- `name` (String) - Control name
- `query` (String) - Security Graph query (JSON encoded)
- `scope_query` (String) - Scope query defining applicable resources (JSON encoded)

**Optional:**
- `enabled` (Boolean) - Enable control (default: true)
- `description` (String) - Control description
- `resolution_recommendation` (String) - How to remediate
- `severity` (String) - INFORMATIONAL, LOW, MEDIUM, HIGH, CRITICAL
- `project_id` (String) - Scope to specific project
- `security_sub_categories` (Set of String) - Link to compliance frameworks
- `tags` (Block Set, Max: 10) - Key/value tags

**Read-Only:**
- `id` (String)
- `built_in` (Boolean)

---

### wiz_security_framework

Manage security/compliance frameworks.

**Required Permissions:** `create:security_frameworks`, `read:security_frameworks`, `update:security_frameworks`, `delete:security_frameworks`, `read:controls`, `read:host_configuration`, `read:cloud_configuration`

#### Schema

**Required:**
- `name` (String) - Framework name

**Optional:**
- `description` (String)
- `enabled` (Boolean) - Enable framework (default: true)
- `certification` (String) - Certification name
- `categories` (Block Set) - Framework categories
  - `name` (String, Required)
  - `description` (String)
  - `sub_categories` (Block Set)
    - `title` (String, Required)
    - `description` (String)
    - `controls` (Set of String) - Control IDs
    - `cloud_configuration_rules` (Set of String) - CCR IDs
    - `host_configuration_rules` (Set of String) - HCR IDs

**Read-Only:**
- `id` (String)
- `built_in` (Boolean)

---

## Automation & Response

### wiz_automation_rule

Manage automation rules for if-this-then-that workflows.

**Available from:** Version >= 1.3.238

**Required Permissions:** `create:automation_rules`, `read:automation_rules`, `update:automation_rules`, `delete:automation_rules`, `admin:run_response_action`

#### Example Usage

See the detailed examples in FILE 1 (wiz-terraform-provider-overview.md) - the wiz_automation_rule examples are extensive and cover:
- Notification services (AWS SNS, Azure Service Bus, GCP Pub/Sub)
- Message services (Slack, Google Chat, Microsoft Teams, Email)
- Ticket creation (Jira, ServiceNow, ClickUp, Azure DevOps, Freshservice)
- Ticket updates (Jira transitions, ServiceNow updates)
- Alert creation (PagerDuty, OpsGenie)
- Alert closure (PagerDuty, OpsGenie)
- System health issues
- Detection forwarding

#### Schema

**Required:**
- `name` (String) - Rule name
- `filters` (String) - JSON filters (format depends on trigger_source)
- `trigger_type` (List of String) - Trigger types
  - Possible values: `CREATED`, `UPDATED`, `RESOLVED`, `REOPENED`, `DETECTED`, `DUE`, `STATUS_CHANGED`

**Optional:**
- `actions` (Block Set) - Actions to execute (extensive nested schema - see data gathering for full details)
  - `action_type` (String, Required) - Type of action (50+ possible values)
  - `integration_id` (String, Required) - Integration ID
  - `params` (Block Set, Max: 1) - Action-specific parameters
    - Different param blocks for each action type (aws_sns, email, jira_create_ticket, slack, webhook, etc.)
- `auto_remediation` (Block Set, Max: 1) - Auto-remediation configuration
  - `auto_remediation_gateway_ids` (Set of String) - Gateway IDs
  - `deployment_ids` (Set of String) - Deployment IDs (V3)
  - `disruptive_actions_allowed` (Boolean) - Allow disruptive actions (default: false)
- `description` (String) - Rule description
- `enabled` (Boolean) - Enable rule (default: true)
- `project` (String) - Scope to specific project
- `trigger_parameters` (Block List, Max: 1) - Trigger parameters (required for DUE trigger)
  - `due` (Block List, Min: 1, Max: 1)
    - `within_days` (Number, Required) - Days before/after due date (+/- values)
- `trigger_source` (String) - Event source (default: ISSUES)
  - Possible values: `ISSUES`, `CLOUD_EVENTS`, `CONTROL`, `CONFIGURATION_FINDING`, `AUDIT_LOGS`, `SYSTEM_HEALTH_ISSUES`, `CI_CD_SCANS`, `DATA_FINDINGS`, `SYSTEM_ACTIVITIES`, `ADMISSION_REVIEWS`, `DETECTIONS`, `CLOUD_COST_OPPORTUNITIES`, `SERVICE_ISSUES`, `POSTURE_ISSUES`, `RUNTIME_EVENTS`, `THREATS`, `POLICY_UPDATES`, `CLOUD_COST_MONITOR_FINDINGS`

**Read-Only:**
- `id` (String) - Rule ID
- `created_at` (String) - Creation timestamp
- `created_by` (String) - Creator user ID
- `updated_at` (String) - Update timestamp
- `last_execution_error` (String) - Last error encountered

**Action Types:** The automation rule supports 100+ action types including:
- Notifications: AWS_SNS, AZURE_SERVICE_BUS, GCP_PUB_SUB
- Messaging: SLACK, SLACK_BOT, SLACK_APP_*, GOOGLE_CHAT, MICROSOFT_TEAMS, EMAIL
- Ticketing: JIRA_*, SERVICE_NOW_*, ZENDESK_*, LINEAR_*, CLICK_UP_*
- Alerting: PAGER_DUTY_*, OPSGENIE_*
- SIEM: SPLUNK, SUMO_LOGIC, AZURE_SENTINEL_*, DATADOG_*
- Webhooks: WEBHOOK, TINES, TORQ, and many CSP-specific integrations
- Wiz System: WIZ_SYSTEM_UPDATE_ISSUE_STATUS, WIZ_SYSTEM_ADD_ISSUE_COMMENT, etc.

---

### wiz_action_template

Manage action templates for automation rules.

**Required Permissions:** `create:action_templates`, `read:action_templates`, `delete:action_templates`

#### Schema

**Required:**
- `name` (String) - Template name
- `action_type` (String) - Type of action
- `integration_id` (String) - Integration to use

**Optional:**
- `description` (String)
- `params` (String) - JSON parameters

**Read-Only:**
- `id` (String)

---

### wiz_response_action_catalog_item

Manage response action catalog items.

**Required Permissions:** `create:response_action_catalog_items`, `read:response_action_catalog_items`, `update:response_action_catalog_items`, `delete:response_action_catalog_items`

#### Schema

**Required:**
- `name` (String) - Item name
- `action_type` (String) - Type of action

**Optional:**
- `enabled` (Boolean) - Enable item (default: true)
- `description` (String)

**Read-Only:**
- `id` (String)

---

### wiz_remediation_and_response_deployment_v2

Manage remediation and response deployments.

**Required Permissions:** `create/read/update/delete:remediation_and_response_deployments`, `read:remediation_and_response_connectors`, `read:outposts`

#### Schema

**Required:**
- `name` (String) - Deployment name

**Optional:**
- `enabled` (Boolean) - Enable deployment (default: true)
- `deployment_type` (String) - Type of deployment
- `projects` (Set of String) - Project IDs to scope to

**Read-Only:**
- `id` (String)
- `status` (String)

---

## Access & Identity

### wiz_project

Manage Wiz Projects. (For complete schema, see the wiz_project resource documented in the data gathering phase - it has 40+ nested schemas for cloud_account_links, kubernetes_cluster_links, etc.)

**Available from:** Version >= 1.3.189

**Required Permissions:** `admin:projects`, `read:projects`, `read:users`

#### Key Features
- Organize cloud resources by users/purposes
- Support for folder projects (hierarchical organization)
- Link to cloud accounts, Kubernetes clusters, repositories, container registries
- Risk profile configuration
- Resource filtering by tags, names, and other criteria

See FILE 1 for extensive examples or the full schema gathered in data collection.

---

### wiz_user

Manage Wiz users.

**Required Permissions:** `admin:users`, `read:users`

#### Schema

**Required:**
- `email` (String) - User email

**Optional:**
- `name` (String) - User name
- `role` (String) - User role
  - Examples: GLOBAL_ADMIN, PROJECT_OWNER, PROJECT_MEMBER
- `assigned_project_ids` (Set of String) - Projects assigned to user

**Read-Only:**
- `id` (String)
- `created_at` (String)
- `last_login` (String)

---

### wiz_user_role

Manage custom user roles.

**Required Permissions:** `admin:user_roles`, `read:any`

#### Schema

**Required:**
- `name` (String) - Role name
- `permissions` (Set of String) - Role permissions

**Optional:**
- `description` (String)
- `scope` (String) - GLOBAL or PROJECT

**Read-Only:**
- `id` (String)

---

### wiz_service_account

Manage service accounts.

**Required Permissions:** `create:service_accounts`, `read:service_accounts`, `update:service_accounts`, `delete:service_accounts`

#### Example Usage
```hcl
resource "wiz_service_account" "terraform" {
  name  = "Terraform Automation"
  type  = "CUSTOM_INTEGRATION"
  
  scopes = [
    "read:projects",
    "admin:projects",
    "create:connectors",
    "read:connectors"
  ]
}

output "client_id" {
  value = wiz_service_account.terraform.client_id
}

output "client_secret" {
  value     = wiz_service_account.terraform.client_secret
  sensitive = true
}
```

#### Schema

**Required:**
- `name` (String) - Service account name
- `type` (String) - Account type
  - Possible values: `CUSTOM_INTEGRATION`, `BROKER`
- `scopes` (Set of String) - Permission scopes

**Optional:**
- `project_ids` (Set of String) - Scope to specific projects

**Read-Only:**
- `id` (String)
- `client_id` (String) - Use for authentication
- `client_secret` (String, Sensitive) - Use for authentication (only visible on creation)

---

### wiz_saml_idp

Manage SAML identity providers.

**Required Permissions:** `admin:identity_providers`

#### Schema

**Required:**
- `name` (String) - IDP name
- `issuer_url` (String) - SAML issuer URL
- `login_url` (String) - SAML login URL
- `certificate` (String) - X.509 certificate

**Optional:**
- `enabled` (Boolean) - Enable IDP (default: true)
- `domains` (Set of String) - Email domains for this IDP

**Read-Only:**
- `id` (String)

---

### wiz_saml_group_mapping

Manage SAML group to Wiz role mappings.

**Required Permissions:** `admin:identity_providers`

#### Example Usage
```hcl
resource "wiz_saml_group_mapping" "admins" {
  saml_idp_id = data.wiz_saml_idps.main.saml_idps[0].id
  
  group_mapping {
    provider_group_id = "wiz-admins"
    role             = "GLOBAL_ADMIN"
  }
}

resource "wiz_saml_group_mapping" "project_team" {
  saml_idp_id = data.wiz_saml_idps.main.saml_idps[0].id
  
  group_mapping {
    provider_group_id = "platform-team"
    role             = "PROJECT_OWNER"
    projects         = [wiz_project.platform.id]
  }
}
```

#### Schema

**Required:**
- `saml_idp_id` (String) - SAML IDP ID
- `group_mapping` (Block Set, Min: 1) - Group mappings
  - `provider_group_id` (String, Required) - Group name from IDP
  - `role` (String, Required) - Wiz role to assign
    - Possible values: `GLOBAL_ADMIN`, `GLOBAL_READER`, `PROJECT_OWNER`, `PROJECT_CONTRIBUTOR`, `PROJECT_READER`
  - `projects` (Set of String, Optional) - Project IDs (required for project-scoped roles)

**Read-Only:**
- `id` (String)

---

### wiz_saml_lens_mapping

Manage SAML lens mappings for fine-grained access control.

**Required Permissions:** `admin:identity_providers`

#### Schema

**Required:**
- `saml_idp_id` (String) - SAML IDP ID
- `lens_mapping` (Block Set, Min: 1) - Lens mappings
  - `provider_group_id` (String, Required)
  - `lens_id` (String, Required) - Lens ID

**Read-Only:**
- `id` (String)

---

### wiz_integration

Manage integrations with external services.

**Required Permissions:** `read:integrations`, `write:integrations`

**Additional for new service accounts:** `read:service_accounts`, `write:service_accounts`, plus any permissions the integration requires

#### Schema

**Required:**
- `name` (String)
