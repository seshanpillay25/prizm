# Design Patterns for Specifications

## Effective Design Documentation

The design.md file bridges the gap between what you want to build (requirements) and how you'll build it. It's where architecture meets implementation.

## Core Design Elements

### 1. Architecture Overview
Start with the big picture before diving into details:

```markdown
## Architecture Overview

### System Type
[Monolith / Microservices / Serverless / Hybrid]

### Key Components
- **Frontend**: [Technology stack and approach]
- **Backend**: [API design and framework]
- **Database**: [Storage strategy and technology]
- **Infrastructure**: [Deployment and scaling approach]

### Design Principles
- [Principle 1]: [Why this matters]
- [Principle 2]: [Why this matters]
```

### 2. Technology Decisions
Document not just what, but why:

```markdown
## Technology Stack

### Frontend
- **Framework**: React 18
  - *Why*: Component reusability, strong ecosystem, team expertise
  - *Alternatives considered*: Vue (less team experience), Angular (too heavy)

### Backend  
- **Runtime**: Node.js with Express
  - *Why*: JavaScript throughout stack, excellent async handling
  - *Trade-offs*: Less CPU-efficient than Go, but faster development
```

## Design Patterns by Project Type

### Simple CLI Tool Design
```markdown
## Design Overview

### Architecture
Single-file Node.js application with modular functions

### Core Components
1. **CLI Parser**: Commander.js for argument handling
2. **Business Logic**: Pure functions for todo operations  
3. **Storage**: JSON file for persistence
4. **Display**: Console output with chalk for formatting

### Data Flow
```
User Input → CLI Parser → Business Logic → Storage
                              ↓
                         Console Output
```

### Key Design Decisions
- **No database**: Simplicity over features
- **Synchronous I/O**: Fine for single-user CLI
- **Minimal dependencies**: Easier distribution
```

### Web API Design
```markdown
## API Architecture

### Design Pattern
RESTful API with layered architecture:
```
Controllers → Services → Repositories → Database
     ↓             ↓            ↓
Middleware    Validation    Models
```

### Core Components

#### API Layer
- **Framework**: Express.js with middleware pipeline
- **Routing**: RESTful resource-based URLs
- **Validation**: Joi schemas for request validation
- **Authentication**: JWT with refresh tokens

#### Business Logic Layer  
- **Services**: One service per domain entity
- **Transactions**: Service-level transaction boundaries
- **Error Handling**: Custom error classes with error codes

#### Data Access Layer
- **ORM**: Prisma for type-safe database access
- **Migrations**: Version-controlled schema changes
- **Seeding**: Development and test data

### API Design Principles
1. **Consistent**: All endpoints follow same patterns
2. **Versioned**: /api/v1 prefix for future changes
3. **Documented**: OpenAPI specification generated
4. **Secure**: Authentication required by default

### Example Endpoint Design
```
POST /api/v1/todos
Headers: Authorization: Bearer <token>
Body: {
  "title": "string, required",
  "description": "string, optional", 
  "dueDate": "ISO 8601, optional"
}
Response: {
  "id": "uuid",
  "title": "string",
  "status": "pending|completed",
  "createdAt": "ISO 8601"
}
```
```

### SaaS Application Design
```markdown
## Multi-Tenant Architecture

### Tenancy Model
Database-per-tenant with shared application servers

### Core Architecture Patterns

#### 1. Multi-Tenancy
```
Request → Tenant Resolver → Tenant Context → Database Router
                                                    ↓
                                            Tenant Database
```

#### 2. Event-Driven Updates
```
User Action → Command → Domain Event → Event Store
                              ↓
                    Event Handlers → Read Models
                                   → Notifications
                                   → Analytics
```

#### 3. CQRS Implementation
- **Commands**: Write operations through command handlers
- **Queries**: Optimized read models for different views
- **Eventual Consistency**: Events sync read models

### Security Architecture
1. **Authentication**: OAuth2 with social providers
2. **Authorization**: Role-based with tenant isolation
3. **API Gateway**: Rate limiting and request validation
4. **Encryption**: At-rest and in-transit

### Scalability Design
- **Horizontal Scaling**: Stateless application servers
- **Database Sharding**: By tenant ID
- **Caching Strategy**: Redis for sessions and hot data
- **CDN**: Static assets and API responses
```

### Microservices Design
```markdown
## Distributed System Architecture

### Service Decomposition
Domain-driven design with bounded contexts:

1. **User Service**: Authentication and profiles
2. **Project Service**: Project and task management  
3. **Notification Service**: Email, SMS, push
4. **Analytics Service**: Metrics and reporting
5. **Billing Service**: Subscriptions and payments

### Communication Patterns

#### Synchronous
- **Protocol**: gRPC for internal services
- **Service Discovery**: Consul with health checks
- **Circuit Breaker**: Hystrix for fault tolerance

#### Asynchronous  
- **Message Bus**: Apache Kafka
- **Event Schema**: Avro with schema registry
- **Ordering**: Partition by aggregate ID

### Data Architecture
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   User DB   │     │ Project DB  │     │Analytics DW │
└─────────────┘     └─────────────┘     └─────────────┘
       ↑                    ↑                    ↑
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│User Service │     │Proj Service │     │Analytics Svc│
└─────────────┘     └─────────────┘     └─────────────┘
       ↓                    ↓                    ↑
┌─────────────────────────────────────────────────┐
│                  Event Stream                    │
└─────────────────────────────────────────────────┘
```

