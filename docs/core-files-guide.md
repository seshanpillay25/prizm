# Core Files Guide

The Core Trinity forms the foundation of every Prizm project. These three files - requirements.md, design.md, and tasks.md - provide essential structure for any development project, regardless of complexity.

## Why the Core Trinity?

### Universal Applicability
Every software project needs to answer three fundamental questions:
1. **What** are we building? (requirements.md)
2. **How** are we building it? (design.md)
3. **When** are we building it? (tasks.md)

### AI Assistant Optimization
The Core Trinity provides AI assistants with:
- **Clear Context**: Structured information for better responses
- **Consistent Format**: Predictable structure for parsing
- **Comprehensive Coverage**: All essential project information

### Team Collaboration
These files enable:
- **Shared Understanding**: Everyone knows the project goals
- **Decision Documentation**: Rationale for technical choices
- **Progress Tracking**: Clear visibility into project status

## requirements.md - The "What"

### Purpose
Define what your project needs to accomplish, including business requirements, user needs, and success criteria.

### Structure

```markdown
# Requirements Document - [Project Name]

## Introduction
Brief project overview and context

## Business Requirements
### REQ-001: [Requirement Name]
**User Story:** As a [user], I want [goal] so that [benefit]
**Priority:** High/Medium/Low
**Complexity:** High/Medium/Low  
**Dependencies:** REQ-002, REQ-003

#### Acceptance Criteria
1. WHEN [condition] THEN [expected result]
2. WHEN [condition] THEN [expected result]

#### Business Rules
- Rule 1
- Rule 2

#### Edge Cases
- Edge case 1
- Edge case 2

## Non-Functional Requirements
Performance, security, scalability requirements

## Success Metrics
How you'll measure project success

## Assumptions
What you're assuming to be true

## Out of Scope
What you're explicitly not building
```

### Writing Effective Requirements

#### User Stories
```markdown
# ✅ Good
**User Story:** As a project manager, I want to assign tasks to team members so that work is distributed efficiently and accountability is clear.

# ❌ Bad
**User Story:** The system should have task assignment.
```

#### Acceptance Criteria
```markdown
# ✅ Good
1. WHEN a project manager selects a task THEN they see a dropdown of available team members
2. WHEN a team member is assigned THEN they receive a notification email
3. WHEN a task is already assigned THEN the system shows the current assignee

# ❌ Bad
- Task assignment should work
- Users should get notifications
```

#### Business Rules
```markdown
# ✅ Good
- Tasks can only be assigned to team members who are part of the project
- A task can have only one assignee at a time
- Assignee changes must be logged for audit purposes

# ❌ Bad
- Assignment should be tracked
- Only valid users can be assigned
```

### Common Patterns

#### Simple Project (CLI Tool)
```markdown
## REQ-001: Task Creation
**User Story:** As a user, I want to create todo items so that I can track my tasks.
**Priority:** High
**Complexity:** Low

#### Acceptance Criteria
1. WHEN I run `todo add "Buy milk"` THEN a new task is created
2. WHEN I create a task THEN it appears in the task list
3. WHEN I create a task THEN it has a unique ID
```

#### Medium Project (Web API)
```markdown
## REQ-001: User Authentication
**User Story:** As a user, I want to authenticate securely so that my data is protected.
**Priority:** High
**Complexity:** Medium
**Dependencies:** None

#### Acceptance Criteria
1. WHEN a user provides valid credentials THEN they receive a JWT token
2. WHEN a user provides invalid credentials THEN they receive a clear error message
3. WHEN a token expires THEN the user must re-authenticate
4. WHEN a user changes their password THEN all existing tokens are invalidated

#### Business Rules
- Passwords must be at least 8 characters with mixed case and numbers
- Tokens expire after 24 hours
- Failed login attempts are rate-limited after 3 failures

#### Edge Cases
- Account lockout after 5 failed attempts
- Password reset via email verification
- Concurrent login sessions handling
```

