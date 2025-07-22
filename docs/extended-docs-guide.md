# Extended Documents Guide

Extended steering documents complement the Core Trinity by providing specialized guidance for specific project concerns. This guide helps you understand when and how to use each extended document effectively.

## Philosophy: Progressive Complexity

Extended documents follow Prizm's progressive complexity principle:

- **Start Simple**: Begin with Core Trinity only
- **Add as Needed**: Include extended docs when specific concerns arise
- **Scale Gradually**: Don't overwhelm small projects with unnecessary documentation
- **Maintain Quality**: Better to have fewer, well-maintained documents than many outdated ones

## Decision Framework

### When to Add Extended Documents

Use this decision tree to determine which extended documents your project needs:

```
Project Team Size > 3 people? → YES → Add style.md
Multiple external services? → YES → Add integrations.md
Complex business logic? → YES → Add domain.md
Distributed architecture? → YES → Add architecture.md
High quality requirements? → YES → Add testing-strategy.md
Strict constraints? → YES → Add constraints.md
Security requirements? → YES → Add security.md
Enterprise compliance? → YES → Add compliance.md
Complex deployment? → YES → Add deployment.md
Multiple environments? → YES → Add monitoring.md
```

### Progressive Addition Strategy

1. **Start with Core Trinity** - Always begin here
2. **Add First-Tier Documents** - Based on immediate needs
3. **Include Second-Tier Documents** - As complexity grows
4. **Complete with Third-Tier Documents** - For enterprise projects

## Extended Documents Overview

### First-Tier Documents
**Add when basic concerns arise**

| Document | When to Add | Primary Benefit |
|----------|-------------|-----------------|
| **style.md** | Team size > 2 | Code consistency |
| **integrations.md** | External services | Integration clarity |
| **testing-strategy.md** | Quality focus | Test organization |

### Second-Tier Documents
**Add when complexity increases**

| Document | When to Add | Primary Benefit |
|----------|-------------|-----------------|
| **domain.md** | Complex business logic | Domain understanding |
| **constraints.md** | Multiple limitations | Constraint awareness |
| **architecture.md** | Distributed systems | Architecture clarity |

### Third-Tier Documents
**Add for enterprise needs**

| Document | When to Add | Primary Benefit |
|----------|-------------|-----------------|
| **security.md** | Security requirements | Security design |
| **compliance.md** | Regulatory needs | Compliance management |
| **deployment.md** | Complex deployment | Deployment strategy |
| **monitoring.md** | Observability needs | System monitoring |

## Detailed Document Guide

### style.md - Code Standards and Conventions

#### When to Add
- **Team Size**: 3+ developers
- **Code Quality**: Quality requirements are high
- **Onboarding**: New team members join regularly
- **Maintenance**: Long-term project maintenance

#### What to Include
```markdown
# Style Guide - [Project Name]

## General Principles
- Code quality principles
- Team conventions
- Tool configurations

## Language-Specific Guidelines
- TypeScript/JavaScript patterns
- Python conventions
- Java standards

## Framework Patterns
- React component patterns
- Express.js conventions
- Database patterns

## Code Organization
- File structure
- Import/export patterns
- Naming conventions

## Testing Patterns
- Unit test structure
- Integration test patterns
- Mock strategies

## Documentation Standards
- Code comments
- API documentation
- README structure
```

#### Example Implementation
```markdown
## TypeScript Guidelines

### Interface Naming
```typescript
// ✅ Good - descriptive interface names
interface UserCreateRequest {
  email: string;
  password: string;
  profile: UserProfile;
}

// ❌ Bad - generic or unclear names
interface UserData {
  email: string;
  password: string;
}
```

### Function Patterns
```typescript
// ✅ Good - explicit return types and error handling
const createUser = async (userData: UserCreateRequest): Promise<User> => {
  try {
    const user = await userRepository.create(userData);
    return user;
  } catch (error) {
    throw new ValidationError('Failed to create user', error);
  }
}
```
```

### integrations.md - External Service Integration

#### When to Add
- **Multiple Services**: 3+ external integrations
- **Complex Authentication**: OAuth, API keys, webhooks
- **Service Dependencies**: Critical external dependencies
- **Integration Patterns**: Reusable integration approaches

#### What to Include
```markdown
# Integrations Document - [Project Name]

## Integration Overview
Summary of all external services

## Service Configurations
### Database Integration
- Connection details
- Pool configuration
- Health checks

### Cache Integration
- Redis configuration
- Caching strategies
- Invalidation patterns

### Third-Party APIs
- Authentication methods
- Rate limiting
- Error handling

## Health Monitoring
Service health checks and monitoring

## Error Handling
Integration failure patterns and recovery

## Environment Configuration
Configuration for different environments
```