### Cross-Cutting Concerns
1. **Distributed Tracing**: OpenTelemetry
2. **Service Mesh**: Istio for communication
3. **Monitoring**: Prometheus + Grafana
4. **Logging**: ELK stack with correlation IDs
```

## Design Decision Records

### ADR Template
```markdown
## ADR-001: [Decision Title]
**Date:** YYYY-MM-DD
**Status:** Accepted/Rejected/Superseded

### Context
[What prompted this decision]

### Decision  
[What we decided to do]

### Consequences
**Positive:**
- [Good outcome 1]
- [Good outcome 2]

**Negative:**
- [Trade-off 1]
- [Trade-off 2]

### Alternatives Considered
1. **[Alternative 1]**: [Why not chosen]
2. **[Alternative 2]**: [Why not chosen]
```

## Common Design Patterns

### 1. Layered Architecture
```
Presentation Layer
       ↓
Application Layer  
       ↓
Domain Layer
       ↓
Infrastructure Layer
```

### 2. Hexagonal Architecture
```
        Driving Adapters
              ↓
    ┌─────────────────┐
    │                 │
    │   Domain Core   │ ← Ports
    │                 │
    └─────────────────┘
              ↑
        Driven Adapters
```

### 3. Event Sourcing
```
Commands → Aggregate → Events → Event Store
                          ↓
                    Projections → Read Models
```

## API Design Patterns

### RESTful Resource Design
```markdown
## Resource: Todos

### Endpoints
- GET    /todos       - List all todos
- POST   /todos       - Create new todo
- GET    /todos/:id   - Get specific todo
- PUT    /todos/:id   - Update entire todo
- PATCH  /todos/:id   - Update todo fields
- DELETE /todos/:id   - Delete todo

### Query Parameters
- ?status=pending|completed
- ?sort=createdAt|dueDate
- ?page=1&limit=20
```

### GraphQL Schema Design
```graphql
type Todo {
  id: ID!
  title: String!
  description: String
  status: TodoStatus!
  createdAt: DateTime!
  owner: User!
}

type Query {
  todos(status: TodoStatus, limit: Int): [Todo!]!
  todo(id: ID!): Todo
}

type Mutation {
  createTodo(input: CreateTodoInput!): Todo!
  updateTodo(id: ID!, input: UpdateTodoInput!): Todo!
}
```

## Database Design Patterns

### Normalized Schema
```sql
-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Todos table with foreign key
CREATE TABLE todos (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  title VARCHAR(200) NOT NULL,
  status VARCHAR(20) DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT NOW()
);

-- Indexes for performance
CREATE INDEX idx_todos_user_id ON todos(user_id);
CREATE INDEX idx_todos_status ON todos(status);
```

### Document Store Design
```javascript
// MongoDB document structure
{
  _id: ObjectId("..."),
  userId: ObjectId("..."),
  title: "Buy groceries",
  description: "Milk, bread, eggs",
  status: "pending",
  tags: ["shopping", "urgent"],
  metadata: {
    createdAt: ISODate("..."),
    updatedAt: ISODate("..."),
    version: 1
  }
}

// Indexes
db.todos.createIndex({ userId: 1, status: 1 })
db.todos.createIndex({ tags: 1 })
```

## Performance Design Patterns

### Caching Strategy
```markdown
## Caching Layers

1. **Browser Cache**: Static assets with long TTL
2. **CDN Cache**: API responses for read-heavy endpoints
3. **Application Cache**: Redis for session and hot data
4. **Database Cache**: Query result caching

### Cache Invalidation
- **TTL-based**: Default 5 minutes for most data
- **Event-based**: Invalidate on write operations
- **Tag-based**: Invalidate related cache entries
```

### Optimization Patterns
```markdown
## Performance Optimizations

### Database
- Connection pooling (min: 5, max: 20)
- Prepared statements for common queries
- Read replicas for analytics queries

### API
- Response compression (gzip)
- Pagination for list endpoints  
- Field filtering for large objects
- ETag headers for caching

### Frontend
- Code splitting by route
- Lazy loading for images
- Service worker for offline
```

## Security Design Patterns

### Defense in Depth
```markdown
## Security Layers

1. **Network**: WAF, DDoS protection
2. **Application**: Input validation, CSRF tokens
3. **API**: Rate limiting, authentication
4. **Data**: Encryption, access control
5. **Monitoring**: Anomaly detection, alerts
```

## Design Anti-Patterns to Avoid

### 1. Big Ball of Mud
❌ No clear architecture or boundaries
✅ Well-defined layers and modules

### 2. God Object
❌ One class/service doing everything
✅ Single responsibility principle

### 3. Chatty APIs
❌ Multiple calls for one operation
✅ Aggregate data in single endpoint

### 4. Shared Mutable State
❌ Global variables modified everywhere
✅ Immutable data or clear ownership

## Design Documentation Tips

1. **Diagrams > Words**: Use ASCII diagrams or Mermaid
2. **Show Data Flow**: How information moves through system
3. **Document Trade-offs**: Why you chose A over B
4. **Include Examples**: Show typical usage patterns
5. **Version Your Design**: Track how architecture evolves

Remember: Good design documentation helps future developers (including yourself) understand not just what the system does, but why it was built that way.