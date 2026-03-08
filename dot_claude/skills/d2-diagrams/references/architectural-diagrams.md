# Architectural Diagram Templates

Templates for system architecture, infrastructure, and deployment diagrams.

## Basic System Architecture

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
    center: true
  }
}

direction: right

# External actors
user: User {
  shape: person
  style.fill: "#FD9891"
}

# Load balancer
lb: Load Balancer {
  style.fill: "#B8ACF6"
  icon: https://icons.terrastruct.com/aws%2FNetworking%20&%20Content%20Delivery%2FAmazon-Elastic-Load-Balancing.svg
}

# Application tier
app: Application Tier {
  style.fill: "#F7F8F9"

  api-1: API Server 1 {
    style.fill: "#85B8FF"
  }
  api-2: API Server 2 {
    style.fill: "#85B8FF"
  }
}

# Data tier
data: Data Tier {
  style.fill: "#F7F8F9"

  postgres: PostgreSQL {
    shape: cylinder
    style.fill: "#9DD9EE"
  }

  redis: Redis Cache {
    shape: cylinder
    style.fill: "#7EE2B8"
  }
}

# Connections
user -> lb: HTTPS
lb -> app.api-1
lb -> app.api-2
app.api-1 -> data.postgres
app.api-2 -> data.postgres
app.api-1 -> data.redis
app.api-2 -> data.redis
```

## Microservices Architecture

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

direction: down

# API Gateway
gateway: API Gateway {
  style.fill: "#B8ACF6"
  style.stroke: "#6554C0"
}

# Services
services: Services {
  style.fill: "#F7F8F9"
  direction: right

  auth: Auth Service {
    style.fill: "#F797D2"
  }

  user: User Service {
    style.fill: "#85B8FF"
  }

  order: Order Service {
    style.fill: "#85B8FF"
  }

  payment: Payment Service {
    style.fill: "#85B8FF"
  }

  notification: Notification Service {
    style.fill: "#7EE2B8"
  }
}

# Message broker
broker: Message Broker {
  shape: queue
  style.fill: "#FEC195"
}

# Databases
datastores: Data Stores {
  style.fill: "#F7F8F9"
  direction: right

  user-db: Users DB {
    shape: cylinder
    style.fill: "#9DD9EE"
  }

  order-db: Orders DB {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
}

# Connections
gateway -> services.auth: Authenticate
gateway -> services.user
gateway -> services.order
gateway -> services.payment

services.order -> broker: Order Events {
  style.stroke-dash: 5
}
broker -> services.notification: Subscribe {
  style.stroke-dash: 5
}
broker -> services.payment: Subscribe {
  style.stroke-dash: 5
}

services.user -> datastores.user-db
services.order -> datastores.order-db
```

## Cloud Infrastructure (AWS)

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

aws: AWS Cloud {
  style.fill: "#F7F8F9"
  icon: https://icons.terrastruct.com/aws%2F_Group%20Icons%2FAWS-Cloud-alt_light-bg.svg

  region: ap-southeast-2 {
    style.fill: "#DEEBFF"

    vpc: VPC 10.0.0.0/16 {
      style.fill: "#FFFFFF"
      style.stroke-dash: 3

      public: Public Subnets {
        style.fill: "#E6FCFF"

        alb: Application Load Balancer {
          style.fill: "#B8ACF6"
        }

        nat: NAT Gateway {
          style.fill: "#B8ACF6"
        }
      }

      private: Private Subnets {
        style.fill: "#F7F8F9"

        ecs: ECS Cluster {
          style.fill: "#85B8FF"

          service-1: Service 1
          service-2: Service 2
        }
      }

      data: Data Subnets {
        style.fill: "#FFF7E6"

        rds: RDS PostgreSQL {
          shape: cylinder
          style.fill: "#9DD9EE"
        }

        elasticache: ElastiCache {
          shape: cylinder
          style.fill: "#7EE2B8"
        }
      }
    }
  }
}

# External
internet: Internet {
  shape: cloud
  style.fill: "#F4F4F4"
}

# Connections
internet -> aws.region.vpc.public.alb: HTTPS
aws.region.vpc.public.alb -> aws.region.vpc.private.ecs
aws.region.vpc.private.ecs -> aws.region.vpc.data.rds
aws.region.vpc.private.ecs -> aws.region.vpc.data.elasticache
aws.region.vpc.private.ecs -> aws.region.vpc.public.nat: Outbound
```

## Kubernetes Deployment

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

k8s: Kubernetes Cluster {
  style.fill: "#F7F8F9"
  icon: https://icons.terrastruct.com/azure%2FCompute%20Service%20Color%2FKubernetes%20Services.svg

  ingress: Ingress Controller {
    style.fill: "#B8ACF6"
  }

  ns-app: namespace: app {
    style.fill: "#DEEBFF"
    style.stroke-dash: 3

    deploy-api: Deployment: api {
      style.fill: "#85B8FF"

      pod-1: Pod
      pod-2: Pod
      pod-3: Pod
    }

    svc-api: Service: api {
      style.fill: "#7EE2B8"
    }

    hpa: HPA {
      style.fill: "#F5CD47"
    }
  }

  ns-data: namespace: data {
    style.fill: "#FFF7E6"
    style.stroke-dash: 3

    sts-db: StatefulSet: postgres {
      shape: cylinder
      style.fill: "#9DD9EE"
    }

    pvc: PVC {
      style.fill: "#F4F4F4"
    }
  }
}

# Connections
k8s.ingress -> k8s.ns-app.svc-api
k8s.ns-app.svc-api -> k8s.ns-app.deploy-api.pod-1
k8s.ns-app.svc-api -> k8s.ns-app.deploy-api.pod-2
k8s.ns-app.svc-api -> k8s.ns-app.deploy-api.pod-3
k8s.ns-app.hpa -> k8s.ns-app.deploy-api: Scale {
  style.stroke-dash: 5
}
k8s.ns-app.deploy-api -> k8s.ns-data.sts-db
k8s.ns-data.sts-db -> k8s.ns-data.pvc
```

