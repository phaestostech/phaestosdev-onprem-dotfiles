# Package management

Packages are installed automatically during the first `chezmoi apply` via `run_once` scripts. macOS and Linux use different package managers.

## macOS: Brewfile

The `dot_Brewfile` (rendered as `~/.Brewfile`) declares all Homebrew packages and casks. The `run_once_before_02-install-packages-darwin.sh.tmpl` script runs `brew bundle --file="$HOME/.Brewfile"` on first apply.

Current packages:

| Type | Packages |
|------|----------|
| Taps | `common-fate/granted`, `goreleaser/tap`, `terrastruct/tap` |
| Formulae | `openssl@3`, `xz`, `awscli`, `d2`, `fzf`, `gemini-cli`, `gh`, `git`, `git-lfs`, `glow`, `gum`, `k9s`, `kubectx`, `mise`, `neovim`, `opentofu`, `terraform`, `tmux`, `tree`, `trivy`, `uv`, `granted`, `goreleaser`, `tala` |
| Casks | `1password-cli`, `codex`, `gcloud-cli`, `google-chrome`, `arc`, `obsidian`, `jetbrains-toolbox` |

### Add a macOS package

1. Edit the source Brewfile:
   ```bash
   chezmoi edit ~/.Brewfile
   ```
2. Add the package using Homebrew bundle syntax:
   ```ruby
   brew "new-tool"
   # or
   cask "new-app"
   ```
3. Apply and install:
   ```bash
   chezmoi apply
   brew bundle --file="$HOME/.Brewfile"
   ```

The `run_once` script only runs on first apply. Run `brew bundle` manually after adding packages to an existing machine.

## Linux: apt packages

The `run_once_before_02-install-packages-linux.sh.tmpl` script installs packages via `apt-get` on first apply.

Current packages: `curl`, `git`, `git-lfs`, `build-essential`, `unzip`, `jq`, `fzf`, `ripgrep`, `htop`, `neovim`, `tmux`, `tree`, `wget`.

On non-server machines, the script also installs:
- **Google Chrome** -- downloaded from `dl.google.com`
- **Obsidian** -- downloaded from the latest GitHub release

### Add a Linux package

Edit the script source:

```bash
chezmoi edit ~/.local/share/chezmoi/run_once_before_02-install-packages-linux.sh.tmpl
```

Add the package to the `apt-get install` list. Since `run_once` scripts only execute once, run the script manually on existing machines or use `chezmoi state delete-bucket --bucket=scriptState` to force re-execution.

## mise: global tool versions

The `dot_config/mise/config.toml` file (rendered as `~/.config/mise/config.toml`) pins global tool versions managed by [mise](https://mise.jdx.dev/):

| Tool | Version |
|------|---------|
| Go | 1.26 |
| Node | 24 |
| Python | 3.14 |
| uv | latest |

The `run_once_before_01-install-mise.sh.tmpl` script installs mise and runs `mise install` on first apply.

### Add a mise tool

1. Edit the config:
   ```bash
   chezmoi edit ~/.config/mise/config.toml
   ```
2. Add the tool under `[tools]`:
   ```toml
   [tools]
   go = "1.26"
   node = "24"
   python = "3.14"
   uv = "latest"
   rust = "1.85"
   ```
3. Apply and install:
   ```bash
   chezmoi apply
   mise install
   ```

## Tailscale

Tailscale is installed separately by `run_once_before_00-install-tailscale.sh.tmpl`:
- **macOS**: `brew install --cask tailscale`
- **Linux**: `curl -fsSL https://tailscale.com/install.sh | sh`
