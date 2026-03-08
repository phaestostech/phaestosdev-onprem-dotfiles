# Sequence Diagram Templates

UML-style sequence diagrams for API interactions, message flows, and async operations.

## Basic Sequence Diagram

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
    center: true
  }

  colors: {
    actor: "#FD9891"
    service: "#85B8FF"
    external: "#B8ACF6"
    database: "#9DD9EE"
  }
}

classes: {
  response: {
    style.stroke-dash: 5
    style.stroke-width: 2
  }
  async: {
    style.stroke-dash: 3
    style.stroke: "#FF8B00"
  }
}

sequence: {
  shape: sequence_diagram

  # Actors
  user: User {
    shape: person
    style.fill: ${colors.actor}
  }

  # Services
  api: API Gateway {
    style.fill: ${colors.service}
  }

  auth: Auth Service {
    style.fill: ${colors.external}
  }

  db: Database {
    shape: cylinder
    style.fill: ${colors.database}
  }

  # Interactions
  user -> api: POST /login
  api -> auth: Validate credentials
  auth -> db: Query user
  db -> auth: User record {
    class: response
  }
  auth -> api: JWT token {
    class: response
  }
  api -> user: 200 OK + token {
    class: response
  }
}
```

## Authentication Flow

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  response: { style.stroke-dash: 5 }
  fragment: { style.opacity: 0.6 }
}

auth-flow: {
  shape: sequence_diagram

  client: Client App {
    style.fill: "#85B8FF"
  }
  gateway: API Gateway {
    style.fill: "#B8ACF6"
  }
  auth: Auth Service {
    style.fill: "#F797D2"
  }
  idp: Identity Provider {
    style.fill: "#B8ACF6"
  }
  db: User DB {
    shape: cylinder
    style.fill: "#9DD9EE"
  }

  # Login request
  client -> gateway: POST /auth/login

  # Credential validation
  gateway -> auth: Validate credentials
  auth -> db: Find user by email
  db -> auth: User record {
    class: response
  }

  # Password check - ALT fragment
  alt-valid: "ALT: Password valid" {
    class: fragment

    auth -> auth: Verify password hash
    auth -> idp: Generate tokens
    idp -> auth: Access + Refresh tokens {
      class: response
    }
    auth -> db: Update last_login
    auth -> gateway: Authentication success {
      class: response
    }
    gateway -> client: 200 OK + tokens {
      class: response
    }
  }

  alt-invalid: "ELSE: Invalid" {
    class: fragment

    auth -> gateway: Authentication failed {
      class: response
    }
    gateway -> client: 401 Unauthorized {
      class: response
    }
  }
}
```

## API Request/Response

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  response: { style.stroke-dash: 5 }
  async: { style.stroke-dash: 3; style.stroke: "#FF8B00" }
  fragment: { style.opacity: 0.6 }
}

api-flow: {
  shape: sequence_diagram

  client: Client {
    style.fill: "#FD9891"
  }
  lb: Load Balancer {
    style.fill: "#B8ACF6"
  }
  api: API Server {
    style.fill: "#85B8FF"
  }
  cache: Redis Cache {
    shape: cylinder
    style.fill: "#7EE2B8"
  }
  db: PostgreSQL {
    shape: cylinder
    style.fill: "#9DD9EE"
  }

  # Request
  client -> lb: GET /api/products/123

  # Load balancing
  lb -> api: Forward request

  # Cache check
  opt-cache: "OPT: Check cache first" {
    class: fragment

    api -> cache: GET product:123
    cache -> api: Cache hit/miss {
      class: response
    }
  }

  # Database query on cache miss
  alt-miss: "ALT: Cache miss" {
    class: fragment

    api -> db: SELECT * FROM products
    db -> api: Product data {
      class: response
    }
    api -> cache: SET product:123 {
      class: async
    }
  }

  # Response
  api -> lb: Product JSON {
    class: response
  }
  lb -> client: 200 OK {
    class: response
  }
}
```

## Async Message Processing

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  response: { style.stroke-dash: 5 }
  async: { style.stroke-dash: 3; style.stroke: "#FF8B00" }
  fragment: { style.opacity: 0.6 }
}

async-flow: {
  shape: sequence_diagram

  producer: Order Service {
    style.fill: "#85B8FF"
  }
  broker: Kafka {
    style.fill: "#FEC195"
  }
  consumer-1: Payment Service {
    style.fill: "#85B8FF"
  }
  consumer-2: Notification Service {
    style.fill: "#7EE2B8"
  }
  consumer-3: Analytics {
    style.fill: "#B8ACF6"
  }

  # Publish event
  producer -> broker: Publish OrderCreated {
    class: async
  }
  broker -> producer: ACK {
    class: response
  }

  # Parallel consumption
  par-consume: "PAR: Parallel processing" {
    class: fragment

    broker -> consumer-1: OrderCreated {
      class: async
    }
    consumer-1 -> consumer-1: Process payment
    consumer-1 -> broker: Publish PaymentProcessed {
      class: async
    }

    broker -> consumer-2: OrderCreated {
      class: async
    }
    consumer-2 -> consumer-2: Send confirmation email

    broker -> consumer-3: OrderCreated {
      class: async
    }
    consumer-3 -> consumer-3: Record metrics
  }
}
```

