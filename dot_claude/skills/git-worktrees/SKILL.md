---
name: git-worktrees
description: >-
  Create isolated git worktrees for feature development with automatic config file copying.
  Use when: (1) starting feature work needing isolation, (2) before executing implementation
  plans, (3) user says "create worktree", "new branch workspace", or "isolated workspace".
  Handles directory selection, .gitignore verification, dependency installation, config
  file copying from source worktree, and baseline test verification.
---

# Git Worktrees

Create isolated workspaces sharing the same repository.

**Announce:** "Using git-worktrees skill to set up an isolated workspace."

## Workflow

1. Select directory (existing > CLAUDE.md preference > ask user)
2. Verify .gitignore for project-local directories
3. Create worktree and branch
4. Copy config files from source worktree
5. Install dependencies
6. Verify baseline tests
7. Report ready

## Step 1: Directory Selection

Check in priority order:

```bash
ls -d .worktrees 2>/dev/null       # First choice
ls -d ../worktrees 2>/dev/null     # Second
ls -d ~/.worktrees 2>/dev/null     # Third
```

If none exist, check CLAUDE.md for preference, then ask user.

## Step 2: Safety Verification

For project-local directories only:

```bash
git check-ignore -q .worktrees 2>/dev/null
```

If not ignored: add to .gitignore and commit before proceeding.

## Step 3: Create Worktree

```bash
project=$(basename "$(git rev-parse --show-toplevel)")
clean_branch=$(echo "$BRANCH" | sed 's/\//-/g')
git worktree add "$path" -b "$BRANCH"
```

## Step 4: Copy Config Files

**Critical:** Local config files (.env, YAML, TOML, JSON configs) are not tracked in git.
Copy them from source worktree to enable the new worktree to function.

### 4a. Check CLAUDE.md for explicit config requirements

```bash
grep -A 50 -i "git worktree\|worktree config\|files to copy" .claude/CLAUDE.md CLAUDE.md 2>/dev/null
```

If CLAUDE.md specifies config files (like the example below), copy exactly those files:

```markdown
# Example CLAUDE.md section:
## Git worktree
### Files to copy to new worktree
- .env
- rad-cockpit-ui/.env
- rad-cockpit-ui/public/app.config.yml
- rad-cockpit-bff/.env
- rad-cockpit-bff/config/config.local.yml
```

### 4b. Auto-detect config files if no CLAUDE.md guidance

Search for untracked config files:

```bash
# Find .env files (any depth)
find . -name ".env" -o -name ".env.local" -o -name ".env.development" 2>/dev/null | \
  xargs -I{} git check-ignore -q {} 2>/dev/null && echo {}

# Find local config files (common patterns)
find . -name "*.local.yml" -o -name "*.local.yaml" -o -name "*.local.toml" \
  -o -name "*.local.json" -o -name "config.local.*" 2>/dev/null

# Find app config in public directories
find . -path "*/public/*.yml" -o -path "*/public/*.yaml" \
  -o -path "*/public/*.json" 2>/dev/null
```

### 4c. Copy config files

```bash
copy_config() {
  local src="$1" dst="$2"
  if [ -f "$src" ]; then
    mkdir -p "$(dirname "$dst")"
    cp "$src" "$dst"
    echo "Copied: ${src#./}"
  fi
}

# For each config file found:
copy_config "$SOURCE/.env" "$TARGET/.env"
copy_config "$SOURCE/subdir/.env" "$TARGET/subdir/.env"
# ... etc
```

### Config file patterns by project type

| Project Type | Common Config Locations |
|--------------|------------------------|
| Node.js | `.env`, `*.local.js`, `public/*.json` |
| Go | `.env`, `config/*.local.yml`, `config/*.local.yaml` |
| Python | `.env`, `*.local.toml`, `config/*.local.yml` |
| Rust | `.env`, `*.local.toml` |
| Monorepo | Root `.env` + per-package `.env` files |

## Step 5: Install Dependencies

Auto-detect from project files:

```bash
[ -f mise.toml ] || [ -f .mise.toml ] && mise install
[ -f package.json ] && npm install
[ -f go.mod ] && go mod download
[ -f Cargo.toml ] && cargo build
[ -f requirements.txt ] && pip install -r requirements.txt
[ -f pyproject.toml ] && uv sync
```

## Step 6: Verify Baseline

Run project-appropriate tests. If tests fail, report and ask whether to proceed.

## Step 7: Report

```
Worktree ready at <full-path>
Config files copied: .env, subdir/.env, config/app.local.yml
Tests passing (N tests)
Ready to implement <feature>
```

## Example

```
Using git-worktrees skill to set up an isolated workspace.

[Directory: .worktrees/ exists, verified ignored]
[Created: .worktrees/feature-auth from branch feature/auth]
[CLAUDE.md specifies config files - copying 4 files]
  Copied: .env
  Copied: rad-cockpit-ui/.env
  Copied: rad-cockpit-ui/public/app.config.yml
  Copied: rad-cockpit-bff/.env
[Ran: mise install, npm install]
[Tests: 47 passing]

Worktree ready at /project/.worktrees/feature-auth
Ready to implement auth feature
```

