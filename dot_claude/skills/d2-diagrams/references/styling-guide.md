# D2 Styling Guide

Color schemes, themes, and visual conventions for professional diagrams.

## Recommended Color Palette

Based on Atlassian Design System colors for consistency with technical documentation.

```d2
vars: {
  colors: {
    # Neutrals
    white: "#FFFFFF"
    light-grey: "#F7F8F9"
    grey: "#F4F4F4"
    dark-grey: "#2C333A"
    black: "#000000"

    # Primary palette
    blue: "#85B8FF"
    dark-blue: "#0052CC"
    light-blue: "#DEEBFF"

    # Status colors
    green: "#7EE2B8"
    dark-green: "#006644"
    yellow: "#F5CD47"
    dark-yellow: "#FF8B00"
    red: "#FD9891"
    dark-red: "#DE350B"

    # Accent colors
    purple: "#B8ACF6"
    teal: "#9DD9EE"
    magenta: "#F797D2"
    lime: "#B3DF72"
    orange: "#FEC195"
  }
}
```

## Color Semantics

| Color | Hex | Use Case |
|-------|-----|----------|
| **Blue** | `#85B8FF` | Primary services, APIs, main components |
| **Purple** | `#B8ACF6` | External services, third-party integrations |
| **Teal** | `#9DD9EE` | Data stores, databases, caches |
| **Green** | `#7EE2B8` | Success states, healthy services |
| **Yellow** | `#F5CD47` | Warnings, conditional paths, pending |
| **Red** | `#FD9891` | Errors, failures, critical alerts |
| **Orange** | `#FEC195` | Actors, user-facing components |
| **Magenta** | `#F797D2` | Security, authentication, sensitive |
| **Lime** | `#B3DF72` | New features, additions |
| **Grey** | `#F4F4F4` | Deprecated, disabled, background |

## Standard Classes

Define these classes in a shared styles file:

```d2
classes: {
  # Service types
  primary-service: {
    style.fill: "#85B8FF"
    style.stroke: "#0052CC"
    style.stroke-width: 2
    style.border-radius: 8
  }

  external-service: {
    style.fill: "#B8ACF6"
    style.stroke: "#6554C0"
    style.stroke-width: 2
    style.stroke-dash: 5
    style.border-radius: 8
  }

  database: {
    shape: cylinder
    style.fill: "#9DD9EE"
    style.stroke: "#00A3BF"
    style.stroke-width: 2
  }

  queue: {
    shape: queue
    style.fill: "#FEC195"
    style.stroke: "#FF8B00"
    style.stroke-width: 2
  }

  # Actor types
  user: {
    shape: person
    style.fill: "#FD9891"
    style.stroke: "#DE350B"
  }

  system-actor: {
    shape: c4-person
    style.fill: "#F4F4F4"
    style.stroke: "#2C333A"
  }

  # Connection types
  sync-request: {
    style.stroke: "#0052CC"
    style.stroke-width: 2
  }

  async-message: {
    style.stroke: "#FF8B00"
    style.stroke-width: 2
    style.stroke-dash: 5
  }

  response: {
    style.stroke: "#2C333A"
    style.stroke-width: 2
    style.stroke-dash: 3
  }

  # Status indicators
  healthy: {
    style.fill: "#7EE2B8"
    style.stroke: "#006644"
  }

  warning: {
    style.fill: "#F5CD47"
    style.stroke: "#FF8B00"
  }

  error: {
    style.fill: "#FD9891"
    style.stroke: "#DE350B"
  }

  # Containers
  boundary: {
    style.fill: "#F7F8F9"
    style.stroke: "#2C333A"
    style.stroke-dash: 3
  }

  highlighted: {
    style.shadow: true
    style.stroke-width: 3
  }
}
```

## Theme Selection

| Theme ID | Name | Best For |
|----------|------|----------|
| 0 | Neutral Default | General use |
| 1 | Neutral Grey | Minimal distraction |
| 3 | Flagship Terrastruct | Branded presentations |
| 4 | Cool Classics | Technical docs |
| **5** | **Mixed Berry Muse** | **Recommended default** |
| 6 | Grape Soda | Dark accents |
| 7 | Aubergine | High contrast |
| 8 | Terminal | CLI documentation |
| 100 | Terminal Grayscale | Monochrome |
| 101 | Origami | Soft pastels |
| 102 | Everglade | Nature themed |

### Dark Themes

| Theme ID | Name |
|----------|------|
| 200 | Dark Mauve |
| 201 | Dark Flagship |
| 300-302 | Variations |

## CLI Theme Usage

```bash
# Light theme
d2 --layout=tala --theme=5 diagram.d2 output.png

# Auto dark mode switching
d2 --layout=tala --theme=5 --dark-theme=200 diagram.d2 output.svg

# In-file configuration
vars: {
  d2-config: {
    theme-id: 5
    dark-theme-id: 200
  }
}
```

## Sketch Mode

For whiteboard/informal aesthetics:

```bash
d2 --layout=tala --sketch diagram.d2 output.png
```

Or in-file:

```d2
vars: {
  d2-config: {
    sketch: true
  }
}
```

## Typography

### Font Sizes

| Element | Size | Use |
|---------|------|-----|
| Title | 20-24px | Diagram titles |
| Shape label | 14-16px | Default |
| Connection label | 12-14px | Edge labels |
| Note | 12px | Annotations |

```d2
title: {
  style.font-size: 24
  style.bold: true
}

service: {
  style.font-size: 16
}

a -> b: label {
  style.font-size: 12
}
```

### Font Weight

```d2
important: {
  style.bold: true
}

note: {
  style.italic: true
}
```

## Visual Hierarchy

### Z-Index via Opacity

```d2
# Background/context elements
background-system: {
  style.opacity: 0.5
}

# Primary focus
main-service: {
  style.opacity: 1.0
  style.shadow: true
}
```

### Border Radius

```d2
# Software components
service: { style.border-radius: 8 }

# Infrastructure
server: { style.border-radius: 0 }

# Abstract concepts
process: { style.border-radius: 16 }
```

## Connection Styling Conventions

| Pattern | Style | Meaning |
|---------|-------|---------|
| Solid | `stroke-dash: 0` | Synchronous, direct |
| Dashed | `stroke-dash: 5` | Asynchronous, indirect |
| Dotted | `stroke-dash: 2` | Response, callback |
| Animated | `animated: true` | Active data flow |

```d2
# Sync request
service-a -> service-b: REST API {
  style.stroke-dash: 0
}

# Async message
service-a -> queue: Publish {
  style.stroke-dash: 5
}

# Response
service-b -> service-a: Response {
  style.stroke-dash: 2
}
```

## Container Conventions

```d2
# System boundary (dashed)
system-boundary: {
  style.stroke-dash: 5
  style.fill: transparent
}

# Trust boundary (double)
trust-boundary: {
  style.double-border: true
  style.fill: "#FFEBE6"
}

# Cloud provider
aws: {
  style.fill: "#F7F8F9"
  style.border-radius: 16
  icon: https://icons.terrastruct.com/aws%2F_Group%20Icons%2FAWS-Cloud-alt_light-bg.svg
}
```