## WebSocket Connection

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  response: { style.stroke-dash: 5 }
  bidirectional: { style.stroke: "#6554C0"; style.stroke-width: 2 }
  fragment: { style.opacity: 0.6 }
}

websocket: {
  shape: sequence_diagram

  client: Browser {
    style.fill: "#FD9891"
  }
  server: WebSocket Server {
    style.fill: "#85B8FF"
  }
  pubsub: PubSub {
    style.fill: "#FEC195"
  }

  # Connection establishment
  client -> server: HTTP Upgrade: websocket
  server -> client: 101 Switching Protocols {
    class: response
  }

  # Subscribe to channel
  client -> server: Subscribe: room:123 {
    class: bidirectional
  }
  server -> pubsub: Subscribe room:123
  server -> client: Subscribed {
    class: response
  }

  # Loop for messages
  loop-messages: "LOOP: While connected" {
    class: fragment

    # Incoming message from another client
    pubsub -> server: Message: room:123
    server -> client: Push message {
      class: bidirectional
    }

    # Client sends message
    client -> server: Send: room:123 {
      class: bidirectional
    }
    server -> pubsub: Publish room:123
  }

  # Disconnect
  client -> server: Close connection
  server -> pubsub: Unsubscribe room:123
  server -> client: Connection closed {
    class: response
  }
}
```

## Saga Pattern (Distributed Transaction)

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  response: { style.stroke-dash: 5 }
  compensate: { style.stroke: "#DE350B"; style.stroke-dash: 5 }
  fragment: { style.opacity: 0.6 }
}

saga: {
  shape: sequence_diagram

  orchestrator: Saga Orchestrator {
    style.fill: "#B8ACF6"
  }
  order: Order Service {
    style.fill: "#85B8FF"
  }
  inventory: Inventory Service {
    style.fill: "#85B8FF"
  }
  payment: Payment Service {
    style.fill: "#85B8FF"
  }
  shipping: Shipping Service {
    style.fill: "#85B8FF"
  }

  # Start saga
  orchestrator -> order: Create order
  order -> orchestrator: Order created {
    class: response
  }

  orchestrator -> inventory: Reserve items
  inventory -> orchestrator: Items reserved {
    class: response
  }

  orchestrator -> payment: Process payment

  # Payment failure scenario
  alt-fail: "ALT: Payment fails" {
    class: fragment

    payment -> orchestrator: Payment failed {
      class: response
    }

    # Compensating transactions
    orchestrator -> inventory: Release reservation {
      class: compensate
    }
    inventory -> orchestrator: Released {
      class: response
    }

    orchestrator -> order: Cancel order {
      class: compensate
    }
    order -> orchestrator: Cancelled {
      class: response
    }
  }

  alt-success: "ELSE: Payment succeeds" {
    class: fragment

    payment -> orchestrator: Payment processed {
      class: response
    }

    orchestrator -> shipping: Create shipment
    shipping -> orchestrator: Shipment created {
      class: response
    }

    orchestrator -> order: Complete order
    order -> orchestrator: Order completed {
      class: response
    }
  }
}
```

## Fragment Types Reference

| Fragment | Purpose | Example |
|----------|---------|---------|
| **opt** | Optional execution | Cache check |
| **alt/else** | Conditional branches | Success/failure paths |
| **loop** | Repeated execution | Polling, retries |
| **par** | Parallel execution | Fan-out processing |
| **break** | Exit condition | Error handling |
| **critical** | Atomic region | Transaction |
| **neg** | Invalid scenario | Error case |
| **ignore** | Irrelevant messages | Filtered events |
| **consider** | Relevant subset | Specific handlers |
| **assert** | State assertion | Validation |
| **ref** | Reference another diagram | Shared subprocess |

## Activation Bars

Show when an actor is actively processing:

```d2
sequence: {
  shape: sequence_diagram

  client: Client
  server: Server

  # Create activation span with nested object
  client -> server.process: Request
  server.process -> server.process: Process internally
  server.process -> client: Response {
    class: response
  }
}
```

## Self-Messages

```d2
sequence: {
  shape: sequence_diagram

  service: Service

  # Self-message for internal processing
  service -> service: Validate input
  service -> service: Transform data
  service -> service: Apply business rules
}
```

## Notes and Comments

```d2
sequence: {
  shape: sequence_diagram

  client: Client
  server: Server

  client -> server: Request

  # Add note on lifeline
  server."Validate JWT token"
  server."Check rate limits"

  server -> client: Response {
    class: response
  }
}
```