#### Complex Project (Enterprise)
```markdown
## REQ-001: Multi-Tenant Workspace Management
**User Story:** As a business owner, I want to create and manage isolated workspaces so that different organizations can use the platform securely.
**Priority:** Critical
**Complexity:** Very High
**Dependencies:** None

#### Acceptance Criteria
1. WHEN a user creates a workspace THEN the system provisions an isolated tenant environment
2. WHEN a user invites members to a workspace THEN the system sends invitation emails and manages pending invitations
3. WHEN a workspace admin manages user roles THEN the system enforces role-based permissions
4. WHEN a user accesses the platform THEN the system routes them to the correct workspace context
5. WHEN a workspace is deleted THEN the system soft-deletes all related data and maintains audit trail

#### Business Rules
- Each workspace has a unique subdomain (e.g., acme.company.com)
- Workspace owners can invite unlimited users on paid plans
- Free tier limited to 5 users per workspace
- Data isolation between workspaces must be complete
- Workspace deletion requires confirmation and has 30-day recovery period

#### Edge Cases
- Subdomain conflicts should suggest alternatives
- Invitation to existing user should switch workspace context
- Workspace owner transfer requires email verification
- Bulk user import should handle validation errors gracefully
```

## design.md - The "How"

### Purpose
Outline your technical approach, architecture decisions, and implementation details.

### Structure

```markdown
# Design Document - [Project Name]

## Overview
High-level technical approach

## Architecture
System architecture and component relationships

## Technology Stack
Chosen technologies and rationale

## API Design
Endpoint specifications and data formats

## Database Design
Schema, relationships, and data modeling

## Security Design
Authentication, authorization, and data protection

## Performance Considerations
Scalability, caching, and optimization strategies

## Deployment Architecture
Infrastructure and deployment strategies
```

### Writing Effective Design Documents

#### Architecture Diagrams
```markdown
## System Architecture

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   React     │    │   Express   │    │ PostgreSQL  │
│  Frontend   │◄───┤   API       │◄───┤  Database   │
│             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
```

#### Technology Decisions
```markdown
## Technology Stack

### Frontend
- **Framework:** React 18 with TypeScript
- **State Management:** Redux Toolkit
- **Styling:** Tailwind CSS
- **Rationale:** Team expertise, strong ecosystem, type safety

### Backend
- **Runtime:** Node.js 18+
- **Framework:** Express.js
- **Database:** PostgreSQL 14+
- **Rationale:** JavaScript consistency, proven scalability, ACID compliance
```

#### API Specifications
```markdown
## API Design

### Authentication Endpoints

#### POST /api/auth/login
```json
{
  "email": "user@example.com",
  "password": "securepassword"
}
```

**Response:**
```json
{
  "token": "jwt-token-here",
  "user": {
    "id": "user-id",
    "email": "user@example.com",
    "name": "John Doe"
  }
}
```
```

### Common Patterns

#### Simple Project Design
```markdown
## Architecture Overview
Simple command-line tool with file-based storage

## Technology Stack
- **Language:** JavaScript (Node.js)
- **Storage:** JSON files
- **CLI Framework:** Commander.js

## Data Storage
```json
{
  "tasks": [
    {
      "id": 1,
      "title": "Buy milk",
      "completed": false,
      "createdAt": "2024-01-01T10:00:00Z"
    }
  ]
}
```
```

#### Medium Project Design
```markdown
## Architecture Overview
RESTful API with database persistence and authentication

## System Architecture
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Client    │    │   Express   │    │ PostgreSQL  │
│ Application │◄───┤   Server    │◄───┤  Database   │
│             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
                           │
                   ┌─────────────┐
                   │    Redis    │
                   │    Cache    │
                   └─────────────┘
```

## Database Schema
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE tasks (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    title VARCHAR(255) NOT NULL,
    completed BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT NOW()
);
```
```

## tasks.md - The "When"

### Purpose
Plan your development work with clear tasks, timelines, and dependencies.

### Structure

```markdown
# Tasks Document - [Project Name]

## Overview
Project timeline and team structure

## Development Phases
High-level project phases

## Task Breakdown
### Phase 1: [Phase Name]
**TASK-001: [Task Name]**
- **Priority:** High/Medium/Low
- **Dependencies:** TASK-002, TASK-003

**Deliverables:**
- [ ] Specific deliverable 1
- [ ] Specific deliverable 2

**Acceptance Criteria:**
- Criteria for task completion

## Risk Management
Identified risks and mitigation strategies

## Success Metrics
How you'll measure project success
```

### Writing Effective Tasks

#### Task Breakdown
```markdown
# ✅ Good
**TASK-001: User Authentication System**
- **Priority:** High
- **Dependencies:** TASK-002 (Database Setup)

**Deliverables:**
- [ ] JWT token generation and validation
- [ ] Login endpoint with rate limiting
- [ ] Password hashing with bcrypt
- [ ] Unit tests for authentication logic
- [ ] Integration tests for login flow

**Acceptance Criteria:**
- All authentication endpoints return proper HTTP status codes
- JWT tokens expire after 24 hours
- Rate limiting prevents brute force attacks
- Password hashing uses industry-standard algorithms
- Test coverage exceeds 90%

# ❌ Bad
**TASK-001: Do authentication**
- **Priority:** Important
```

