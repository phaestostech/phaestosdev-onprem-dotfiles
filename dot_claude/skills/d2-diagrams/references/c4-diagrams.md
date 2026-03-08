# C4 Model Diagram Templates

Templates for C4 model diagrams at all four levels: Context, Container, Component, and Code.

## C4 Model Overview

| Level | Purpose | Audience |
|-------|---------|----------|
| **Context** | System scope and external actors | Everyone |
| **Container** | High-level technology decisions | Technical people |
| **Component** | Internal structure of containers | Developers |
| **Code** | Implementation details | Developers |

## Standard C4 Styling

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
    center: true
  }

  # C4 color scheme
  c4: {
    person: "#08427B"
    person-ext: "#999999"
    system: "#1168BD"
    system-ext: "#999999"
    container: "#438DD5"
    component: "#85BBF0"
    database: "#438DD5"
  }
}

classes: {
  # People
  person: {
    shape: c4-person
    style.fill: "#08427B"
    style.font-color: "#FFFFFF"
  }
  person-ext: {
    shape: c4-person
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
    style.stroke-dash: 5
  }

  # Systems
  system: {
    style.fill: "#1168BD"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }
  system-ext: {
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
    style.stroke-dash: 5
    style.border-radius: 8
  }

  # Containers
  container: {
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }
  database: {
    shape: cylinder
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
  }
  queue: {
    shape: queue
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
  }

  # Components
  component: {
    style.fill: "#85BBF0"
    style.font-color: "#000000"
    style.border-radius: 8
  }

  # Boundaries
  boundary: {
    style.fill: "#FFFFFF"
    style.stroke: "#444444"
    style.stroke-dash: 5
    style.font-color: "#444444"
  }

  # Relationships
  sync: {
    style.stroke: "#707070"
    style.stroke-width: 2
  }
  async: {
    style.stroke: "#707070"
    style.stroke-width: 2
    style.stroke-dash: 5
  }
}
```

## Level 1: System Context Diagram

Shows the system in scope and its relationships with users and other systems.

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
    center: true
  }
}

classes: {
  person: {
    shape: c4-person
    style.fill: "#08427B"
    style.font-color: "#FFFFFF"
  }
  person-ext: {
    shape: c4-person
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
  }
  system: {
    style.fill: "#1168BD"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }
  system-ext: {
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
    style.stroke-dash: 5
    style.border-radius: 8
  }
}

title: "System Context Diagram - Internet Banking System" {
  near: top-center
  style.font-size: 24
  style.bold: true
}

# People
customer: |md
  **Personal Banking Customer**

  A customer of the bank with
  personal bank accounts
| {
  class: person
}

support: |md
  **Customer Support Staff**

  Support staff within the bank
| {
  class: person-ext
}

# System in scope
banking: |md
  **Internet Banking System**

  Allows customers to view their
  accounts and make payments
| {
  class: system
}

# External systems
mainframe: |md
  **Mainframe Banking System**

  Stores all core banking
  information
| {
  class: system-ext
}

email: |md
  **E-mail System**

  Microsoft Exchange email
  system
| {
  class: system-ext
}

# Relationships
customer -> banking: |md
  Views account balances
  and makes payments
|

support -> banking: |md
  Uses for customer
  support queries
|

banking -> mainframe: |md
  Gets account information
  from, makes payments using
|

banking -> email: |md
  Sends e-mails using
|
```

## Level 2: Container Diagram

Shows the high-level shape of the software architecture and how responsibilities are distributed.

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  person: {
    shape: c4-person
    style.fill: "#08427B"
    style.font-color: "#FFFFFF"
  }
  container: {
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }
  database: {
    shape: cylinder
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
  }
  system-ext: {
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
    style.stroke-dash: 5
    style.border-radius: 8
  }
  boundary: {
    style.fill: "#FFFFFF"
    style.stroke: "#1168BD"
    style.stroke-dash: 5
  }
}

title: "Container Diagram - Internet Banking System" {
  near: top-center
  style.font-size: 24
  style.bold: true
}

# External actors
customer: |md
  **Personal Banking Customer**
| {
  class: person
}

