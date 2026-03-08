# MCP Threats & Issues Reference

MCP tools for threat detection and issue investigation.

## Threat Investigation Tools

### mcp__wiz__list_threats

List active threat detections from Wiz.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `severity` | string[] | Filter: `["CRITICAL", "HIGH", "MEDIUM", "LOW"]` |
| `status` | string[] | Filter: `["OPEN", "IN_PROGRESS", "RESOLVED", "REJECTED"]` |
| `first` | int | Limit results (default: 10) |

**Example:**
```
mcp__wiz__list_threats with:
- severity: ["CRITICAL", "HIGH"]
- status: ["OPEN", "IN_PROGRESS"]
- first: 20
```

### mcp__wiz__get_threat

Get detailed threat analysis.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `issue_id` | string | Threat/issue ID from list_threats |

**Returns:** Full threat details including attack path, affected resources, and remediation steps.

### mcp__wiz__list_resolved_threats

Find similar past resolutions for remediation guidance.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `severity` | string[] | Filter by severity |
| `first` | int | Limit results |

## Issue Investigation Tools

### mcp__wiz__list_issues

List cloud misconfigurations and security issues.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `severity` | string[] | Filter: `["CRITICAL", "HIGH", "MEDIUM", "LOW"]` |
| `status` | string[] | Filter: `["OPEN", "IN_PROGRESS", "RESOLVED", "REJECTED"]` |
| `cloud_provider` | string[] | Filter: `["AWS", "Azure", "GCP", "OCI", "Alibaba"]` |
| `first` | int | Limit results |

**Example:**
```
mcp__wiz__list_issues with:
- severity: ["CRITICAL", "HIGH"]
- status: ["OPEN"]
- cloud_provider: ["AWS"]
- first: 25
```

### mcp__wiz__get_issue

Get full issue details including remediation.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `issue_id` | string | Issue ID from list_issues |

### mcp__wiz__list_issues_grouped

Group issues by resource, subscription, or other dimension.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `group_by` | string | Grouping dimension |
| `severity` | string[] | Filter by severity |

### mcp__wiz__get_issue_assignee_suggestions

Get recommended assignees for an issue.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `issue_id` | string | Issue ID |

## Common Investigation Workflows

### Triage New Threats

1. List critical/high threats: `mcp__wiz__list_threats`
2. Get details for each: `mcp__wiz__get_threat`
3. Find similar resolved: `mcp__wiz__list_resolved_threats`
4. Assign to team: `mcp__wiz__get_issue_assignee_suggestions`

### Review Cloud Misconfigurations

1. List open issues by severity: `mcp__wiz__list_issues`
2. Group by subscription/project: `mcp__wiz__list_issues_grouped`
3. Get remediation steps: `mcp__wiz__get_issue`

### Executive Summary

```
For C-level briefing:
1. mcp__wiz__list_threats with severity=["CRITICAL"], status=["OPEN"]
2. mcp__wiz__list_issues with severity=["CRITICAL"], status=["OPEN"]
3. mcp__wiz__get_project for security score trends
```