#### Example Implementation
```markdown
## Database Integration

### PostgreSQL Configuration
```javascript
const pool = new Pool({
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || '5432'),
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  min: 5,
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000
});
```

### Health Check Implementation
```javascript
const checkDatabaseHealth = async () => {
  try {
    const client = await pool.connect();
    await client.query('SELECT 1');
    client.release();
    return { status: 'healthy', timestamp: new Date().toISOString() };
  } catch (error) {
    return { 
      status: 'unhealthy', 
      error: error.message, 
      timestamp: new Date().toISOString() 
    };
  }
}
```
```

### testing-strategy.md - Test Organization and Strategy

#### When to Add
- **Quality Requirements**: High-quality code requirements
- **Multiple Test Types**: Unit, integration, e2e tests
- **CI/CD Pipeline**: Automated testing pipeline
- **Test Complexity**: Complex testing scenarios

#### What to Include
```markdown
# Testing Strategy - [Project Name]

## Testing Philosophy
Overall approach to testing

## Test Types
### Unit Tests
- Scope and purpose
- Tools and frameworks
- Coverage requirements

### Integration Tests
- Service integration testing
- Database testing
- API testing

### End-to-End Tests
- User workflow testing
- Browser testing
- Mobile testing

## Test Organization
Directory structure and naming conventions

## CI/CD Integration
Automated testing in deployment pipeline

## Quality Gates
Coverage thresholds and quality metrics
```

#### Example Implementation
```markdown
## Testing Pyramid

### Unit Tests (70%)
- **Scope**: Individual functions and components
- **Framework**: Jest with TypeScript
- **Coverage**: 90% minimum
- **Location**: `src/**/*.test.ts`

### Integration Tests (25%)
- **Scope**: Service interactions and database operations
- **Framework**: Jest with Supertest
- **Coverage**: All API endpoints
- **Location**: `tests/integration/**/*.test.ts`

### E2E Tests (5%)
- **Scope**: Complete user workflows
- **Framework**: Playwright
- **Coverage**: Critical user paths
- **Location**: `tests/e2e/**/*.test.ts`

## Test Examples

### Unit Test Example
```typescript
describe('UserService', () => {
  let userService: UserService;
  let mockUserRepository: jest.Mocked<UserRepository>;

  beforeEach(() => {
    mockUserRepository = {
      create: jest.fn(),
      findById: jest.fn(),
      findByEmail: jest.fn(),
      update: jest.fn(),
      delete: jest.fn()
    };
    userService = new UserService(mockUserRepository);
  });

  describe('createUser', () => {
    it('should create user with valid data', async () => {
      const userData = {
        email: 'test@example.com',
        password: 'password123',
        profile: { name: 'Test User' }
      };
      
      const expectedUser = { id: '1', ...userData };
      mockUserRepository.create.mockResolvedValue(expectedUser);

      const result = await userService.createUser(userData);

      expect(result).toEqual(expectedUser);
      expect(mockUserRepository.create).toHaveBeenCalledWith(userData);
    });
  });
});
```
```

### domain.md - Business Domain Modeling

#### When to Add
- **Complex Business Logic**: Rich domain models
- **Multiple Bounded Contexts**: Domain-driven design
- **Business Rules**: Complex business rule validation
- **Domain Experts**: Subject matter expert involvement

#### What to Include
```markdown
# Domain Model - [Project Name]

## Domain Overview
Business domain description

## Core Entities
Primary business entities and their relationships

## Value Objects
Immutable domain concepts

## Business Rules
Domain-specific business logic

## Domain Events
Events that occur within the domain

## Bounded Contexts
Separate domain boundaries

## Ubiquitous Language
Shared vocabulary between business and technical teams
```

#### Example Implementation
```markdown
## Core Entities

### User
**Purpose:** Represents a person who can access the system

```typescript
interface User {
  id: UserId;
  email: string;
  profile: UserProfile;
  role: UserRole;
  status: UserStatus;
  createdAt: Date;
  updatedAt: Date;
}