# System boundary
banking: Internet Banking System {
  class: boundary

  web-app: |md
    **Web Application**

    [Java / Spring MVC]

    Delivers static content and
    the single page application
  | {
    class: container
  }

  spa: |md
    **Single-Page Application**

    [JavaScript / React]

    Provides banking functionality
    via the web browser
  | {
    class: container
  }

  mobile: |md
    **Mobile App**

    [React Native]

    Provides banking functionality
    via mobile device
  | {
    class: container
  }

  api: |md
    **API Application**

    [Java / Spring Boot]

    Provides banking functionality
    via JSON/HTTPS API
  | {
    class: container
  }

  db: |md
    **Database**

    [PostgreSQL]

    Stores user registration,
    authentication, and access logs
  | {
    class: database
  }
}

# External systems
mainframe: |md
  **Mainframe Banking System**

  [Mainframe]
| {
  class: system-ext
}

email: |md
  **E-mail System**

  [Microsoft Exchange]
| {
  class: system-ext
}

# Relationships
customer -> banking.web-app: |md
  Visits [HTTPS]
|

customer -> banking.spa: |md
  Uses
|

customer -> banking.mobile: |md
  Uses
|

banking.web-app -> banking.spa: |md
  Delivers to browser
|

banking.spa -> banking.api: |md
  Makes API calls to
  [JSON/HTTPS]
|

banking.mobile -> banking.api: |md
  Makes API calls to
  [JSON/HTTPS]
|

banking.api -> banking.db: |md
  Reads/writes
  [SQL/TCP]
|

banking.api -> mainframe: |md
  Makes API calls to
  [XML/HTTPS]
|

banking.api -> email: |md
  Sends e-mails using
  [SMTP]
|
```

## Level 3: Component Diagram

Shows the internal structure of a container and how it is composed.

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

classes: {
  component: {
    style.fill: "#85BBF0"
    style.font-color: "#000000"
    style.border-radius: 8
  }
  database: {
    shape: cylinder
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
  }
  container: {
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }
  system-ext: {
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
    style.stroke-dash: 5
    style.border-radius: 8
  }
  boundary: {
    style.fill: "#FFFFFF"
    style.stroke: "#438DD5"
    style.stroke-dash: 5
  }
}

title: "Component Diagram - API Application" {
  near: top-center
  style.font-size: 24
  style.bold: true
}

# External containers
spa: |md
  **Single-Page Application**

  [React]
| {
  class: container
}

mobile: |md
  **Mobile App**

  [React Native]
| {
  class: container
}

db: |md
  **Database**

  [PostgreSQL]
| {
  class: database
}

mainframe: |md
  **Mainframe Banking System**
| {
  class: system-ext
}

# API Application boundary
api: API Application {
  class: boundary

  auth-controller: |md
    **Auth Controller**

    [Spring REST Controller]

    Handles authentication
    requests
  | {
    class: component
  }

  accounts-controller: |md
    **Accounts Controller**

    [Spring REST Controller]

    Handles account-related
    requests
  | {
    class: component
  }

  auth-service: |md
    **Auth Service**

    [Spring Service]

    Provides authentication
    functionality
  | {
    class: component
  }

  accounts-service: |md
    **Accounts Service**

    [Spring Service]

    Provides account
    functionality
  | {
    class: component
  }

  security-component: |md
    **Security Component**

    [Spring Security]

    Handles JWT validation
    and authorization
  | {
    class: component
  }

  user-repository: |md
    **User Repository**

    [Spring Data JPA]

    Provides user data
    access
  | {
    class: component
  }

  mainframe-facade: |md
    **Mainframe Facade**

    [Spring Component]

    Integration with
    mainframe systems
  | {
    class: component
  }
}

# Relationships - External to API
spa -> api.auth-controller: Makes API calls [JSON/HTTPS]
spa -> api.accounts-controller: Makes API calls [JSON/HTTPS]
mobile -> api.auth-controller: Makes API calls [JSON/HTTPS]
mobile -> api.accounts-controller: Makes API calls [JSON/HTTPS]

# Relationships - Internal
api.auth-controller -> api.auth-service: Uses
api.accounts-controller -> api.accounts-service: Uses
api.auth-controller -> api.security-component: Uses
api.accounts-controller -> api.security-component: Uses
api.auth-service -> api.user-repository: Uses
api.accounts-service -> api.mainframe-facade: Uses

# Relationships - API to External
api.user-repository -> db: Reads/writes [SQL]
api.mainframe-facade -> mainframe: Makes calls [XML/HTTPS]
```

