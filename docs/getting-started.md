# Getting started

## Prerequisites

Install these tools before bootstrapping:

| Tool | Purpose | Install |
|------|---------|---------|
| [1Password CLI](https://developer.1password.com/docs/cli/) | Secret injection | `brew install --cask 1password-cli` or [manual install](https://developer.1password.com/docs/cli/get-started/) |
| [chezmoi](https://www.chezmoi.io/install/) | Dotfile management | `brew install chezmoi` or `sh -c "$(curl -fsLS get.chezmoi.io)"` |
| [GitHub CLI](https://cli.github.com/) | Repo cloning, SSH key upload | `brew install gh` or `sudo apt install gh` |
| SSH key | Git signing, SSH access | Must exist at `~/.ssh/id_ed25519` |

## Bootstrap

### macOS

```bash
# Install Homebrew if missing
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Sign into 1Password
eval $(op signin)

# Init and apply
chezmoi init --apply https://github.com/phaestostech/phaestosdev-onprem-dotfiles.git
```

### Ubuntu / Debian

```bash
# Install chezmoi
sh -c "$(curl -fsLS get.chezmoi.io)"

# Sign into 1Password
eval $(op signin)

# Init and apply
chezmoi init --apply https://github.com/phaestostech/phaestosdev-onprem-dotfiles.git
```

## What happens during first run

### Machine identity prompts

Chezmoi prompts for four values stored in `~/.config/chezmoi/chezmoi.toml`:

| Prompt | Default | Effect |
|--------|---------|--------|
| `name` | Andru Manual-Che | Sets `git user.name` |
| `email` | andru@phaestos.tech | Sets `git user.email`, SSH allowed signers |
| `isWork` | false | Enables Slack MCP servers, AWS MCP servers, work SSH hosts |
| `isServer` | false | Disables Obsidian, desktop apps, personal SSH hosts |

Non-server machines also prompt for an Obsidian Local REST API key.

### run_once scripts

Chezmoi executes these scripts in order on first apply:

**Before scripts** (install dependencies):

1. `run_once_before_00-install-tailscale.sh.tmpl` -- installs Tailscale via brew (macOS) or curl (Linux)
2. `run_once_before_01-install-mise.sh.tmpl` -- installs mise and runs `mise install` for Go, Node, Python, uv
3. `run_once_before_02-install-packages-darwin.sh.tmpl` -- runs `brew bundle` from `~/.Brewfile` (macOS only)
4. `run_once_before_02-install-packages-linux.sh.tmpl` -- installs apt packages, Chrome, and Obsidian (Linux only)

**After scripts** (configure environment):

1. `run_once_after_create-dirs.sh.tmpl` -- creates `~/phaestostech`, `~/.local/bin`, and work directories
2. `run_once_after_clone-repos.sh.tmpl` -- clones standard repos via `gh repo clone`
3. `run_once_after_setup-git-ssh.sh.tmpl` -- creates `~/.ssh/allowed_signers` and uploads SSH key to GitHub

### 1Password requirement

All `run_once` scripts and templates that call `onepasswordRead` require an active 1Password session. Run `eval $(op signin)` before `chezmoi apply` if your session has expired.