enum UserRole {
  ADMIN = 'admin',
  MANAGER = 'manager',
  MEMBER = 'member'
}
```

**Business Rules:**
- Users must have unique email addresses
- User roles determine system permissions
- Inactive users retain data but cannot access system

### Project
**Purpose:** Represents a collection of related work

```typescript
interface Project {
  id: ProjectId;
  name: string;
  description: string;
  status: ProjectStatus;
  owner: UserId;
  team: ProjectMember[];
  timeline: ProjectTimeline;
  createdAt: Date;
  updatedAt: Date;
}
```

**Business Rules:**
- Projects must have defined start and end dates
- Project owners can manage team membership
- Completed projects become read-only
```

### constraints.md - Project Constraints

#### When to Add
- **Technical Limitations**: Technology or performance constraints
- **Business Constraints**: Budget, timeline, or resource limitations
- **Regulatory Requirements**: Compliance constraints
- **External Dependencies**: Third-party limitations

#### What to Include
```markdown
# Constraints Document - [Project Name]

## Technical Constraints
Technology stack limitations and requirements

## Business Constraints
Budget, timeline, and resource limitations

## Regulatory Constraints
Compliance and legal requirements

## Performance Constraints
Scalability and performance requirements

## Security Constraints
Security requirements and limitations

## Integration Constraints
Third-party service limitations
```

#### Example Implementation
```markdown
## Technical Constraints

### Technology Stack Constraints
- **Backend:** Node.js 18+ with TypeScript 4.9+
- **Database:** PostgreSQL 14+ (no NoSQL databases)
- **Cloud Provider:** AWS only (no multi-cloud)
- **Containerization:** Docker containers mandatory

**Rationale:** Team expertise and existing infrastructure

### Performance Constraints
- **API Response Time:** 95th percentile < 200ms
- **Database Queries:** 95th percentile < 100ms
- **Concurrent Users:** Support 10,000 concurrent users
- **Storage:** 1TB maximum per tenant

**Rationale:** User experience requirements and cost optimization

## Business Constraints

### Budget Constraints
- **Total Development Budget:** $500K
- **Monthly Infrastructure:** $10K maximum
- **Third-Party Services:** $2K maximum

### Timeline Constraints
- **MVP Release:** 12 weeks maximum
- **Feature Releases:** Bi-weekly cycles
- **Critical Bug Fixes:** 24-hour resolution

### Resource Constraints
- **Team Size:** Maximum 5 developers
- **Technology Training:** 2 weeks maximum per developer
- **External Dependencies:** Minimize vendor lock-in
```

### architecture.md - System Architecture

#### When to Add
- **Distributed Systems**: Microservices or distributed architecture
- **Complex Integrations**: Multiple system integrations
- **Scalability Requirements**: High-scale architecture needs
- **Architecture Decisions**: Need to document architectural choices

#### What to Include
```markdown
# Architecture Document - [Project Name]

## System Architecture
High-level system design and component relationships

## Service Architecture
Individual service designs and interactions

## Data Architecture
Database design and data flow patterns

## Security Architecture
Security patterns and implementations

## Deployment Architecture
Infrastructure and deployment strategies

## Performance Architecture
Scalability and performance patterns

## Monitoring Architecture
Observability and monitoring strategies
```

#### Example Implementation
```markdown
## System Architecture

### High-Level Architecture
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Frontend   │    │     API     │    │  Database   │
│   React     │◄───┤   Gateway   │◄───┤ PostgreSQL  │
│             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
                           │
                   ┌─────────────┐
                   │    Cache    │
                   │    Redis    │
                   └─────────────┘
```

### Service Architecture

#### API Gateway
- **Purpose:** Single entry point for all client requests
- **Technology:** Kong API Gateway
- **Responsibilities:**
  - Authentication and authorization
  - Rate limiting and throttling
  - Request routing and load balancing
  - Request/response transformation

#### User Service
- **Purpose:** User management and authentication
- **Technology:** Node.js with Express
- **Responsibilities:**
  - User registration and login
  - Profile management
  - Role-based access control
  - Session management

### Data Architecture

#### Database Design
- **Primary Database:** PostgreSQL for transactional data
- **Cache Layer:** Redis for session and query caching
- **File Storage:** AWS S3 for file uploads
- **Search Engine:** Elasticsearch for full-text search

#### Data Flow
1. Client requests hit API Gateway
2. Gateway authenticates and routes to services
3. Services query database with connection pooling
4. Cache layer reduces database load
5. Search engine provides fast text search
```

## Enterprise Documents

### security.md - Security Implementation

#### When to Add
- **Security Requirements**: Explicit security requirements
- **Compliance Needs**: Security compliance frameworks
- **Threat Model**: Identified security threats
- **Security Architecture**: Complex security implementations

#### What to Include
```markdown
# Security Document - [Project Name]

