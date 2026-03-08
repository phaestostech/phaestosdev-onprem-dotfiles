# Flow Diagram Templates

Templates for data pipelines, process workflows, state machines, and decision flows.

## Basic Process Flow

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

direction: right

start: Start {
  shape: oval
  style.fill: "#7EE2B8"
}

step-1: Process Input {
  style.fill: "#85B8FF"
}

step-2: Validate Data {
  style.fill: "#85B8FF"
}

step-3: Transform {
  style.fill: "#85B8FF"
}

step-4: Store Result {
  shape: cylinder
  style.fill: "#9DD9EE"
}

end: End {
  shape: oval
  style.fill: "#FD9891"
}

start -> step-1 -> step-2 -> step-3 -> step-4 -> end
```

## Decision Flow (Flowchart)

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  start-end: {
    shape: oval
    style.fill: "#7EE2B8"
  }
  process: {
    style.fill: "#85B8FF"
  }
  decision: {
    shape: diamond
    style.fill: "#F5CD47"
  }
  io: {
    shape: parallelogram
    style.fill: "#B8ACF6"
  }
}

start: Start {
  class: start-end
}

input: Receive Request {
  class: io
}

validate: Validate Input {
  class: process
}

check-valid: Valid? {
  class: decision
}

process-data: Process Data {
  class: process
}

check-success: Success? {
  class: decision
}

store: Store Result {
  class: process
}

output-success: Return Success {
  class: io
}

output-error: Return Error {
  class: io
}

end: End {
  class: start-end
  style.fill: "#FD9891"
}

# Flow
start -> input -> validate -> check-valid

check-valid -> process-data: Yes
check-valid -> output-error: No

process-data -> check-success

check-success -> store: Yes
check-success -> output-error: No

store -> output-success

output-success -> end
output-error -> end
```

## Data Pipeline

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

direction: right

