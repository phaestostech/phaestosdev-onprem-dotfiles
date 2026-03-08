---
name: bootstrap-go-cli
description: |
  Bootstrap and scaffold Go CLI projects with standardized structure, logging, and build tooling.
  Use when: (1) Creating a new Go CLI application, (2) User asks to "bootstrap", "scaffold", or
  "create" a Go CLI project, (3) User wants to set up a new command-line tool in Go, (4) Starting
  a new Go project that needs CLI argument parsing with Kong, (5) User mentions "go cli template"
  or "go cli boilerplate".
---

# Bootstrap Go CLI

Scaffold a Go CLI project with standardized structure following Harrison.ai patterns.

## Workflow

### Step 1: Gather requirements

Ask the user for:

1. **Module name** (required): The Go module path (e.g., `github.com/harrison-ai/my-cli`)
2. **main.go location**: Root (`./main.go`) or cmd directory (`./cmd/<cli-name>/main.go`)
3. **App name**: Derived from module name if not specified (last segment)
4. **App description**: Brief description for CLI help text

Example questions:
```
What is the Go module name? (e.g., github.com/harrison-ai/my-cli)
Where should main.go be placed?
  - Root directory (./main.go) - simpler for single-binary projects
  - cmd directory (./cmd/<name>/main.go) - better for multi-binary projects
```

### Step 2: Create directory structure

```
<project>/
├── main.go (or cmd/<app-name>/main.go)
├── go.mod
├── Makefile
├── mise.toml
├── .golangci.yml
├── .gitignore
├── bin/                    # Build output (gitignored)
├── internal/
│   ├── cli/               # CLI entrypoint
│   │   └── cli.go
│   ├── commands/          # Command implementations
│   │   └── version.go
│   ├── config/            # Configuration types
│   │   └── base.go
│   ├── constants/         # App constants
│   │   └── constants.go
│   └── types/             # Shared internal types
│       └── types.go
└── pkg/
    ├── build/             # Build info
    │   └── info.go
    ├── cli/               # Shared CLI utilities (colors, themes)
    │   ├── cli.go
    │   └── common.go
    ├── logger/            # Zap logger utilities
    │   └── logger.go
    ├── types/             # Exportable types
    │   └── types.go
    ├── utils/             # General utilities
    │   └── utils.go
    └── version/           # Version flag handling
        └── verflag.go
```

### Step 3: Generate files from templates

Use templates from `assets/templates/`. Replace placeholders:
- `{{.Module}}` - Go module path
- `{{.AppName}}` - CLI binary name (kebab-case)
- `{{.AppDescription}}` - Brief description
- `{{.GoVersion}}` - Go version (default: 1.25.6)
- `{{.MainPath}}` - Path to main.go (`.` or `./cmd/<name>`)

**Template selection based on main.go location:**
- Root: Use `main.go.tmpl`, `Makefile.tmpl`
- cmd/: Use `main_cmd.go.tmpl`, `Makefile_cmd.tmpl`

### Step 4: Initialize and verify

After creating files:
```bash
go mod tidy
go fmt ./...
make lint  # or: mise run lint
make build # or: mise run build
make run   # or: mise run run
```

## Dependencies

| Module | Version | Purpose |
|--------|---------|---------|
| github.com/alecthomas/kong | v1.13.0 | CLI argument parsing |
| github.com/charmbracelet/huh | v0.8.0 | Interactive forms |
| github.com/charmbracelet/lipgloss | v1.1.0 | Terminal styling |
| github.com/gookit/color | v1.6.0 | Colorized output |
| github.com/samber/lo | v1.52.0 | Utility functions |
| go.uber.org/zap | v1.27.0 | Structured logging |
| golang.org/x/text | v0.28.0 | Text utilities |

## Logging

Uses `go.uber.org/zap` with development/pretty config by default. Three initialization options:

```go
// Pretty console output (default)
log := logger.Init(false)

// JSON output for production
log := logger.Init(true)

// Custom configuration
cfg := &logger.Config{
    Level:      "debug",
    JSONOutput: false,
    TimeKey:    "timestamp",
    MessageKey: "message",
}
log := logger.InitCustom(cfg)
```

## Build tasks

Both Makefile and mise.toml provide identical tasks. **Prefer mise** for task execution.

| Task | Makefile | mise | Description |
|------|----------|------|-------------|
| Format | `make fmt` | `mise run fmt` | Format Go code |
| Lint | `make lint` | `mise run lint` | Run golangci-lint |
| Test | `make test` | `mise run test` | Run unit tests |
| Build | `make build` | `mise run build` | Build for current platform |
| Compile | `make compile` | `mise run compile` | Cross-compile all platforms |
| Run help | `make run` | `mise run run` | Run with --help |
| Run version | `make run-version` | `mise run run:version` | Run version command |
| Clean | `make clean` | `mise run clean` | Remove build artifacts |

## Adding commands

1. Create command file in `internal/commands/`:
```go
package commands

import "{{.Module}}/internal/config"

type MyCmd struct {
    Flag string `help:"Description" default:"value"`
}

func (c *MyCmd) Run(ctx *config.AppCtx) error {
    // Implementation
    return nil
}
```

2. Register in `internal/cli/cli.go`:
```go
var app struct {
    config.AppCtx
    Version commands.VersionCmd `cmd:"" name:"version" help:"Print version"`
    MyCmd   commands.MyCmd      `cmd:"" name:"mycmd" help:"My command"`
}
```