## Security Overview
Security principles and approach

## Threat Model
Identified threats and attack vectors

## Authentication and Authorization
Identity and access management

## Data Protection
Encryption and data handling

## Security Monitoring
Threat detection and response

## Compliance
Security compliance requirements
```

### compliance.md - Regulatory Compliance

#### When to Add
- **Regulatory Requirements**: GDPR, HIPAA, SOC 2
- **Audit Requirements**: Regular compliance audits
- **Enterprise Customers**: B2B compliance needs
- **Data Protection**: Strict data handling requirements

#### What to Include
```markdown
# Compliance Document - [Project Name]

## Compliance Overview
Regulatory frameworks and requirements

## Data Protection
Privacy and data handling compliance

## Audit Trail
Compliance monitoring and reporting

## Risk Management
Compliance risk assessment

## Vendor Management
Third-party compliance requirements
```

### deployment.md - Deployment Strategy

#### When to Add
- **Complex Deployment**: Multi-environment deployment
- **Infrastructure as Code**: Automated infrastructure
- **CI/CD Pipeline**: Complex deployment pipeline
- **Zero-Downtime**: High-availability deployment

#### What to Include
```markdown
# Deployment Document - [Project Name]

## Deployment Overview
Deployment strategy and approach

## Environment Configuration
Development, staging, and production environments

## Infrastructure as Code
Automated infrastructure provisioning

## CI/CD Pipeline
Continuous integration and deployment

## Monitoring and Rollback
Deployment monitoring and recovery procedures
```

### monitoring.md - System Monitoring

#### When to Add
- **Observability Requirements**: Comprehensive monitoring needs
- **Distributed Systems**: Multi-service monitoring
- **SLA Requirements**: Service level agreements
- **Incident Response**: Structured incident handling

#### What to Include
```markdown
# Monitoring Document - [Project Name]

## Monitoring Overview
Observability strategy and approach

## Metrics and Alerting
Key metrics and alert configuration

## Logging Strategy
Log collection and analysis

## Distributed Tracing
Request tracing across services

## Incident Response
Monitoring-driven incident response
```

## Best Practices

### Document Management

#### Version Control
- Keep all documents in version control
- Link document changes to code changes
- Use descriptive commit messages for doc updates

#### Maintenance
- Review documents during sprint planning
- Update documents when making changes
- Archive outdated documents

#### Quality
- Keep documents focused and actionable
- Use consistent formatting and structure
- Include examples and code snippets

### Team Collaboration

#### Ownership
- Assign document owners for each extended doc
- Require reviews for significant changes
- Maintain document quality standards

#### Integration
- Reference documents in code comments
- Link requirements to code implementations
- Use documents in planning meetings

### Tool Integration

#### AI Assistants
- Reference specific documents in prompts
- Use documents to provide context
- Update documents based on AI suggestions

#### Development Tools
- Link documents to issues and pull requests
- Include document reviews in code reviews
- Use documents for onboarding

## Common Pitfalls

### Over-Documentation
- **Problem**: Creating too many documents too early
- **Solution**: Start with Core Trinity, add extended docs as needed
- **Prevention**: Use the decision framework

### Outdated Documents
- **Problem**: Documents that don't reflect current reality
- **Solution**: Regular review and update cycles
- **Prevention**: Include doc updates in development workflow

### Inconsistent Quality
- **Problem**: Some documents are detailed, others are sparse
- **Solution**: Establish quality standards and review processes
- **Prevention**: Use templates and examples

### Poor Integration
- **Problem**: Documents exist but aren't used by the team
- **Solution**: Integrate documents into daily workflow
- **Prevention**: Make documents actionable and valuable

## Next Steps

1. **Assess Your Project**: Use the decision framework to identify needed documents
2. **Start Small**: Add one extended document at a time
3. **Maintain Quality**: Keep documents current and actionable
4. **Integrate with Workflow**: Use documents in planning and development
5. **Iterate**: Continuously improve your documentation approach

## Related Resources

- **[Core Files Guide](core-files-guide.md)**: Master the Core Trinity first
- **[Best Practices](best-practices.md)**: Collaborative development patterns and proven approaches
- **[AI Integration](ai-integration.md)**: Using documents with AI assistants
- **[Getting Started](getting-started.md)**: Learn Prizm basics and setup

---

**Extended documents provide specialized guidance for specific project concerns. Use the decision framework to add the right documents at the right time, and maintain them as living parts of your development process.**