## Event-Driven Architecture

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

direction: right

# Producers
producers: Event Producers {
  style.fill: "#F7F8F9"
  direction: down

  web-app: Web Application {
    style.fill: "#85B8FF"
  }

  mobile-app: Mobile App {
    style.fill: "#85B8FF"
  }

  iot: IoT Devices {
    style.fill: "#B8ACF6"
  }
}

# Event backbone
events: Event Backbone {
  style.fill: "#FFF7E6"

  kafka: Kafka Cluster {
    shape: queue
    style.fill: "#FEC195"

    topic-orders: orders
    topic-users: users
    topic-events: events
  }
}

# Consumers
consumers: Event Consumers {
  style.fill: "#F7F8F9"
  direction: down

  analytics: Analytics Service {
    style.fill: "#7EE2B8"
  }

  search: Search Indexer {
    style.fill: "#7EE2B8"
  }

  notifications: Notification Service {
    style.fill: "#7EE2B8"
  }

  audit: Audit Logger {
    style.fill: "#F797D2"
  }
}

# Connections - Async (dashed)
producers.web-app -> events.kafka: Publish {
  style.stroke-dash: 5
}
producers.mobile-app -> events.kafka: Publish {
  style.stroke-dash: 5
}
producers.iot -> events.kafka: Publish {
  style.stroke-dash: 5
}

events.kafka -> consumers.analytics: Subscribe {
  style.stroke-dash: 5
}
events.kafka -> consumers.search: Subscribe {
  style.stroke-dash: 5
}
events.kafka -> consumers.notifications: Subscribe {
  style.stroke-dash: 5
}
events.kafka -> consumers.audit: Subscribe {
  style.stroke-dash: 5
}
```

## Database Schema Overview

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

users: users {
  shape: sql_table
  id: uuid { constraint: primary_key }
  email: varchar(255) { constraint: unique }
  name: varchar(100)
  created_at: timestamp
  updated_at: timestamp
}

organizations: organizations {
  shape: sql_table
  id: uuid { constraint: primary_key }
  name: varchar(100)
  slug: varchar(50) { constraint: unique }
  created_at: timestamp
}

memberships: memberships {
  shape: sql_table
  id: uuid { constraint: primary_key }
  user_id: uuid { constraint: foreign_key }
  org_id: uuid { constraint: foreign_key }
  role: varchar(20)
  created_at: timestamp
}

projects: projects {
  shape: sql_table
  id: uuid { constraint: primary_key }
  org_id: uuid { constraint: foreign_key }
  name: varchar(100)
  description: text
  created_at: timestamp
}

# Relationships
users.id <- memberships.user_id
organizations.id <- memberships.org_id
organizations.id <- projects.org_id
```

## Network Topology

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

internet: Internet {
  shape: cloud
  style.fill: "#F4F4F4"
}

edge: Edge Layer {
  style.fill: "#FFEBE6"

  cdn: CDN {
    style.fill: "#B8ACF6"
  }

  waf: WAF {
    style.fill: "#F797D2"
  }

  ddos: DDoS Protection {
    style.fill: "#F797D2"
  }
}

dmz: DMZ {
  style.fill: "#FFF7E6"
  style.stroke-dash: 5

  lb: Load Balancer {
    style.fill: "#B8ACF6"
  }

  bastion: Bastion Host {
    style.fill: "#F5CD47"
  }
}

internal: Internal Network {
  style.fill: "#E6FCFF"
  style.stroke-dash: 5

  app: Application Servers {
    style.fill: "#85B8FF"
  }

  db: Database Servers {
    shape: cylinder
    style.fill: "#9DD9EE"
  }
}

# Connections
internet -> edge.cdn
edge.cdn -> edge.waf
edge.waf -> edge.ddos
edge.ddos -> dmz.lb

dmz.lb -> internal.app
internal.app -> internal.db

internet -> dmz.bastion: SSH (restricted) {
  style.stroke-dash: 5
  style.stroke: "#DE350B"
}
dmz.bastion -> internal.app: SSH {
  style.stroke-dash: 5
}
```
