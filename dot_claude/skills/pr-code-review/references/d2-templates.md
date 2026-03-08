# D2 Diagram Templates

Use D2 Lang diagrams for visualizing architectural and flow changes in PR reviews.

## Sequence Diagram Template

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
    center: true
    sketch: true
  }

  colors: {
    white: "#F7F8F9"
    grey: "#F4F4F4"
    darkgrey: "#2C333A"
    black: "#000000"
    lime: "#B3DF72"
    red: "#FD9891"
    orange: "#FEC195"
    yellow: "#F5CD47"
    green: "#7EE2B8"
    teal: "#9DD9EE"
    blue: "#85B8FF"
    purple: "#B8ACF6"
    magenta: "#F797D2"
  }
}

classes: {
  response: {
    style.stroke-dash: 5
    style.stroke: ${colors.black}
    style.stroke-width: 2
  }
  fragment: {
    style.opacity: 0.5
  }
}

sequence: {
  shape: sequence_diagram

  # Actors - use different colors for different services
  actor: "Actor" {
    style.fill: ${colors.red}
    style.stroke: ${colors.red}
  }
  service_a: "Service A" {
    style.fill: ${colors.blue}
    style.stroke: ${colors.blue}
  }
  service_b: "Service B"
  database
  cache

  # Option fragment - for conditional flows
  opt1: "OPT: If actor not logged in" {
    class: fragment
    actor -> service: login
  }

  # Activation bar (open)
  actor -> service.a1: request

  # Alternate fragment - for if/else flows
  altIf1: "IF: external caching is enabled" {
    class: fragment
    service_a -> cache: store in external cache
  }
  altElse1: "ELSE:" {
    class: fragment
    service_a -> service: store in-memory
  }

  # Comment on lifeline
  service_a."This is a comment"

  # Synchronous message
  service.a1 -> database: query

  # Return message (dashed)
  database -> service.a1: response {
    class: response
  }

  # Asynchronous message
  service_a -> service_b: request long-running operation

  # Loop fragment
  loop1: "LOOP: every 10 secs" {
    class: fragment
    service_a -> service_c: check side-effect long-running operation has completed
  }

  service_a -> actor: result
}
```

## ASCII Art Alternative

For simpler relationships when D2 is not needed:

```text
┌─────────────┐     ┌─────────────┐
│  Component  │────▶│  Component  │
└─────────────┘     └─────────────┘
```

## When to Use Each

| Diagram Type | Use Case |
|--------------|----------|
| D2 Sequence | Multi-service interactions, API flows, async operations |
| D2 Architecture | System components, service boundaries, data flow |
| ASCII Art | Simple relationships, inline comments, quick sketches |

## Color Guidelines

- **Red** (`#FD9891`): Actors, user-facing components
- **Blue** (`#85B8FF`): Primary services, APIs
- **Green** (`#7EE2B8`): Success paths, healthy states
- **Yellow** (`#F5CD47`): Warnings, conditional logic
- **Purple** (`#B8ACF6`): External services, third-party integrations
- **Teal** (`#9DD9EE`): Data stores, caches

## Fragment Types

- **OPT**: Optional execution (if condition)
- **ALT**: Alternative paths (if/else)
- **LOOP**: Repeated execution
- **PAR**: Parallel execution
- **BREAK**: Exit condition