#### Dependencies
```markdown
# ✅ Good
**TASK-003: User Registration**
- **Dependencies:** 
  - TASK-001 (Database Schema) - Need user table
  - TASK-002 (Email Service) - Need email verification

# ❌ Bad
**TASK-003: User Registration**
- **Dependencies:** Other stuff needs to be done first
```

### Common Patterns

#### Simple Project Tasks
```markdown
## Phase 1: Core Functionality (Week 1)

**TASK-001: Project Setup**
- **Priority:** High
- **Dependencies:** None

**Deliverables:**
- [ ] Node.js project initialized
- [ ] Package.json configured
- [ ] Basic CLI structure with Commander.js
- [ ] Git repository setup

**TASK-002: Task Management Logic**
- **Priority:** High
- **Dependencies:** TASK-001

**Deliverables:**
- [ ] Add task functionality
- [ ] List tasks functionality
- [ ] Complete task functionality
- [ ] JSON file storage implementation
```

#### Medium Project Tasks
```markdown
## Phase 1: Foundation (Week 1)

**TASK-001: Development Environment**
- **Priority:** Critical
- **Dependencies:** None

**Deliverables:**
- [ ] Docker development environment
- [ ] PostgreSQL database setup
- [ ] Redis cache setup
- [ ] Environment configuration

**TASK-002: Database Schema**
- **Priority:** Critical
- **Dependencies:** TASK-001

**Deliverables:**
- [ ] User table with authentication fields
- [ ] Task table with relationships
- [ ] Database migrations
- [ ] Seed data for development
```

#### Complex Project Tasks
```markdown
## Phase 1: Foundation & Architecture (Weeks 1-3)

**TASK-001: Multi-Tenant Database Schema**
- **Priority:** Critical
- **Dependencies:** None

**Deliverables:**
- [ ] Tenant-aware database schema
- [ ] Row-level security policies
- [ ] Migration system for tenant data
- [ ] Performance indexes for multi-tenancy
- [ ] Data seeding for multiple tenants

**Acceptance Criteria:**
- All queries are tenant-scoped automatically
- Data isolation is complete between tenants
- Performance tests show <100ms query times
- Migration system handles tenant-specific changes
- Seed data creates realistic test scenarios
```

## Best Practices

### General Guidelines

#### Keep It Current
- Update documents during development, not after
- Use version control to track changes
- Link documents to code changes in commit messages

#### Make It Actionable
- Write specific, measurable requirements
- Include clear acceptance criteria
- Provide detailed task deliverables

#### Focus on Value
- Explain the "why" behind decisions
- Connect requirements to business value
- Prioritize ruthlessly

### AI Assistant Integration

#### Reference Specific Sections
```bash
# ✅ Good
"Based on REQ-001 in requirements.md, implement the authentication system"

# ❌ Bad
"Implement authentication"
```

#### Update Documents During Development
```bash
# After implementing a feature
"Update the design.md to reflect the new API endpoints we just created"
```

#### Use for Planning
```bash
# Before starting work
"Based on the current tasks.md, what should I work on next?"
```

### Team Collaboration

#### Document Ownership
- Assign document maintainers
- Require reviews for major changes
- Keep documents in version control

#### Regular Updates
- Review documents in sprint planning
- Update status in daily standups
- Reflect changes in retrospectives

#### Onboarding
- Use documents for new team member orientation
- Include document reading in onboarding checklists
- Encourage questions and clarifications

## Troubleshooting

### Common Issues

**Documents get out of sync with code**
- Solution: Update documents during development
- Prevention: Include doc updates in definition of done

**Requirements are too vague**
- Solution: Add specific acceptance criteria
- Prevention: Use the GIVEN-WHEN-THEN format

**Tasks are too large**
- Solution: Break down into smaller, specific deliverables
- Prevention: Use 1-3 day task estimates

**Design decisions aren't documented**
- Solution: Add rationale for technical choices
- Prevention: Include "why" alongside "what" and "how"

## Next Steps

Once you're comfortable with the Core Trinity:

1. **[Extended Docs Guide](extended-docs-guide.md)**: Learn when to add additional documents
2. **[AI Integration](ai-integration.md)**: Advanced AI assistant techniques
3. **[Best Practices](best-practices.md)**: Proven patterns and approaches

---

**The Core Trinity provides the foundation for every project. Master these three files, and you'll have the essential structure for successful development!**