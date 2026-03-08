# Hostname overrides

Three patterns handle per-host configuration. Use whichever fits the situation.

## 1. Template guards in `.tmpl` files

Add conditional blocks directly in templated config files using `chezmoi.hostname`, `chezmoi.os`, or custom data variables.

**OS-based guards** (used in `dot_gitconfig.tmpl`):

```go
{{ if eq .chezmoi.os "darwin" -}}
[credential]
  helper = osxkeychain
{{ else -}}
[credential "https://github.com"]
  helper =
  helper = !/usr/bin/gh auth git-credential
{{ end -}}
```

**Data variable guards** (used in `dot_zshrc.tmpl`, `private_dot_ssh/config.tmpl`):

```go
{{ if .isWork -}}
# Work-only configuration here
{{ end -}}

{{ if not .isServer -}}
# Workstation-only configuration here
{{ end -}}
```

**Hostname-specific guards**:

```go
{{ if eq .chezmoi.hostname "phaestosdev-ubuntu-1" -}}
# Config specific to this host
{{ end -}}
```

Use template guards when the difference is a few lines within a larger config file.

## 2. Hostname-specific files via `.chezmoiignore`

The `.chezmoiignore` file controls which source files chezmoi applies based on template conditions. Files listed in `.chezmoiignore` are skipped during `chezmoi apply`.

Current ignore rules:

```go
# Skip Brewfile on non-macOS machines
{{ if ne .chezmoi.os "darwin" }}
.Brewfile
{{ end }}

# Skip Obsidian config on servers
{{ if .isServer }}
.config/obsidian/
{{ end }}
```

Use `.chezmoiignore` when entire files or directories should only exist on certain machines.

To add a hostname-specific ignore:

```go
{{ if ne .chezmoi.hostname "phaestosdev-ubuntu-1" }}
.config/some-tool/
{{ end }}
```

## 3. Per-host `~/.zshrc_host` sourced from main `.zshrc`

The managed `.zshrc` sources a local `~/.zshrc_host` file if it exists:

```go
{{ if stat (joinPath .chezmoi.homeDir ".zshrc_host") -}}
source ~/.zshrc_host
{{ end -}}
```

This file is **not managed by chezmoi**. Create it manually on any machine to add host-specific shell configuration without modifying the shared dotfiles.

```bash
# Create host-local overrides
cat >> ~/.zshrc_host << 'EOF'
# Host-specific aliases or exports
export CUSTOM_VAR="value"
EOF
```

Use `~/.zshrc_host` for one-off shell customisations that do not belong in the shared repo.

## Which pattern to use

| Situation | Pattern |
|-----------|---------|
| A few lines differ in a shared config | Template guards |
| An entire file or directory is host-specific | `.chezmoiignore` |
| One-off shell overrides on a single machine | `~/.zshrc_host` |