# Sources
sources: Data Sources {
  style.fill: "#F7F8F9"
  direction: down

  api: REST API {
    style.fill: "#85B8FF"
  }
  db: Database {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
  files: File Storage {
    style.fill: "#B8ACF6"
  }
}

# Ingestion
ingestion: Ingestion Layer {
  style.fill: "#F7F8F9"

  kafka: Kafka {
    shape: queue
    style.fill: "#FEC195"
  }
}

# Processing
processing: Processing Layer {
  style.fill: "#F7F8F9"

  spark: Spark Jobs {
    style.fill: "#85B8FF"
  }
}

# Storage
storage: Storage Layer {
  style.fill: "#F7F8F9"
  direction: down

  lake: Data Lake {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
  warehouse: Data Warehouse {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
}

# Serving
serving: Serving Layer {
  style.fill: "#F7F8F9"
  direction: down

  api-out: Analytics API {
    style.fill: "#85B8FF"
  }
  bi: BI Dashboard {
    style.fill: "#7EE2B8"
  }
}

# Connections
sources.api -> ingestion.kafka
sources.db -> ingestion.kafka
sources.files -> ingestion.kafka

ingestion.kafka -> processing.spark

processing.spark -> storage.lake
processing.spark -> storage.warehouse

storage.warehouse -> serving.api-out
storage.warehouse -> serving.bi
```

## ETL Pipeline

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

direction: right

# Extract
extract: Extract {
  style.fill: "#DEEBFF"
  style.stroke: "#0052CC"
  style.stroke-width: 2

  source-1: MySQL {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
  source-2: MongoDB {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
  source-3: S3 Files {
    style.fill: "#B8ACF6"
  }
}

# Transform
transform: Transform {
  style.fill: "#FFF7E6"
  style.stroke: "#FF8B00"
  style.stroke-width: 2

  clean: Data Cleaning {
    style.fill: "#85B8FF"
  }
  enrich: Enrichment {
    style.fill: "#85B8FF"
  }
  aggregate: Aggregation {
    style.fill: "#85B8FF"
  }
  validate: Validation {
    style.fill: "#F5CD47"
  }
}

# Load
load: Load {
  style.fill: "#E6FCFF"
  style.stroke: "#00A3BF"
  style.stroke-width: 2

  staging: Staging Tables {
    shape: cylinder
    style.fill: "#F5CD47"
  }
  target: Data Warehouse {
    shape: cylinder
    style.fill: "#7EE2B8"
  }
}

# Flow
extract.source-1 -> transform.clean
extract.source-2 -> transform.clean
extract.source-3 -> transform.clean

transform.clean -> transform.enrich -> transform.aggregate -> transform.validate

transform.validate -> load.staging -> load.target
```

## State Machine

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  state: {
    shape: rectangle
    style.border-radius: 16
    style.fill: "#85B8FF"
  }
  initial: {
    shape: circle
    style.fill: "#2C333A"
    width: 30
    height: 30
  }
  final: {
    shape: circle
    style.fill: "#FFFFFF"
    style.stroke: "#2C333A"
    style.stroke-width: 3
    width: 30
    height: 30
  }
}

title: "Order State Machine" {
  near: top-center
  style.font-size: 20
  style.bold: true
}

# Initial state
initial: {
  class: initial
}

# States
pending: Pending {
  class: state
  style.fill: "#F5CD47"
}

confirmed: Confirmed {
  class: state
  style.fill: "#85B8FF"
}

processing: Processing {
  class: state
  style.fill: "#B8ACF6"
}

shipped: Shipped {
  class: state
  style.fill: "#9DD9EE"
}

delivered: Delivered {
  class: state
  style.fill: "#7EE2B8"
}

cancelled: Cancelled {
  class: state
  style.fill: "#FD9891"
}

# Final state
final: {
  class: final
}

# Transitions
initial -> pending: create order

pending -> confirmed: payment received
pending -> cancelled: cancel

confirmed -> processing: start processing
confirmed -> cancelled: cancel

processing -> shipped: ship order
processing -> cancelled: cancel (refund)

shipped -> delivered: delivery confirmed

delivered -> final
cancelled -> final
```

## BPMN-Style Workflow

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  event-start: {
    shape: circle
    style.fill: "#7EE2B8"
    style.stroke: "#006644"
    style.stroke-width: 2
    width: 40
    height: 40
  }
  event-end: {
    shape: circle
    style.fill: "#FD9891"
    style.stroke: "#DE350B"
    style.stroke-width: 3
    width: 40
    height: 40
  }
  task: {
    style.fill: "#85B8FF"
    style.border-radius: 8
  }
  gateway: {
    shape: diamond
    style.fill: "#F5CD47"
  }
  subprocess: {
    style.fill: "#DEEBFF"
    style.stroke-dash: 3
  }
}

# Swimlanes
customer: Customer {
  style.fill: "#F7F8F9"
  direction: right

  start: {
    class: event-start
  }
  submit: Submit Order {
    class: task
  }
  receive: Receive Confirmation {
    class: task
  }
}

sales: Sales Team {
  style.fill: "#FFF7E6"
  direction: right

  review: Review Order {
    class: task
  }
  check: Inventory Check {
    class: gateway
  }
  approve: Approve Order {
    class: task
  }
  reject: Reject Order {
    class: task
  }
}

warehouse: Warehouse {
  style.fill: "#E6FCFF"
  direction: right

  pick: Pick Items {
    class: task
  }
  pack: Pack Order {
    class: task
  }
  ship: Ship Order {
    class: task
  }
  end: {
    class: event-end
  }
}

# Flow
customer.start -> customer.submit
customer.submit -> sales.review
sales.review -> sales.check

sales.check -> sales.approve: In Stock
sales.check -> sales.reject: Out of Stock

sales.approve -> warehouse.pick
sales.reject -> customer.receive

warehouse.pick -> warehouse.pack -> warehouse.ship
warehouse.ship -> customer.receive
customer.receive -> warehouse.end
```

## Event-Driven Flow

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  event: {
    shape: hexagon
    style.fill: "#FEC195"
  }
  command: {
    style.fill: "#85B8FF"
  }
  aggregate: {
    style.fill: "#B8ACF6"
    style.stroke-width: 2
  }
  projection: {
    style.fill: "#9DD9EE"
    shape: cylinder
  }
}

direction: down

# Commands
commands: Commands {
  style.fill: "#F7F8F9"
  direction: right

  create-order: CreateOrder {
    class: command
  }
  add-item: AddItem {
    class: command
  }
  submit: SubmitOrder {
    class: command
  }
}

# Aggregate
aggregate: Order Aggregate {
  class: aggregate

  validate: Validate
  apply: Apply Event
}

# Events
events: Domain Events {
  style.fill: "#F7F8F9"
  direction: right

  created: OrderCreated {
    class: event
  }
  item-added: ItemAdded {
    class: event
  }
  submitted: OrderSubmitted {
    class: event
  }
}

# Event Store
store: Event Store {
  shape: cylinder
  style.fill: "#2C333A"
  style.font-color: "#FFFFFF"
}

# Projections
projections: Read Models {
  style.fill: "#F7F8F9"
  direction: right

  order-view: Order View {
    class: projection
  }
  analytics: Analytics {
    class: projection
  }
}

# Flow
commands.create-order -> aggregate
commands.add-item -> aggregate
commands.submit -> aggregate

aggregate -> events.created
aggregate -> events.item-added
aggregate -> events.submitted

events.created -> store
events.item-added -> store
events.submitted -> store

store -> projections.order-view: Project {
  style.stroke-dash: 5
}
store -> projections.analytics: Project {
  style.stroke-dash: 5
}
```

## CI/CD Pipeline

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

direction: right

# Stages
commit: Commit Stage {
  style.fill: "#DEEBFF"

  checkout: Checkout
  lint: Lint
  unit-test: Unit Tests
}

build: Build Stage {
  style.fill: "#FFF7E6"

  compile: Compile
  docker: Build Image
  scan: Security Scan
}

test: Test Stage {
  style.fill: "#E6FCFF"

  integration: Integration Tests
  e2e: E2E Tests
  perf: Performance Tests
}

deploy: Deploy Stage {
  style.fill: "#F4F4F4"

  staging: Deploy to Staging {
    style.fill: "#F5CD47"
  }
  approval: Manual Approval {
    shape: diamond
    style.fill: "#B8ACF6"
  }
  prod: Deploy to Production {
    style.fill: "#7EE2B8"
  }
}

# Flow
commit.checkout -> commit.lint -> commit.unit-test
commit.unit-test -> build.compile
build.compile -> build.docker -> build.scan

build.scan -> test.integration
test.integration -> test.e2e -> test.perf

test.perf -> deploy.staging -> deploy.approval -> deploy.prod
```

## Error Handling Flow

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  process: { style.fill: "#85B8FF" }
  decision: { shape: diamond; style.fill: "#F5CD47" }
  error: { style.fill: "#FD9891" }
  success: { style.fill: "#7EE2B8" }
  retry: { style.fill: "#FEC195" }
}

start: Receive Request {
  class: process
}

process: Process Request {
  class: process
}

check: Success? {
  class: decision
}

# Success path
respond: Send Response {
  class: success
}

# Error handling
error-check: Retryable? {
  class: decision
}

increment: Increment Retry Count {
  class: retry
}

max-check: Max Retries? {
  class: decision
}

backoff: Exponential Backoff {
  class: retry
}

dlq: Send to DLQ {
  class: error
}

alert: Alert Operations {
  class: error
}

# Flow
start -> process -> check

check -> respond: Yes
check -> error-check: No

error-check -> increment: Yes
error-check -> dlq: No

increment -> max-check

max-check -> backoff: No
max-check -> dlq: Yes

backoff -> process: Retry {
  style.stroke-dash: 5
}

dlq -> alert
```
