# Adding dotfiles

## Quick add

```bash
# Add a file to chezmoi source
chezmoi add ~/.config/some-tool/config.toml

# Edit, then apply
chezmoi edit ~/.config/some-tool/config.toml
chezmoi apply
```

`chezmoi add` copies the file into `~/.local/share/chezmoi/` with chezmoi naming conventions applied automatically.

## Naming conventions

Chezmoi uses filename prefixes to control how files are managed:

| Prefix | Meaning | Example |
|--------|---------|---------|
| `dot_` | Replaces with `.` in target path | `dot_zshrc` -> `.zshrc` |
| `private_` | Sets file permissions to `0600` | `private_dot_ssh/` -> `.ssh/` (mode 600) |
| `empty_` | Creates an empty file if missing | `empty_dot_placeholder` |
| `exact_` | Removes files in target dir not in source | `exact_dot_config/` |

Suffixes:

| Suffix | Meaning | Example |
|--------|---------|---------|
| `.tmpl` | Process as Go template | `dot_zshrc.tmpl` -> `.zshrc` (templated) |

## Templates

Add `.tmpl` to any file that needs dynamic content. Templates use Go's `text/template` syntax with chezmoi extensions.

Common template patterns in this repo:

```go
// Access machine identity data from .chezmoi.toml.tmpl
{{ .name }}
{{ .email }}
{{ .isWork }}
{{ .isServer }}

// OS-specific blocks
{{ if eq .chezmoi.os "darwin" -}}
# macOS-only config
{{ end -}}

// Hostname-specific blocks
{{ if eq .chezmoi.hostname "phaestosdev-ubuntu-1" -}}
# Host-specific config
{{ end -}}

// 1Password secret injection
{{ onepasswordRead "op://vault/item/field" }}
```

## Example workflow

Add a tool's config file with a secret:

1. Create the config file locally and verify it works
2. Add it to chezmoi:
   ```bash
   chezmoi add ~/.config/some-tool/config.yaml
   ```
3. If it contains secrets, convert to a template:
   ```bash
   # Rename the source file to add .tmpl suffix
   cd ~/.local/share/chezmoi
   mv dot_config/some-tool/config.yaml dot_config/some-tool/config.yaml.tmpl
   ```
4. Replace hardcoded secrets with `onepasswordRead` calls:
   ```yaml
   api_key: {{ onepasswordRead "op://phaestosdev-onprem/Some Tool API Key/credential" }}
   ```
5. Store the secret in 1Password under the `phaestosdev-onprem` vault
6. Test the template:
   ```bash
   chezmoi diff
   chezmoi apply --dry-run --verbose
   chezmoi apply
   ```
7. Commit the source file (never the rendered output)

## Directories

Chezmoi manages directories the same way. The `dot_config/` directory in source maps to `~/.config/` in the target. Nested directories follow the same prefix rules:

```
~/.local/share/chezmoi/
  dot_config/
    mise/
      config.toml           -> ~/.config/mise/config.toml
  private_dot_ssh/
    config.tmpl             -> ~/.ssh/config (mode 600, templated)
```

## External files

Files managed outside the repo (like oh-my-zsh) are declared in `.chezmoiexternal.toml`. Chezmoi downloads and extracts these archives automatically. See the existing entries for oh-my-zsh plugins and zsh-bd as examples.
