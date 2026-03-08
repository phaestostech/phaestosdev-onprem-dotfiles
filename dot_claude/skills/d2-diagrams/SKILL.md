---
name: d2-diagrams
description: Generate diagram-as-code using D2 language. Use when asked to "create a diagram", "draw architecture", "visualize flow", "make a sequence diagram", "C4 diagram", "/d2", "diagram this", or when visualizing systems, APIs, data flows, or component relationships. Creates PNG and SVG outputs using TALA layout engine.
---

# D2 Diagram Generation Skill

Generate professional diagrams-as-code using D2 language with TALA layout engine.

## Supported Diagram Types

| Type | Use Case | Reference |
|------|----------|-----------|
| **Architectural** | System components, service boundaries, deployments | `references/architectural-diagrams.md` |
| **Flow** | Data pipelines, process workflows, state machines | `references/flow-diagrams.md` |
| **Sequence** | API interactions, message flows, async operations | `references/sequence-diagrams.md` |
| **C4 Model** | Context, Container, Component, Code levels | `references/c4-diagrams.md` |

## Quick Start

Create a `.d2` file with diagram code, then render:

```bash
# Render PNG and SVG with TALA layout
d2 --layout=tala diagram.d2 diagram.png
d2 --layout=tala diagram.d2 diagram.svg
```

## Workflow

### Step 1: Understand Requirements

Clarify with the user:
- What system or process needs visualization?
- Which diagram type best represents the concept?
- What level of detail is needed?
- Are there existing conventions or styles to match?

### Step 2: Choose Diagram Type

| Need | Recommended Type |
|------|------------------|
| System overview | C4 Context or Architectural |
| Service interactions | C4 Container or Architectural |
| Internal structure | C4 Component |
| API/message flow | Sequence Diagram |
| Data pipeline | Flow Diagram |
| State transitions | Flow Diagram |
| Process steps | Flow Diagram |

### Step 3: Write D2 Code

Follow the templates in reference files. Key principles:
- Start with shapes and their relationships
- Group related components in containers
- Apply consistent styling via classes
- Use meaningful IDs (lowercase, hyphenated)

### Step 4: Render Output

```bash
# Set TALA license (should be in ~/.zshrc)
export TSTRUCT_TOKEN="your-token"

# Render to PNG (high quality)
d2 --layout=tala --theme=5 diagram.d2 diagram.png

# Render to SVG (scalable)
d2 --layout=tala --theme=5 diagram.d2 diagram.svg

# Sketch mode for whiteboard aesthetic
d2 --layout=tala --sketch diagram.d2 diagram.png
```

### Step 5: Iterate

Review with user and refine:
- Adjust layout direction if flow is unclear
- Add/remove detail based on feedback
- Tune colors and styling for clarity

## CLI Reference

```bash
d2 [flags] input.d2 [output.png|svg|pdf]

# Layout engines
--layout=tala    # Best for architecture (default if TSTRUCT_TOKEN set)
--layout=dagre   # Fast hierarchical (default)
--layout=elk     # Academic/mature

# Themes (numeric IDs)
--theme=0        # Neutral default
--theme=5        # Recommended for technical docs
--theme=8        # Terminal style (monospace)

# Output options
--sketch         # Hand-drawn aesthetic
--center         # Center diagram in canvas
--pad=100        # Padding around diagram

# Dark mode
--dark-theme=200 # Auto-switch to dark theme
```

## D2 Syntax Quick Reference

For complete syntax, see `references/d2-syntax.md`.

**Shapes:**
```d2
service: Service Name            # Rectangle (default)
db: Database { shape: cylinder }
user: User { shape: person }
```

**Connections:**
```d2
a -> b: label      # Directed
a -- b: label      # Undirected
a <-> b: label     # Bidirectional
a -> b -> c        # Chained
```

**Containers:**
```d2
cloud: AWS {
  vpc: VPC {
    subnet: Private Subnet {
      ec2: Instance
    }
  }
}
```

**Styling:**
```d2
classes: {
  primary: { style.fill: "#85B8FF" }
}
service.class: primary
```

## File Organization

For complex diagrams, use D2's composition features:

```
diagrams/
├── system-context.d2      # High-level overview
├── container.d2           # Service breakdown
├── sequence-auth.d2       # Auth flow sequence
├── common/
│   ├── styles.d2          # Shared styling
│   └── icons.d2           # Icon definitions
└── output/
    ├── system-context.png
    ├── system-context.svg
    └── ...
```

## Output Best Practices

1. **Always generate both PNG and SVG** - PNG for docs/presentations, SVG for web embedding
2. **Use consistent themes** - Theme 5 works well for technical documentation
3. **Enable sketch mode** for informal/whiteboard contexts
4. **Include padding** (`--pad=100`) for cleaner borders
5. **Center diagrams** (`--center`) for presentation slides

## Reference Files

- **`references/d2-syntax.md`** - Complete D2 syntax reference
- **`references/architectural-diagrams.md`** - System and infrastructure templates
- **`references/sequence-diagrams.md`** - UML sequence diagram templates
- **`references/c4-diagrams.md`** - C4 model templates at all levels
- **`references/flow-diagrams.md`** - Process and data flow templates
- **`references/styling-guide.md`** - Colors, themes, and visual conventions
