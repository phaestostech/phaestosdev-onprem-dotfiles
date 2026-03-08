# Architecture Decision Record Template

Based on [Michael Nygard's ADR format](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions)
and Harrison.ai Confluence templates.

## File naming

```
adr/NNNN-short-description.md
```

Examples:
- `adr/0001-use-d2-for-diagrams.md`
- `adr/0002-nodejs-http-request-library.md`
- `adr/0003-static-app-delivery.md`

## Template

```markdown
# ADR: [Title]

| Field | Value |
|-------|-------|
| **System/Project** | [e.g., Harrison Open Platform] |
| **Architect/Team** | [e.g., Backend / Platforms] |
| **Domain/Quantum** | [e.g., Frontend, Backend, Infrastructure] |
| **Impact** | High / Medium / Low |
| **Driver** | [Name and email of person driving the decision] |
| **Approver** | [Name and email of approver] |
| **Contributors** | [Names of contributors] |
| **Informed** | [Stakeholders who should be informed] |
| **Date modified** | [YYYY-MM-DD] |
| **Next review date** | [YYYY-MM-DD or N/A] |
| **Resources** | [Links to relevant research, pages, related decisions] |

## Status

[proposed | accepted | rejected | deprecated | superseded]

If superseded, link to the replacement ADR.

## Context

What is the issue motivating this decision or change?

- Describe the current situation
- Explain the problem or need
- Include relevant technical constraints
- Reference any incidents or discoveries that triggered this

## Comparison (optional)

When evaluating multiple options, include a comparison table:

| Criterion | Option A | Option B | Option C |
|-----------|----------|----------|----------|
| Criterion 1 | Value | Value | Value |
| Criterion 2 | Value | Value | Value |
| Criterion 3 | Value | Value | Value |

## Decision

What is the change we are proposing and/or doing?

State the decision clearly and directly:
- "We will use X for Y"
- "We will adopt Z as the standard for W"

## Consequences

What becomes easier or more difficult because of this change?

**Positive:**
- Benefit 1
- Benefit 2

**Trade-offs / Mitigations:**
- Trade-off 1: Mitigation approach
- Trade-off 2: Mitigation approach

## Compliance

How will you ensure compliance with this decision?

- Code review requirements
- Automated checks
- Team involvement required for exceptions

## Notes

Additional context, implementation details, or metadata:

1. **Implementation detail 1** - Description
2. **Implementation detail 2** - Description
```

## Example: NodeJS HTTP Request Library

```markdown
# ADR: NodeJS HTTP Request Library

| Field | Value |
|-------|-------|
| **System/Project** | Annalise Enterprise V2 |
| **Architect/Team** | Backend (Teapots) |
| **Domain/Quantum** | Backend |
| **Impact** | Low |
| **Driver** | Duong Dinh |
| **Approver** | Andru Manuel-Che |
| **Contributors** | - |
| **Informed** | - |
| **Date modified** | 2025-04-15 |
| **Next review date** | N/A |
| **Resources** | https://www.npmjs.com/package/got |

## Status

accepted

## Context

During the Silent Deploy milestone for the Annalise Enterprise V2 Platform and CTC release, it was
discovered there were regular occurrences of timeouts between the orchestrator and backend
components. The Orchestrator as the client has been configured to timeout a request after 10
minutes. This was not respected in the Backend server as observability demonstrated consistent
timeouts at exactly 5 minutes.

The Backend as a component is primarily forwarding the orchestrator requests onto the AI model
containers. The AI containers processing can exceed 5 minutes, hence the requirement for a
long-running request connection.

It was discovered the Node `fetch` library does not have a configurable timeout support and
defaults to 5 minutes in the Backend.

In addition to the issues encountered, future architectural requirements will require HTTP/2 and
Server Side Events (SSE) support.

## Comparison

| | HTTP/2 SSE support | Configurable timeouts | Browser support | Notes |
|---|---|---|---|---|
| fetch | no | No (5 min default) | yes | |
| axios | no | yes | yes | |
| got | yes | yes | no | Same maintainers as Ky |
| ky | yes | yes | yes | Meant for browser use |

## Decision

We will use `got` as the HTTP request library for any NodeJS server-side components.

## Consequences

**Positive:**
- Timeout configuration will be possible
- HTTP/2 and SSE support for future requirements

**Trade-offs:**
- The library does not have browser support
- Additional off-the-shelf dependency which adds to blast radius of supply chain vulnerabilities

## Compliance

- Unit testing

## Notes

- Exploratory review and fix was done in ticket V2B-772
```