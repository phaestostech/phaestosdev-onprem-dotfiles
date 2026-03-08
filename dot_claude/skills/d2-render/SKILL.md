---
name: d2-render
description: Start D2 diagram live preview server with hot reload. Use when asked to "render diagram",
  "preview d2", "live reload diagram", "/d2-render", "serve diagram", "watch diagram", or when wanting
  to interactively develop a D2 diagram with real-time browser preview.
---

# D2 Render

Start a live preview server for D2 diagrams with automatic hot reload on file changes.

## Workflow

### Step 1: Get the D2 file path

Use `AskUserQuestion` to prompt for the input `.d2` file path. The user should provide an absolute or relative path
to an existing D2 diagram file.

### Step 2: Select layout engine

Use `AskUserQuestion` to prompt for the layout engine:

| Engine | Description |
|--------|-------------|
| `tala` | Terrastruct's AutoLayout Approach (Recommended for complex diagrams) |
| `dagre` | Directed graph layout library (default, good for simple diagrams) |
| `elk` | Eclipse Layout Kernel with Layered algorithm |

### Step 3: Check tala requirements (if selected)

If the user selects `tala`:

1. Check if `TSTRUCT_TOKEN` environment variable is set
2. If not set, use `AskUserQuestion` to either:
   - Have the user provide the token (they can set it with `export TSTRUCT_TOKEN=<token>`)
   - Select a different layout engine (dagre or elk)

### Step 4: Select theme

Use `AskUserQuestion` to prompt for the theme. Common options to offer:

**Light themes (recommended first):**
| ID | Name |
|----|------|
| 0 | Neutral Default |
| 1 | Neutral Grey |
| 3 | Flagship Terrastruct |
| 8 | Colorblind Clear |
| 300 | Terminal |
| 302 | Origami |

**Dark themes:**
| ID | Name |
|----|------|
| 200 | Dark Mauve |
| 201 | Dark Flagship Terrastruct |

If the user selects "Other", provide the full list:

**All light themes:**
| ID | Name |
|----|------|
| 0 | Neutral Default |
| 1 | Neutral Grey |
| 3 | Flagship Terrastruct |
| 4 | Cool Classics |
| 5 | Mixed Berry Blue |
| 6 | Grape Soda |
| 7 | Aubergine |
| 8 | Colorblind Clear |
| 100 | Vanilla Nitro Cola |
| 101 | Orange Creamsicle |
| 102 | Shirley Temple |
| 103 | Earth Tones |
| 104 | Everglade Green |
| 105 | Buttered Toast |
| 300 | Terminal |
| 301 | Terminal Grayscale |
| 302 | Origami |
| 303 | C4 |

**All dark themes:**
| ID | Name |
|----|------|
| 200 | Dark Mauve |
| 201 | Dark Flagship Terrastruct |

### Step 5: Validate the file

Before starting the server:

1. Verify the file exists using `Read` tool or `ls`
2. Verify the file has a `.d2` extension

If validation fails, inform the user and ask for a corrected path.

### Step 6: Start the live preview server as a background task

Run the D2 watch command using the Bash tool with `run_in_background: true`:

**Command:**
```bash
source ~/.zshrc && d2 --layout=<engine> --theme=<theme_id> --center --watch <file_path>
```

**CRITICAL:** When calling the Bash tool, set the `run_in_background` parameter to `true`. This runs the server as
a Claude Code background task, allowing the conversation to continue while the server runs.

**Default flags:**
- `--center` is always included (centers the diagram in the viewport)
- Only omit `--center` if the user explicitly requests not to center the image

**Example Bash tool call:**
```json
{
  "command": "source ~/.zshrc && d2 --layout=tala --theme=0 --center --watch docs/references/diagrams/fhircast.d2",
  "run_in_background": true,
  "description": "Start D2 live preview server"
}
```

The `--watch` flag automatically:
- Starts a local development server (typically on port 54521)
- Opens the default browser with the rendered SVG
- Watches the file for changes and hot-reloads the preview

### Step 7: Inform the user

After starting the background task, tell the user:
- The browser should open automatically with the diagram preview
- Any changes to the `.d2` file will automatically reload in the browser
- The server is running as a background task (provide the task ID)
- To check server output: use `TaskOutput` tool with the task ID
- To stop the server: use `TaskStop` tool with the task ID, or press `Ctrl+C` if viewing in terminal

## Notes

- The D2 CLI must be installed (`brew install d2` on macOS)
- For `tala` layout engine, a valid `TSTRUCT_TOKEN` is required (get from Terrastruct)
- The `source ~/.zshrc` ensures environment variables like `TSTRUCT_TOKEN` are loaded
- The server runs as a background task so the conversation can continue
- Default port is 54521, but D2 will find an available port if occupied
- The `--center` flag is always included unless the user explicitly requests otherwise