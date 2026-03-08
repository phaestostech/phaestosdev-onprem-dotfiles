# MCP Graph Search Reference

Free-text security queries using Wiz's graph database.

## mcp__wiz__graph_search

Query the Wiz security graph with natural language.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `query` | string | Natural language query |

## Query Patterns

### Public Exposure

```
"Show me all S3 buckets that are publicly accessible"
"Find Azure storage accounts with public blob access"
"List GCS buckets without uniform bucket-level access"
```

### Vulnerability Queries

```
"Find EC2 instances with critical vulnerabilities"
"Show containers with CVE-2024-xxxxx"
"List resources affected by Log4Shell"
```

### Access & Permissions

```
"Find service accounts with admin privileges"
"Show IAM roles with overly permissive policies"
"List users with console access and no MFA"
```

### Container Security

```
"List containers running as root"
"Find pods without resource limits"
"Show containers with privileged mode"
```

### Network Security

```
"Find security groups allowing 0.0.0.0/0 inbound"
"Show databases accessible from internet"
"List load balancers without WAF"
```

### Data Security

```
"Find databases without encryption at rest"
"Show S3 buckets without server-side encryption"
"List resources with sensitive data exposure"
```

### Compliance

```
"Show resources not compliant with CIS benchmark"
"Find resources violating HIPAA controls"
"List PCI DSS compliance gaps"
```

## Query Tips

1. **Be specific** - Include resource types, severity, or conditions
2. **Use cloud terminology** - "EC2", "S3", "pods" rather than generic terms
3. **Combine conditions** - "critical vulnerabilities AND public exposure"
4. **Reference CVEs** - Include CVE IDs for specific vulnerability searches

## Example Workflows

### Attack Surface Review
```
1. "Show all internet-facing resources"
2. "Find public resources with critical vulnerabilities"
3. "List external endpoints without WAF protection"
```

### Toxic Combination Hunt
```
1. "Find resources with public access AND sensitive data"
2. "Show containers with root AND external network access"
3. "List databases accessible from internet with no encryption"
```

### Compliance Audit
```
1. "Show all non-compliant resources by framework"
2. "Find resources missing required tags"
3. "List encryption gaps across all services"
```
