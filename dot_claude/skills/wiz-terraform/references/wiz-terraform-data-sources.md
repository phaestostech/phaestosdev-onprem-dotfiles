# Wiz Terraform Provider - Data Sources Reference

Complete reference for all 22 Wiz Terraform data sources. Data sources allow you to query and retrieve information about existing components in Wiz.

## Table of Contents

1. [wiz_cloud_accounts](#wiz_cloud_accounts)
2. [wiz_cloud_configuration_rules](#wiz_cloud_configuration_rules)
3. [wiz_cloud_organizations](#wiz_cloud_organizations)
4. [wiz_container_registries](#wiz_container_registries)
5. [wiz_controls](#wiz_controls)
6. [wiz_graphql_query](#wiz_graphql_query)
7. [wiz_host_configuration_rules](#wiz_host_configuration_rules)
8. [wiz_host_configuration_target_platforms](#wiz_host_configuration_target_platforms)
9. [wiz_ignore_rules](#wiz_ignore_rules)
10. [wiz_image_integrity_validators](#wiz_image_integrity_validators)
11. [wiz_integrations](#wiz_integrations)
12. [wiz_kubernetes_clusters](#wiz_kubernetes_clusters)
13. [wiz_kubernetes_namespaces](#wiz_kubernetes_namespaces)
14. [wiz_projects](#wiz_projects)
15. [wiz_repositories](#wiz_repositories)
16. [wiz_resource_groups](#wiz_resource_groups)
17. [wiz_response_actions_catalog](#wiz_response_actions_catalog)
18. [wiz_saml_idps](#wiz_saml_idps)
19. [wiz_security_frameworks](#wiz_security_frameworks)
20. [wiz_threat_detection_rules](#wiz_threat_detection_rules)
21. [wiz_tunnel_server](#wiz_tunnel_server)
22. [wiz_users](#wiz_users)

---

## wiz_cloud_accounts

Retrieve details about cloud accounts (referred to as subscriptions in the Wiz portal).

**Available from:** Version ≥ 1.8.1154

**Required Permission:** `read:cloud_accounts`

### Example Usage
```hcl
# Fetch Azure cloud accounts matching a name
data "wiz_cloud_accounts" "search_azure" {
  search         = ["my-first-wiz-subscription"]
  cloud_provider = ["Azure"]
}

output "search_azure" {
  value = data.wiz_cloud_accounts.search_azure
}

# Fetch specific cloud accounts by IDs
data "wiz_cloud_accounts" "search_ids" {
  ids = [
    "157173ac-ed87-478c-befb-ee7931c9e114",
    "60e66222-6ad8-4ac0-b467-0121fcf89012"
  ]
}

output "search_ids" {
  value = data.wiz_cloud_accounts.search_ids
}
```

### Schema

#### Optional Arguments

- **`assigned_to_project`** (Boolean) - Filter cloud accounts assigned to a project
- **`cloud_provider`** (Set of String) - Filter by cloud provider
  - Possible values: `GCP`, `AWS`, `Azure`, `OCI`, `Alibaba`, `vSphere`, `OpenStack`, `OpenShift`, `Kubernetes`, `Linode`, `AzureDevOps`, `GitHub`, `GitLab`, `Bitbucket`, `Terraform`, `OpenAI`, `Snowflake`, `MongoDBAtlas`, `Databricks`, `Okta`, `Cloudflare`, `Microsoft365`, `Wiz`, `SelfHosted`, `IBM`, `ServiceNow`, `Vercel`, `GenericDB`, `DigitalOcean`, `TencentCloud`
- **`connector_id`** (Set of String) - Filter by connector IDs
- **`connector_issues_id`** (Set of String) - Filter cloud accounts with these connector issue IDs
- **`has_multiple_connector_sources`** (Boolean) - Filter accounts with multiple connector sources
- **`ids`** (Set of String) - The IDs of specific cloud accounts
- **`project_id`** (String) - Filter accounts scoped to these Wiz project IDs
- **`search`** (Set of String) - Search matching external ID, name, or tags
- **`status`** (Set of String) - Filter by cloud account status
  - Possible values: `CONNECTED`, `ERROR`, `DISABLED`, `INITIAL_SCANNING`, `PARTIALLY_CONNECTED`, `DISCONNECTED`, `DISCOVERED`

#### Read-Only Attributes

- **`cloud_accounts`** (Set of Object) - List of filtered cloud accounts
  - `cloud_provider` (String)
  - `external_id` (String)
  - `id` (String)
  - `linked_project_ids` (List of String)
  - `name` (String)
  - `source_connector_ids` (List of String)
  - `status` (String)
- **`id`** (String) - The ID of this resource

---

## wiz_cloud_configuration_rules

Retrieve details about Cloud Configuration Rules.

**Available from:** Version ≥ 1.8.1154

**Required Permission:** `read:cloud_configuration`

### Example Usage
```hcl
data "wiz_cloud_configuration_rules" "this" {
  search          = "test"
  cloud_platform  = ["Azure"]
  risk_equals_any = ["VULNERABILITY"]
}

output "this" {
  value = data.wiz_cloud_configuration_rules.this
}
```

### Schema

#### Optional Arguments

- **`cloud_platform`** (Set of String) - Filter rules targeting these platforms
  - Possible values: Same as `cloud_provider` in wiz_cloud_accounts
- **`created_by`** (Set of String) - Filter by creator type: `USER`, `BUILT_IN`
- **`enabled`** (Boolean, Deprecated) - Use `enabled_string` instead
- **`enabled_string`** (String) - Filter enabled rules: `true`, `false`
- **`generates_issues`** (Boolean) - Filter rules that generate Issues
- **`has_findings`** (Boolean) - Filter rules with findings
- **`has_remediation_steps`** (Boolean) - Filter rules with remediation steps
- **`id`** (Set of String) - Filter by rule IDs
- **`native_type`** (Set of String) - Filter by native types (e.g., "ec2", "bucket")
- **`original_configuration_rule_overridden`** (Boolean) - Search built-in rules with overridden values
- **`resource_type`** (Set of String) - Filter by targeted resource type
- **`risk_equals_all`** (Set of String) - Filter by risk type (rule must match ALL)
  - Possible values: `VULNERABILITY`, `EXTERNAL_EXPOSURE`, `UNPROTECTED_PRINCIPAL`, `INSECURE_USE_OF_SECRETS`, `UNPROTECTED_DATA`, `INSECURE_KUBERNETES_CLUSTER`, `HIGH_PROFILE_THREAT`, `RELIABILITY_IMPACT`, `INSECURE_CICD`, `INSECURE_APPLICATION`, `AI_SECURITY`, `EXTERNAL_ATTACK_SURFACE`, `APPLICATION_WEAKNESS`
- **`risk_equals_any`** (Set of String) - Filter by risk type (rule must match ANY)
- **`scope_project_id`** (String) - Filter by scoped Wiz project ID
- **`search`** (String) - Search for rules matching the search string
- **`security_sub_categories`** (Set of String) - Filter by security framework categories
- **`severity`** (Set of String) - Filter by severity: `INFORMATIONAL`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`
- **`short_id`** (Block Set, Max: 1) - Search on rules shortId
  - `equals` (Set of String) - Filter by rules matching these names
- **`subscription_scope`** (Set of String) - Filter by scoped subscriptions
- **`supports_auto_remediation`** (Boolean) - Filter rules supporting auto-remediation
- **`supports_nrt`** (Boolean) - Filter rules supporting near real-time updates
- **`tags`** (Block Set, Max: 1) - Filter by assigned tags
  - `key` (String, Required) - Key of the tag
  - `value` (String, Optional) - Value of the tag
- **`target_platform`** (Set of String) - Filter by target platform
  - Possible values: `TERRAFORM`, `CLOUD_FORMATION`, `KUBERNETES`, `AZURE_RESOURCE_MANAGER`, `DOCKER_FILE`, `ADMISSION_CONTROLLER`, `ANSIBLE`, `GOOGLE_CLOUD_DEPLOYMENT_MANAGER`, `GITHUB_ACTIONS`, `BICEP`

#### Read-Only Attributes

- **`cloud_configuration_rules`** (Set of Object) - List of filtered rules
  - `built_in` (Boolean)
  - `description` (String)
  - `enabled` (Boolean)
  - `function_as_control` (Boolean)
  - `iac_matchers` (Set of Object)
    - `parameters` (Set of Object)
      - `admission_controller` (Set of Object)
        - `operation` (Set of String)
    - `rego_code` (String)
    - `remediation_instructions` (String)
    - `type` (String)
  - `id` (String)
  - `name` (String)
  - `opa_policy` (String)
  - `remediation_instructions` (String)
  - `risks` (Set of String)
  - `scope_account_ids` (Set of String)
  - `scope_project_id` (String)
  - `security_frameworks_managed_externally` (Set of String)
  - `security_sub_categories` (Set of String)
  - `severity` (String)
  - `short_id` (String)
  - `tags` (Set of Object)
    - `key` (String)
    - `value` (String)
  - `target_native_types` (Set of String)

---

## wiz_cloud_organizations

Retrieve details about cloud organizations.

**Available from:** Version ≥ 1.8.1154

**Required Permission:** `read:cloud_accounts`

### Example Usage
```hcl
data "wiz_cloud_organizations" "external_id_lookup" {
  search = ["tenantId/1df56910-a0c4-4966-a78d-06b1ea77a16d"]
}

output "org_ids" {
  value = data.wiz_cloud_organizations.external_id_lookup.cloud_organizations.*.id
}
```

### Schema

#### Optional Arguments

- **`cloud_provider`** (Set of String) - Filter by cloud provider (same values as wiz_cloud_accounts)
- **`ids`** (Set of String) - Filter by organization IDs
- **`search`** (Set of String) - Search matching external ID, name, or tags

#### Read-Only Attributes

- **`cloud_organizations`** (Set of Object) - List of filtered organizations
  - `cloud_provider` (String)
  - `external_id` (String)
  - `id` (String)
  - `name` (String)

---

## wiz_container_registries

Retrieve details about container registries.

**Available from:** Version ≥ 1.8.1154

**Required Permission:** `read:registries`

### Schema

#### Optional Arguments

- **`ids`** (Set of String) - Filter by registry IDs
- **`search`** (Set of String) - Search matching name

#### Read-Only Attributes

- **`container_registries`** (Set of Object) - List of filtered registries
  - `external_id` (String)
  - `id` (String)
  - `name` (String)
  - `registry_type` (String)

---

## wiz_controls

Query Wiz built-in and custom Controls.

**Required Permission:** `read:controls`

### Example Usage
```hcl
data "wiz_controls" "search" {
  search   = "test"
  severity = ["HIGH"]
}

output "search" {
  value = data.wiz_controls.search
}

data "wiz_controls" "type" {
  type = ["SECURITY_GRAPH"]
}

output "type" {
  value = data.wiz_controls.type
}
```

### Schema

#### Optional Arguments

- **`created_by`** (Set of String) - Filter by creator: `USER`, `BUILT_IN`
- **`enabled`** (Boolean, Deprecated) - Use `enabled_string` instead
- **`enabled_string`** (String) - Filter enabled controls: `true`, `false`
- **`framework_category`** (Set of String) - Filter by security framework categories
- **`has_issues`** (Boolean) - Filter controls with existing Wiz issues
- **`overridden_built_in_control`** (Boolean) - Filter built-in controls overridden by user
- **`project_id`** (Set of String) - Filter by scoped Wiz project IDs
- **`search`** (String) - Search matching external ID, name, or path
- **`severity`** (Set of String) - Filter by severity: `INFORMATIONAL`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`
- **`supports_auto_remediation`** (Boolean) - Filter controls supporting auto-remediation
- **`tags`** (Block Set, Max: 1) - Filter by tags
  - `key` (String, Required)
  - `value` (String, Optional)
- **`type`** (Set of String) - Search by type

#### Read-Only Attributes

- **`controls`** (Set of Object) - List of filtered controls
  - `built_in` (Boolean)
  - `description` (String)
  - `enabled` (Boolean)
  - `id` (String)
  - `name` (String)
  - `project_id` (String)
  - `query` (String)
  - `resolution_recommendation` (String)
  - `scope_query` (String)
  - `security_frameworks_managed_externally` (Set of String)
  - `security_sub_categories` (Set of String)
  - `severity` (String)
  - `tags` (Set of Object)
    - `key` (String)
    - `value` (String)

---

## wiz_graphql_query

Make custom queries to the Wiz GraphQL API.

**Required Permissions:** Dependent on the query being run

### Example Usage
```hcl
# Get region details about Wiz DC
data "wiz_graphql_query" "query_example" {
  query = <<-EOF
    query DataCenterInfrastructureDetails {
      dataCenterInfrastructureDetails {
        dataCenter
        region
        regionLocation
      }
    }
  EOF
}

output "query_example" {
  value = jsondecode(data.wiz_graphql_query.query_example.result)
}

# Query with filter and variables
data "wiz_graphql_query" "query_with_filter" {
  query = <<-EOQ
    query DataClassifierRulesPage($filterBy: DataClassifierFilters, $first: Int, $after: String) {
      dataClassifiers(filterBy: $filterBy, first: $first, after: $after) {
        nodes {
          id
          name
          countryCodes
        }
        pageInfo {
          endCursor
          hasNextPage
        }
      }
    }
  EOQ
  
  query_variables = jsonencode({
    "filterBy": {
      "countryCode": "US"
    }
  })
}

output "query_with_filter" {
  value = jsondecode(data.wiz_graphql_query.query_with_filter.result)
}
```

### Schema

#### Required Arguments

- **`query`** (String) - GraphQL query to run

#### Optional Arguments

- **`query_variables`** (String) - GraphQL query variables (JSON encoded)

#### Read-Only Attributes

- **`id`** (String) - The ID of this resource
- **`result`** (String) - List of result objects (JSON string)

### Important Notes

- To enable pagination, include the `pageInfo` object in your query
- `graphSearch` queries are NOT supported (prevents performance issues)
- Use `jsondecode()` to parse the result string into Terraform objects

---

## wiz_host_configuration_rules

Retrieve details about Host Configuration Rules.

**Available from:** Version ≥ 1.8.1154

**Required Permission:** `read:host_configuration`

### Schema

#### Optional Arguments

- **`created_by`** (Set of String) - Filter by creator: `USER`, `BUILT_IN`
- **`enabled_string`** (String) - Filter enabled rules: `true`, `false`
- **`has_auto_remediation`** (Boolean) - Filter rules with auto-remediation
- **`id`** (Set of String) - Filter by rule IDs
- **`search`** (String) - Search matching name or description
- **`security_sub_categories`** (Set of String) - Filter by security framework categories
- **`severity`** (Set of String) - Filter by severity
- **`short_id`** (Block Set, Max: 1) - Search by short ID
  - `equals` (Set of String)
- **`tags`** (Block Set, Max: 1) - Filter by tags
  - `key` (String, Required)
  - `value` (String, Optional)
- **`target_operating_systems`** (Set of String) - Filter by target OS

#### Read-Only Attributes

- **`host_configuration_rules`** (Set of Object) - List of filtered rules
  - Similar structure to cloud_configuration_rules with host-specific fields

---

## wiz_host_configuration_target_platforms

Query host configuration target platforms.

**Required Permission:** Any

### Schema

#### Read-Only Attributes

- **`platforms`** (List of Object) - List of available platforms
  - `id` (String)
  - `name` (String)
  - `operating_systems` (List of Object)
    - `id` (String)
    - `name` (String)

---

## wiz_ignore_rules

Retrieve details about Ignore Rules.

**Required Permission:** `read:ignore_rules`

### Schema

#### Optional Arguments

- **`enabled`** (Boolean) - Filter enabled/disabled rules
- **`id`** (Set of String) - Filter by rule IDs
- **`search`** (String) - Search matching name or notes

#### Read-Only Attributes

- **`ignore_rules`** (Set of Object) - List of filtered ignore rules
  - `enabled` (Boolean)
  - `id` (String)
  - `name` (String)
  - `note` (String)
  - `scope` (Set of Object)
  - `target_policies` (Set of Object)

---

## wiz_image_integrity_validators

Retrieve image integrity validator settings.

**Required Permission:** `read:image_integrity_validators_settings`

### Schema

#### Read-Only Attributes

- **`validators`** (List of Object) - List of validators
  - `id` (String)
  - `name` (String)
  - `enabled` (Boolean)
  - `validator_type` (String)
  - Configuration fields specific to validator type

---

## wiz_integrations

Retrieve details about integrations.

**Required Permission:** `read:integrations`

### Example Usage
```hcl
data "wiz_integrations" "slack" {
  type = ["SLACK"]
}

output "slack_integrations" {
  value = data.wiz_integrations.slack.integrations
}
```

### Schema

#### Optional Arguments

- **`id`** (Set of String) - Filter by integration IDs
- **`name`** (String) - Filter by name (contains)
- **`type`** (Set of String) - Filter by integration type
  - Examples: `SLACK`, `JIRA`, `SERVICE_NOW`, `PAGERDUTY`, `EMAIL`, `AWS_SNS`, etc.

#### Read-Only Attributes

- **`integrations`** (Set of Object) - List of filtered integrations
  - `id` (String)
  - `name` (String)
  - `type` (String)
  - `created_at` (String)
  - `is_accessible` (Boolean)

---

## wiz_kubernetes_clusters

Retrieve details about Kubernetes clusters.

**Required Permission:** `read:kubernetes_clusters`

### Schema

#### Optional Arguments

- **`ids`** (Set of String) - Filter by cluster IDs
- **`search`** (Set of String) - Search matching name

#### Read-Only Attributes

- **`kubernetes_clusters`** (Set of Object) - List of filtered clusters
  - `id` (String)
  - `name` (String)
  - `external_id` (String)
  - `cloud_provider` (String)

---

## wiz_kubernetes_namespaces

Retrieve details about Kubernetes namespaces.

**Required Permission:** `read:resources`

### Schema

#### Optional Arguments

- **`kubernetes_cluster_id`** (String) - Filter by cluster ID
- **`search`** (Set of String) - Search matching namespace name

#### Read-Only Attributes

- **`namespaces`** (Set of Object) - List of filtered namespaces
  - `id` (String)
  - `name` (String)
  - `kubernetes_cluster_id` (String)

---

## wiz_projects

Query Wiz Projects.

**Available from:** Version ≥ 1.8.1154

**Required Permissions:** `read:projects`, `read:users`

### Example Usage
```hcl
data "wiz_projects" "all" {}

data "wiz_projects" "search" {
  search = "test"
}

data "wiz_projects" "search_parent" {
  search            = "test"
  parent_project_id = "69934157-5b4c-5340-9b29-7193c3dbafb2"
}

data "wiz_projects" "advanced" {
  ids {
    equals = [
      "53d0e7a1-aaec-5a63-a57e-c2a40ee6ecf8",
      "f94dcc15-2f55-58a0-b31c-0a227a373fe3"
    ]
  }
  business_impact  = ["MBI"]
  is_folder        = ""
  include_archived = true
  root_only_search = false
}

output "all" {
  value = length(data.wiz_projects.all.projects)
}

output "search" {
  # To use IDs of returned projects
  value = data.wiz_projects.search.projects.*.id
}
```

### Schema

#### Optional Arguments

- **`business_impact`** (Set of String) - Filter by business impact: `LBI`, `MBI`, `HBI`
- **`ids`** (Block Set, Max: 1) - Filter by project IDs
  - `equals` (List of String) - IDs to match
  - `not_equals` (List of String) - IDs to exclude
- **`include_archived`** (Boolean) - Include archived projects (default: false)
- **`is_folder`** (String) - Filter folder projects: `true`, `false` (empty string bypasses filter)
- **`parent_project_id`** (String) - Filter by parent project ID
- **`root_only_search`** (Boolean) - Filter root-level projects only (default: false)
- **`search`** (String) - Search projects by name

#### Read-Only Attributes

- **`id`** (String) - The ID of this resource
- **`projects`** (Set of Object) - List of filtered projects (extensive nested schema - see wiz_project resource for full details)
  - Includes all project properties: name, description, risk_profile, cloud_account_links, kubernetes_cluster_links, etc.

---

## wiz_repositories

Retrieve details about repositories.

**Required Permission:** `read:resources`

### Schema

#### Optional Arguments

- **`ids`** (Set of String) - Filter by repository IDs
- **`search`** (Set of String) - Search matching repository name

#### Read-Only Attributes

- **`repositories`** (Set of Object) - List of filtered repositories
  - `id` (String)
  - `name` (String)
  - `external_id` (String)
  - `repository_type` (String)

---

## wiz_resource_groups

Retrieve details about resource groups.

**Required Permission:** `read:resources`

### Schema

#### Optional Arguments

- **`cloud_account_id`** (String) - Filter by cloud account ID
- **`search`** (Set of String) - Search matching resource group name

#### Read-Only Attributes

- **`resource_groups`** (Set of Object) - List of filtered resource groups
  - `id` (String)
  - `name` (String)
  - `cloud_account_id` (String)

---

## wiz_response_actions_catalog

Query response action catalog items.

**Required Permission:** `read:response_action_catalog_items`

### Schema

#### Optional Arguments

- **`enabled`** (Boolean) - Filter enabled/disabled items
- **`id`** (Set of String) - Filter by item IDs
- **`name`** (String) - Filter by name (contains)

#### Read-Only Attributes

- **`catalog_items`** (Set of Object) - List of catalog items
  - `id` (String)
  - `name` (String)
  - `description` (String)
  - `enabled` (Boolean)
  - `action_type` (String)

---

## wiz_saml_idps

Query SAML identity providers.

**Required Permission:** `admin:identity_providers`

### Schema

#### Optional Arguments

- **`id`** (Set of String) - Filter by IDP IDs
- **`name`** (String) - Filter by name (contains)

#### Read-Only Attributes

- **`saml_idps`** (Set of Object) - List of SAML IDPs
  - `id` (String)
  - `name` (String)
  - `issuer_url` (String)
  - `login_url` (String)
  - `enabled` (Boolean)

---

## wiz_security_frameworks

Data Source for Wiz Security Frameworks (Compliance Frameworks) with associated Categories, Subcategories, Controls, Cloud Configuration Rules, and Host Configuration Rules.

**Required Permissions:** `read:security_frameworks`, `read:controls`, `read:cloud_configuration`, `read:host_configuration`

### Example Usage
```hcl
# Retrieve all security frameworks
data "wiz_security_frameworks" "all" {}

# Search by name
data "wiz_security_frameworks" "search" {
  search = "test"
}

# Search by specific IDs
data "wiz_security_frameworks" "search_by_ids" {
  security_framework_ids = [
    "cebd74c2-92c3-47d4-bff8-e78d6c3c515d",
    "e2fa651f-853d-4030-a2e2-cde86566cf08"
  ]
}

# Filter by policy type
data "wiz_security_frameworks" "search_by_policy_types" {
  policy_types = ["CLOUD"]
}

# Get disabled frameworks
data "wiz_security_frameworks" "disabled_frameworks" {
  enabled = false
}

# Extract specific data
output "security_framework_name" {
  value = data.wiz_security_frameworks.search_by_ids.security_frameworks[0].name
}

output "number_of_disabled_frameworks" {
  value = length(data.wiz_security_frameworks.disabled_frameworks.security_frameworks)
}

# Advanced: Filter subcategory IDs
data "wiz_security_frameworks" "mitre_cloud_matrix" {
  search = "MITRE ATT&CK Cloud Matrix"
}

locals {
  security_sub_categories = flatten([
    for framework in data.wiz_security_frameworks.mitre_cloud_matrix.security_frameworks : [
      for category in framework.category : [
        for sub_category in category.sub_category : {
          framework_id              = framework.id
          category_id               = category.id
          category_name             = category.name
          sub_category_id           = sub_category.id
          sub_category_description  = sub_category.description
        }
      ]
    ]
  ])
  
  # Filter for specific category
  filtered_sub_category_ids = [
    for item in local.security_sub_categories :
    item.sub_category_id if item.category_name == "Privilege Escalation"
  ]
}

# Use filtered subcategories in resource
resource "wiz_custom_control" "example" {
  name                     = "example"
  security_sub_categories  = local.filtered_sub_category_ids
  # ... other configuration ...
}
```

### Schema

#### Optional Arguments

- **`enabled`** (Boolean) - Filter enabled/disabled security frameworks
- **`policy_types`** (Set of String) - Filter by policy type: `CLOUD`, `HOST`
- **`search`** (String) - Search by security framework name
- **`security_framework_ids`** (Set of String) - Search by framework IDs

#### Read-Only Attributes

- **`id`** (String) - The ID of this resource
- **`security_frameworks`** (List of Object) - List of security frameworks
  - `built_in` (Boolean)
  - `categories_ids_dict` (String)
  - `category` (List of Object)
    - `description` (String)
    - `id` (String)
    - `name` (String)
    - `security_score_impact` (Number)
    - `sub_category` (List of Object)
      - `cloud_configuration_rules` (Set of String)
      - `cloud_configuration_rules_managed_externally` (Set of String)
      - `controls` (Set of String)
      - `controls_managed_externally` (Set of String)
      - `description` (String)
      - `host_configuration_rules` (Set of String)
      - `host_configuration_rules_managed_externally` (Set of String)
      - `id` (String)
      - `title` (String)
  - `certification` (String)
  - `description` (String)
  - `enabled` (Boolean)
  - `id` (String)
  - `maintain_rule_links_from_framework` (Boolean)
  - `name` (String)
  - `policy_types` (List of String)

---

## wiz_threat_detection_rules

Retrieve details about Threat Detection Rules.

**Required Permissions:** `read:cloud_events_cloud`, `read:cloud_event_rules`

### Schema

#### Optional Arguments

- **`enabled`** (Boolean) - Filter enabled/disabled rules
- **`id`** (Set of String) - Filter by rule IDs
- **`search`** (String) - Search matching name or description
- **`severity`** (Set of String) - Filter by severity

#### Read-Only Attributes

- **`threat_detection_rules`** (Set of Object) - List of filtered rules
  - `id` (String)
  - `name` (String)
  - `description` (String)
  - `enabled` (Boolean)
  - `severity` (String)
  - Rule-specific configuration

---

## wiz_tunnel_server

Query tunnel servers (connectors).

**Required Permission:** `read:connectors`

### Schema

#### Optional Arguments

- **`id`** (Set of String) - Filter by tunnel server IDs
- **`name`** (String) - Filter by name (contains)

#### Read-Only Attributes

- **`tunnel_servers`** (Set of Object) - List of tunnel servers
  - `id` (String)
  - `name` (String)
  - `status` (String)
  - `deployment_type` (String)

---

## wiz_users

Retrieve a list of users based on filter criteria.

**Required Permission:** `read:users`

### Example Usage
```hcl
// Search for PROJECT_MEMBER users on SAML and WIZ Auth Providers
data "wiz_users" "project_members" {
  role               = ["PROJECT_MEMBER"]
  auth_provider_type = ["SAML", "WIZ"]
}

output "project_members" {
  value       = data.wiz_users.project_members
  description = "All users with PROJECT_MEMBER role"
}

// Search for GLOBAL_ADMIN users with @example.com domain
data "wiz_users" "example_admins" {
  search = "@example.com"
  role   = ["GLOBAL_ADMIN"]
}

output "example_admins" {
  value       = data.wiz_users.example_admins
  description = "Admin users with @example.com domain"
}

// Search for deleted GLOBAL_ADMIN users
data "wiz_users" "example_deleted_admins" {
  role    = ["GLOBAL_ADMIN"]
  deleted = "true"
}

output "example_deleted_admins" {
  value       = data.wiz_users.example_deleted_admins
  description = "Deleted admin users"
}
```

### Schema

#### Optional Arguments

- **`auth_provider_type`** (Set of String) - Filter by Authentication Provider Type
  - Possible values: `WIZ`, `SAML`
- **`deleted`** (String) - Filter by deleted users: `true`, `false`
- **`role`** (Set of String) - Filter by list of roles
- **`search`** (String) - Free text search for name, email, or object name

#### Read-Only Attributes

- **`id`** (String) - The ID of this resource
- **`wiz_users`** (Set of Object) - List of filtered users
  - `assigned_project_ids` (Set of String)
  - `authentication_source` (String)
  - `created_at` (String)
  - `deleted_at` (String)
  - `email` (String)
  - `expires_at` (String)
  - `id` (String)
  - `identity_provider_assigned_projects` (Set of Object)
    - `id` (String)
    - `name` (String)
  - `identity_provider_type` (String)
  - `idp_id` (String)
  - `is_suspended` (Boolean)
  - `last_login` (String)
  - `name` (String)
  - `role` (String)
  - `tenant` (Set of Object)
    - `id` (String)
    - `name` (String)
  - `updated_at` (String)

---

## Summary

This document covers all 22 Wiz Terraform data sources with complete schemas, examples, and attribute documentation. Each data source requires specific permissions and allows querying existing Wiz resources to use in your Terraform configurations.

**Key Points:**
- Data sources are read-only and query existing resources
- Most support filtering by multiple criteria
- Results can be used in resource configurations
- Always check required permissions before use
- Use `jsondecode()` for JSON string results like in `wiz_graphql_query`
