---
name: code-commit
description: This skill should be used when the user asks to "commit changes", "commit my code", "/code-commit", "prepare a commit", "stage and commit", or needs to commit staged changes with security validation. Runs Trivy and Wiz scans for secrets and vulnerabilities, ensures quality checks pass (lint, format, test, build), splits large changes into atomic commits, and formats messages per conventional commits specification. Supports mise, npm, UV (Python), and Go toolchains. Invoke after completing a feature, fix, or any code modifications.
---

# Code Commit Skill

Commit code changes with quality gates, security validation, and conventional commit formatting.

## Pre-Commit Validation

Before any commit, run these checks:

1. **Format code** - Run formatters (prettier, gofmt, biome, etc.)
2. **Lint code** - Run linters to catch issues
3. **Run tests** - Ensure no regressions
4. **Verify build** - Confirm compilation succeeds
5. **Security scan** - Check for secrets and vulnerabilities

Discover available tasks:
```bash
mise tasks        # if mise.toml exists
npm run           # if package.json exists
make help         # if Makefile exists
uv run            # if pyproject.toml exists (Python/UV)
```

## Security Checks

Run security scans before committing. For detailed commands and patterns, see
`references/security-scanning.md`.

**Quick reference:**
```bash
# Trivy for secrets
trivy fs . --scanners secret --severity HIGH,CRITICAL

# Wiz for vulnerabilities (CRITICAL/HIGH only)
wizcli dir scan --secrets --policy cicd-vulnerabilities --policy cicd-secrets --policy-hits-only --path .

# Git for large/binary files
git diff --staged --numstat | awk '$1 == "-" {print "WARNING: Binary file:", $3}'
```

**Never commit:** `.env` files, credentials, API keys, private keys, `kubeconfig` files.

## Change Analysis

Before committing, understand the changes:

```bash
git status
git diff --staged
git diff --name-only
```

**Identify logical groupings:**
- Source code changes
- Test files
- Documentation
- Configuration
- Dependencies

## Atomic Commit Strategy

Split changes into atomic commits:

| Context | Commit Separately |
|---------|-------------------|
| Source code | Yes |
| Related tests | With source OR separate |
| Documentation | Separate |
| Configuration | Separate |
| Dependencies | Separate |

Each commit should:
- Represent one logical change
- Pass all tests independently
- Be independently buildable

## Conventional Commits Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, no code change
- `refactor`: Code restructuring
- `test`: Adding/modifying tests
- `chore`: Maintenance, dependencies
- `perf`: Performance improvements
- `ci`: CI/CD changes
- `build`: Build system changes
- `security`: Security fixes or improvements

**Writing guidelines:**
- Use active voice, direct language
- Sentence case (not Title Case)
- Complete the sentence: "This commit will..."
- Body explains WHY, not just WHAT
- Reference issues: `Fixes #123`

## Execution Workflow

**Step 1: Run security scans**
```bash
# Trivy for secrets
trivy fs . --scanners secret --severity HIGH,CRITICAL

# Wiz for vulnerabilities and secrets (CRITICAL/HIGH only)
wizcli dir scan --secrets --policy cicd-vulnerabilities --policy cicd-secrets --policy-hits-only --path .

# Git for large files and binaries
git diff --staged --numstat | awk '$1 == "-" {print "WARNING: Binary file:", $3}'
```

**Step 2: Run quality checks**

```bash
# Mise (polyglot)
mise run fmt && mise run lint && mise run test && mise run build

# Node.js
npm run format && npm run lint && npm test && npm run build

# Python (UV)
uv run ruff format . && uv run ruff check . && uv run pytest

# Go
go fmt ./... && golangci-lint run && go test ./... && go build ./...
```

**Step 3: Analyze changes**
```bash
git status
git diff --staged
```

**Step 4: Stage and commit atomically**
```bash
git add <related-files>
git commit -m "<type>(<scope>): <description>"
```

Repeat for each logical group.

## CI/CD Verification

After committing, verify CI/CD status if a PR exists. For detailed commands and failure patterns,
see `references/ci-verification.md`.

**Quick reference:**
```bash
gh run list --limit 5                                      # Check status
gh run view <run-id> --log-failed                          # Investigate failures
gh run rerun <run-id> --failed                             # Re-run flaky tests
```

**Re-run:** Flaky tests, transient network errors, runner issues.
**Fix:** Deterministic failures, lint/type errors, security vulnerabilities.

## Critical Rules

### NEVER:
- Use `--no-verify` flag
- Commit with failing linters or tests
- Commit secrets, API keys, or credentials
- Create large monolithic commits
- Commit files that should be ignored
- Use vague messages like "fix bug" or "update code"
- Commit unrelated changes together

### ALWAYS:
- Run security scans before committing
- Verify .gitignore compliance
- Run pre-commit hooks (let them pass)
- Split changes by logical context
- Write clear, conventional commit messages
- Ensure each commit is independently buildable
- Check `git diff` output before committing

## Error Handling

If any check fails:
1. Report the specific failure clearly
2. Identify the root cause
3. Fix the issue (do not bypass)
4. Re-run the check to confirm
5. Only proceed after all checks pass

If you cannot resolve an issue, explain the problem and ask for guidance.

## Output Format

When committing, report:

1. **Security scan results** - Trivy secrets, Wiz vulnerabilities (CRITICAL/HIGH only)
2. **Quality checks** - Pass/fail status for each
3. **Files to commit** - Grouped by logical context
4. **Commit strategy** - How changes will be split
5. **Commit messages** - Each message to be used
6. **Confirmation** - Successful commits with SHAs
7. **CI status** - Workflow status if PR exists

## Reference Files

For detailed guidance, consult:

- **`references/security-scanning.md`** - Trivy, Wiz, and git security scanning commands
- **`references/ci-verification.md`** - CI/CD status checks and failure resolution
