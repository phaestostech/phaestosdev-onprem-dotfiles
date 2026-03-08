# phaestosdev-onprem-dotfiles

Fleet dotfile management using [chezmoi](https://www.chezmoi.io/) with [1Password CLI](https://developer.1password.com/docs/cli/) for secret injection.

## Prerequisites

- [1Password CLI](https://developer.1password.com/docs/cli/) installed and signed in
- [chezmoi](https://www.chezmoi.io/install/) installed
- SSH key at `~/.ssh/id_ed25519`
- [GitHub CLI](https://cli.github.com/) authenticated

## Bootstrap a machine

```bash
# Sign into 1Password
eval $(op signin)

# Init and apply dotfiles
chezmoi init --apply https://github.com/phaestostech/phaestosdev-onprem-dotfiles.git
```

Chezmoi prompts for machine identity on first run:
- **name**: full name (default: Andru Manual-Che)
- **email**: email address (default: andru@phaestos.tech)
- **isWork**: work machine flag (enables Slack MCP servers, AWS configs)
- **isServer**: server flag (disables Obsidian, desktop apps)

## Day-to-day usage

```bash
chezmoi update          # Pull latest and apply
chezmoi diff            # Preview changes before applying
chezmoi edit ~/.zshrc   # Edit source, then apply
chezmoi apply           # Apply without pulling
```

## Documentation

See [docs/](docs/) for detailed guides.
