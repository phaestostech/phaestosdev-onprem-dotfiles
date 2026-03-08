# D2 Syntax Reference

Complete syntax reference for D2 diagramming language.

## Shapes

### Basic Shape Declaration

```d2
# Simple shape (rectangle by default)
service

# Shape with label
service: My Service

# Shape with type
database: PostgreSQL { shape: cylinder }
```

### Available Shape Types

| Shape | Use Case |
|-------|----------|
| `rectangle` | Services, components (default) |
| `square` | Equal-ratio boxes |
| `circle` | State nodes, endpoints |
| `oval` | Start/end states |
| `diamond` | Decision points |
| `hexagon` | Process steps |
| `parallelogram` | I/O operations |
| `cylinder` | Databases, storage |
| `queue` | Message queues |
| `package` | Packages, modules |
| `page` | Documents |
| `document` | Documentation |
| `cloud` | Cloud services, external systems |
| `person` | Users, actors |
| `c4-person` | C4 model actors |
| `stored_data` | Data stores |
| `step` | Process steps |
| `callout` | Notes, comments |

### Special Shapes

```d2
# SQL Table
users: {
  shape: sql_table
  id: int { constraint: primary_key }
  email: varchar(255) { constraint: unique }
  org_id: int { constraint: foreign_key }
}

# Class diagram
User: {
  shape: class
  +id: int
  +name: string
  -password: string
  +login(): bool
  +logout(): void
}

# Sequence diagram (container)
flow: {
  shape: sequence_diagram
  # actors and messages inside
}
```

## Connections

### Connection Types

```d2
a -> b           # Directed (right arrow)
a <- b           # Directed (left arrow)
a <-> b          # Bidirectional
a -- b           # Undirected
```

### Connection Labels

```d2
a -> b: HTTP GET
a -> b: {
  label: REST API
  style.stroke: "#85B8FF"
}
```

### Connection Chaining

```d2
user -> api -> service -> database
```

### Arrowhead Styles

```d2
a -> b: {
  source-arrowhead: diamond
  target-arrowhead: {
    shape: arrow
    label: "1"
  }
}
```

**Arrowhead shapes:** `triangle`, `arrow`, `diamond`, `circle`, `box`, `cf-one`, `cf-many`,
`cf-one-required`, `cf-many-required`

## Containers

### Nested Syntax

```d2
cloud: Cloud Provider {
  region: Region {
    vpc: VPC {
      service: Service
    }
  }
}
```

### Parent Reference

```d2
container: {
  child: {
    # Reference parent
    _.sibling -> sibling2
  }
  sibling
  sibling2
}
```

### Container Labels

```d2
# Method 1: Inline
vpc: Virtual Private Cloud

# Method 2: Label keyword
vpc: {
  label: Virtual Private Cloud
  subnet: Subnet
}
```

## Styling

### Inline Styles

```d2
service: {
  style.fill: "#85B8FF"
  style.stroke: "#0052CC"
  style.stroke-width: 2
  style.border-radius: 8
  style.opacity: 0.9
  style.shadow: true
  style.font-color: "#172B4D"
  style.font-size: 14
  style.bold: true
  style.italic: false
  style.underline: false
}
```

### Connection Styles

```d2
a -> b: {
  style.stroke: "#FF5630"
  style.stroke-width: 2
  style.stroke-dash: 5
  style.animated: true
}
```

### Classes (Reusable Styles)

```d2
classes: {
  primary: {
    style.fill: "#85B8FF"
    style.stroke: "#0052CC"
  }
  database: {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
  external: {
    style.stroke-dash: 5
    style.opacity: 0.7
  }
}

service.class: primary
postgres.class: database
third-party.class: external
```

### Multiple Classes

```d2
service.class: [primary, highlighted]
```

## Variables

### Defining Variables

```d2
vars: {
  env: production
  colors: {
    primary: "#85B8FF"
    secondary: "#B8ACF6"
  }
}
```

### Using Variables

```d2
service: ${env} Service {
  style.fill: ${colors.primary}
}
```

### Configuration Variables

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
    center: true
    sketch: false
    pad: 100
  }
}
```

## Icons

### Using Icons

```d2
# From URL
aws: AWS {
  icon: https://icons.terrastruct.com/aws%2F_Group%20Icons%2FAWS-Cloud-alt_light-bg.svg
}

# Local file (when using CLI)
service: {
  icon: ./icons/service.png
}
```

### Icon-Only Shapes

```d2
logo: {
  shape: image
  icon: ./logo.png
}
```

### Icon Library

Free icons at: https://icons.terrastruct.com

Categories: AWS, GCP, Azure, Kubernetes, Generic tech icons

## Text and Labels

### Markdown in Labels

```d2
explanation: |md
  # Title

  This is **bold** and *italic*.

  - List item 1
  - List item 2
|
```

### Code Blocks

```d2
snippet: |go
  func main() {
    fmt.Println("Hello")
  }
|
```

### Tooltips

```d2
service: Service {
  tooltip: This service handles authentication
}
```

## Layout Control

### Direction

```d2
direction: right  # left, right, up, down
```

### Per-Container Direction (TALA only)

```d2
horizontal-flow: {
  direction: right
  a -> b -> c
}

vertical-flow: {
  direction: down
  x -> y -> z
}
```

### Near Positioning

```d2
title: System Architecture {
  near: top-center
}

legend: {
  near: bottom-right
}
```

**Position values:** `top-left`, `top-center`, `top-right`, `center-left`, `center-right`,
`bottom-left`, `bottom-center`, `bottom-right`

### Absolute Positioning (TALA only)

```d2
element: {
  top: 100
  left: 200
}
```

## Globs (Bulk Operations)

### Apply to All Shapes

```d2
*: {
  style.border-radius: 8
}
```

### Apply to All Connections

```d2
* -> *: {
  style.stroke-width: 2
}
```

### Recursive Glob

```d2
**: {
  style.font-size: 14
}
```

## Composition

### Layers (Independent Boards)

```d2
# Base diagram
service -> database

layers: {
  detailed: {
    # Completely independent
    component-a -> component-b
  }
}
```

### Scenarios (Inherit from Base)

```d2
# Base state
service: Running

scenarios: {
  failure: {
    # Inherits base, modifies
    service: Failed {
      style.fill: red
    }
  }
}
```

### Steps (Sequential Inheritance)

```d2
# Step 1
a

steps: {
  step2: {
    # Inherits step 1
    b
    a -> b
  }
  step3: {
    # Inherits step 2
    c
    b -> c
  }
}
```

## Imports

```d2
# Import entire file
...@./common/styles.d2

# Import specific element
service: @./components/service.d2
```
