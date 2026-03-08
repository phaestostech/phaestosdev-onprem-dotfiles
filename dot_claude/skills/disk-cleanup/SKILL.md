---
name: disk-cleanup
description: |
  Clean up disk space by removing regenerable caches and unused files. Use when:
  (1) User wants to free disk space or clean caches,
  (2) User mentions "disk full", "running out of space", "clean up",
  (3) User asks to clear node_modules, .terraform, venvs, UV cache, mise cache,
  (4) User wants to optimize git repositories with gc,
  (5) User mentions ~/Library/Caches or system caches.
  Provides interactive cleanup with user confirmation before deletions.
---

# Disk Cleanup

Clean up regenerable files and caches to free disk space. Always confirm with user before deleting.

## Quick Reference

| Target | Typical Size | Regenerable? |
|--------|--------------|--------------|
| node_modules | 100MB-2GB each | Yes (`npm install`) |
| .terraform | 100MB-1GB each | Yes (`terraform init`) |
| .terragrunt-cache | 100MB-1GB each | Yes (`terragrunt init`) |
| Python .venv | 50MB-500MB each | Yes (`uv sync` / `pip install`) |
| UV cache | 1-10GB | Yes (auto on next install) |
| Mise tools | 1-5GB | Yes (`mise install`) |
| ~/Library/Caches | 1-20GB | Mostly yes |
| Python __pycache__ | Small but many | Yes (auto-regenerated) |
| Build outputs (dist/build) | 10MB-500MB | Yes (rebuild) |
| .git directories | 50MB-1GB each | **No** - optimize only |

## Workflow

### 1. Assess Current Usage

```bash
df -h ~
du -h -d 1 <target-dir> 2>/dev/null | sort -hr | head -20
```

### 2. Find Deletable Directories

```bash
# Node modules
find ~ -type d -name "node_modules" -maxdepth 5 2>/dev/null | head -30 | \
  while read dir; do du -sh "$dir" 2>/dev/null; done | sort -hr

# Terraform/Terragrunt caches
find ~ -type d \( -name ".terraform" -o -name ".terragrunt-cache" \) 2>/dev/null | \
  while read dir; do du -sh "$dir" 2>/dev/null; done | sort -hr

# Python venvs
find ~ -type d \( -name "venv" -o -name ".venv" \) 2>/dev/null | \
  while read dir; do du -sh "$dir" 2>/dev/null; done | sort -hr

# Build outputs
find ~ -type d \( -name "dist" -o -name "build" -o -name "cdk.out" -o -name ".next" \) \
  -maxdepth 5 2>/dev/null | while read dir; do du -sh "$dir" 2>/dev/null; done | sort -hr | head -20
```

### 3. Check Tool Caches

```bash
du -sh ~/.cache/uv 2>/dev/null              # UV cache
du -sh ~/.local/share/mise 2>/dev/null      # Mise tools
mise ls --prunable 2>/dev/null               # Mise prunable versions
du -sh ~/Library/Caches 2>/dev/null          # macOS caches
du -h -d 1 ~/Library/Caches 2>/dev/null | sort -hr | head -20  # Breakdown
```

### 4. Delete with Confirmation

**Always present a summary table and ask user before deleting.**

```bash
# Bulk delete patterns (replace <path> with target)
find <path> -type d -name "node_modules" -maxdepth 4 2>/dev/null | xargs -I {} rm -rf "{}"
find <path> -type d -name ".terraform" 2>/dev/null | xargs -I {} rm -rf "{}"
find <path> -type d -name ".terragrunt-cache" 2>/dev/null | xargs -I {} rm -rf "{}"
find <path> -type d \( -name "venv" -o -name ".venv" \) 2>/dev/null | xargs -I {} rm -rf "{}"

# Python caches (safe - always regenerated)
find <path> -type d \( -name "__pycache__" -o -name ".pytest_cache" -o -name ".mypy_cache" -o -name ".ruff_cache" \) 2>/dev/null | xargs -I {} rm -rf "{}"

# Tool caches
uv cache clean --force
mise prune -y

# Library/Caches - show breakdown first, let user choose
du -h -d 1 ~/Library/Caches 2>/dev/null | sort -hr | head -20
rm -rf ~/Library/Caches/<specific-app>  # Only after user confirms
```

### 5. Git Optimization

```bash
# Find largest .git directories
find ~ -type d -name ".git" -maxdepth 3 2>/dev/null | \
  while read dir; do du -sh "$dir" 2>/dev/null; done | sort -hr | head -10

# Optimize (from repo root) - needs temp disk space
git gc --aggressive --prune=now
```

## Safety Rules

1. **Always confirm** before bulk deletions - present sizes first
2. **Never delete .git** - only optimize with `git gc`
3. **~/Library/Caches** - review subdirectories with user; some apps re-download large data
4. If `git gc` fails with "Out of diskspace", free space elsewhere first, then retry
