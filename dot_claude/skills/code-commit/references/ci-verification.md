# CI/CD Verification Reference

After committing, verify CI/CD status if a PR exists.

## Check Workflow Status

```bash
gh run list --limit 5
gh run view <run-id>
gh run list --branch $(git branch --show-current)
gh run list --branch $(git branch --show-current) --status failure
```

## Investigate Failures

```bash
gh run view <run-id> --log-failed
gh run view <run-id> --log
gh run download <run-id> --name <artifact-name>
```

## Common Failure Patterns

| Failure Type | Resolution |
|--------------|------------|
| Lint failures | Run linter locally, fix, create new commit |
| Test failures | Run tests with verbose output, identify flaky vs real |
| Build failures | Check dependencies, environment differences |
| Security scan | Review CVEs, update dependencies |
| Timeout | Check for hangs, infinite loops, split large suites |

## Branch Protection Awareness

```bash
gh api repos/{owner}/{repo}/branches/main/protection/required_status_checks
```

## When to Re-run vs Fix

**Re-run:** Flaky tests, transient network errors, runner issues

```bash
gh run rerun <run-id> --failed
```

**Fix:** Deterministic failures, lint/type errors, security vulnerabilities

## Resolution Workflow

1. Identify the failing step from logs
2. Reproduce locally if possible
3. Fix the root cause
4. Create a new commit (do not amend if already pushed)
5. Push and verify the fix in CI