## Level 4: Code Diagram (UML Class)

Shows implementation details (typically auto-generated from code).

```d2
vars: {
  d2-config: {
    layout-engine: tala
    theme-id: 5
  }
}

title: "Class Diagram - Accounts Service" {
  near: top-center
  style.font-size: 20
  style.bold: true
}

# Interface
IAccountsService: {
  shape: class
  style.fill: "#E6FCFF"
  style.stroke: "#00A3BF"

  \<\<interface\>\>
  +getAccounts(userId: String): List\<Account\>
  +getAccountById(id: String): Account
  +getTransactions(accountId: String): List\<Transaction\>
}

# Implementation
AccountsServiceImpl: {
  shape: class
  style.fill: "#DEEBFF"

  -mainframeFacade: MainframeFacade
  -accountMapper: AccountMapper
  +getAccounts(userId: String): List\<Account\>
  +getAccountById(id: String): Account
  +getTransactions(accountId: String): List\<Transaction\>
  -mapToAccount(data: MainframeData): Account
}

# Dependencies
MainframeFacade: {
  shape: class
  style.fill: "#F7F8F9"

  -httpClient: HttpClient
  -xmlParser: XmlParser
  +getAccountData(userId: String): MainframeData
  +getTransactionData(accountId: String): MainframeData
}

AccountMapper: {
  shape: class
  style.fill: "#F7F8F9"

  +toAccount(dto: AccountDTO): Account
  +toTransaction(dto: TransactionDTO): Transaction
}

# Domain models
Account: {
  shape: class
  style.fill: "#FFF7E6"

  -id: String
  -userId: String
  -accountNumber: String
  -balance: BigDecimal
  -currency: String
  +getId(): String
  +getBalance(): BigDecimal
}

Transaction: {
  shape: class
  style.fill: "#FFF7E6"

  -id: String
  -accountId: String
  -amount: BigDecimal
  -type: TransactionType
  -timestamp: Instant
}

# Relationships
AccountsServiceImpl -> IAccountsService: implements {
  style.stroke-dash: 5
  target-arrowhead: {
    shape: triangle
    filled: false
  }
}

AccountsServiceImpl -> MainframeFacade: uses
AccountsServiceImpl -> AccountMapper: uses
AccountsServiceImpl -> Account: creates
AccountsServiceImpl -> Transaction: creates
```

## C4 Legend Template

Add to any C4 diagram for clarity:

```d2
legend: Legend {
  near: bottom-right
  style.fill: "#FFFFFF"
  style.stroke: "#CCCCCC"

  direction: down

  person-legend: Person {
    shape: c4-person
    style.fill: "#08427B"
    style.font-color: "#FFFFFF"
  }

  system-legend: Software System {
    style.fill: "#1168BD"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }

  external-legend: External System {
    style.fill: "#999999"
    style.font-color: "#FFFFFF"
    style.stroke-dash: 5
    style.border-radius: 8
  }

  container-legend: Container {
    style.fill: "#438DD5"
    style.font-color: "#FFFFFF"
    style.border-radius: 8
  }

  component-legend: Component {
    style.fill: "#85BBF0"
    style.font-color: "#000000"
    style.border-radius: 8
  }
}
```

## C4 Best Practices

1. **Context diagrams** should fit on one page - 5-10 elements maximum
2. **Container diagrams** should show deployment units, not classes
3. **Component diagrams** show the internals of one container at a time
4. **Code diagrams** are typically auto-generated - keep manual ones small
5. Use consistent color scheme across all levels
6. Include a legend when sharing with non-technical stakeholders
7. Add brief descriptions explaining the purpose of each element
8. Show technology choices in square brackets: `[Java / Spring Boot]`
