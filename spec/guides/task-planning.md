# Effective Task Planning

## The Art of Breaking Down Work

Tasks.md is where strategy meets execution. It transforms requirements and design into actionable work items that teams can actually complete.

## Task Structure Fundamentals

### Basic Task Format
```markdown
- [ ] Task description
  - [ ] Subtask 1
  - [ ] Subtask 2
```

### Detailed Task Format
```markdown
## TASK-001: Implement User Authentication
**Status:** In Progress
**Assigned:** @developer
**Priority:** High
**Estimate:** 3 days
**Dependencies:** Database setup complete

**Description:**
Implement the authentication system based on REQ-001

**Subtasks:**
- [ ] Create user model and migration
- [ ] Implement registration endpoint
- [ ] Implement login endpoint
- [ ] Add JWT token generation
- [ ] Create middleware for protected routes
- [ ] Write unit tests
- [ ] Update API documentation

**Acceptance Criteria:**
- Users can register with email/password
- Users can login and receive JWT token
- Protected routes require valid token
- All endpoints have test coverage
```

## Task Planning Strategies

### 1. Top-Down Decomposition
Start with high-level phases, then break down:

```markdown
## Phase 1: Foundation (Week 1-2)
Set up project infrastructure and core systems

### Sprint 1: Project Setup
- [ ] Initialize repository
  - [ ] Create project structure
  - [ ] Set up Git workflows
  - [ ] Configure CI/CD pipeline
- [ ] Set up development environment
  - [ ] Docker configuration
  - [ ] Local development scripts
  - [ ] Environment variables
- [ ] Configure deployment
  - [ ] Set up cloud infrastructure
  - [ ] Configure staging environment
  - [ ] Set up monitoring

### Sprint 2: Core Systems
- [ ] Database setup
  - [ ] Design schema
  - [ ] Create migrations
  - [ ] Seed test data
- [ ] Authentication system
  - [ ] User registration
  - [ ] Login/logout
  - [ ] Password reset
```

### 2. Feature-Based Planning
Organize by user-facing features:

```markdown
## Feature: User Management

### Backend Tasks
- [ ] User model and database schema
- [ ] CRUD API endpoints
- [ ] Authentication middleware
- [ ] Role-based permissions

### Frontend Tasks  
- [ ] Registration form
- [ ] Login page
- [ ] User profile page
- [ ] Admin user list

### Integration Tasks
- [ ] Connect frontend to API
- [ ] Error handling
- [ ] Loading states
- [ ] Success notifications
```

### 3. Risk-First Planning
Tackle high-risk items early:

```markdown
## High-Risk Tasks (Complete First)

### Technical Risks
- [ ] Validate performance under load
  - [ ] Set up load testing
  - [ ] Test with expected traffic
  - [ ] Optimize bottlenecks
- [ ] Third-party integration testing
  - [ ] Payment gateway integration
  - [ ] Email service setup
  - [ ] SMS provider configuration

### Business Risks
- [ ] Compliance implementation
  - [ ] GDPR data handling
  - [ ] Audit logging
  - [ ] Data retention policies
```

## Planning by Project Type

### Solo Developer Project
```markdown
# Todo CLI Development Plan

## Week 1: Core Functionality
- [ ] Project setup (2 hours)
  - [ ] Initialize npm project
  - [ ] Set up eslint
  - [ ] Create basic structure
- [ ] Implement add command (3 hours)
  - [ ] Parse command arguments
  - [ ] Save to JSON file
  - [ ] Handle errors
- [ ] Implement list command (2 hours)
  - [ ] Read from JSON file
  - [ ] Format output
  - [ ] Add filters
- [ ] Implement complete command (1 hour)
- [ ] Add tests (2 hours)

## Week 2: Polish
- [ ] Add color output
- [ ] Improve error messages
- [ ] Write documentation
- [ ] Publish to npm
```

### Team Project Planning
```markdown
# Web API Development Plan

## Sprint 1: Foundation (Week 1-2)
**Team:** 3 developers, 1 QA

### Developer 1: Infrastructure
- [ ] Set up repository and CI/CD
- [ ] Configure development environment
- [ ] Database setup and migrations
- [ ] Deployment pipeline

### Developer 2: Authentication
- [ ] User model and schema
- [ ] Registration endpoint
- [ ] Login endpoint
- [ ] JWT implementation

### Developer 3: Core API
- [ ] Project structure
- [ ] Base controllers
- [ ] Error handling
- [ ] Validation middleware

### QA: Test Infrastructure
- [ ] Set up test environment
- [ ] API testing framework
- [ ] Test data management
- [ ] CI test integration

## Sprint 2: Features (Week 3-4)
[Detailed breakdown by developer...]
```

### Enterprise Project Planning
```markdown
# Platform Development Plan

## Phase 1: Foundation (Month 1)

### Week 1-2: Architecture Spike
**Team:** 2 Architects, 3 Senior Developers

- [ ] Proof of concept for microservices communication
- [ ] Service mesh evaluation
- [ ] Database strategy validation
- [ ] Security architecture review

### Week 3-4: Infrastructure
**Team:** 2 DevOps, 2 Backend Developers

- [ ] Kubernetes cluster setup
- [ ] CI/CD pipeline for microservices
- [ ] Service discovery configuration
- [ ] Monitoring infrastructure

## Phase 2: Core Services (Month 2-3)

### User Service Team (3 developers)
- [ ] Service scaffolding
- [ ] Database design
- [ ] API implementation
- [ ] Event publishing
- [ ] Service tests

### Project Service Team (4 developers)
[Similar breakdown...]

## Phase 3: Integration (Month 4)
[Cross-team integration tasks...]
```

## Task Estimation Techniques

### 1. T-Shirt Sizing
Quick relative estimation:
```markdown
## Feature Estimates
- User Registration: **M** (3-5 days)
- Password Reset: **S** (1-2 days)
- Two-Factor Auth: **L** (5-8 days)
- Social Login: **XL** (8-13 days)
```

### 2. Story Points
For agile teams:
```markdown
## Sprint Backlog (Total: 21 points)
- [ ] User registration endpoint (5 points)
- [ ] Login endpoint (3 points)
- [ ] Password reset flow (8 points)
- [ ] Profile update endpoint (3 points)
- [ ] Email verification (2 points)
```

### 3. Time-Based Estimates
For fixed timelines:
```markdown
## Week 1 Schedule (40 hours)
- [ ] Monday: Database schema design (4h)
- [ ] Monday: Initial migrations (4h)
- [ ] Tuesday: User model implementation (6h)
- [ ] Tuesday: Basic tests (2h)
- [ ] Wednesday: Registration endpoint (6h)
- [ ] Wednesday: Integration tests (2h)
- [ ] Thursday: Login endpoint (6h)
- [ ] Thursday: JWT implementation (2h)
- [ ] Friday: Documentation (4h)
- [ ] Friday: Code review and fixes (4h)
```

## Dependency Management

### Visual Dependencies
```markdown
## Task Dependencies

```
Database Setup ──┬──→ User Model ──┬──→ Auth API
                 │                  │
                 └──→ Schema Docs   └──→ Tests
```

### Start-to-Start: Auth API needs User Model started
### Finish-to-Start: Tests need Auth API complete
```

### Dependency List
```markdown
## Critical Path

1. **Database Setup** (No dependencies)
   ↓
2. **User Model** (Requires: Database)
   ↓
3. **Authentication API** (Requires: User Model)
   ↓
4. **Frontend Integration** (Requires: Auth API)
   ↓
5. **End-to-End Tests** (Requires: Frontend)
```

## Progress Tracking

### Simple Progress
```markdown
## Sprint Progress

### Completed ✅
- [x] Database setup
- [x] User model
- [x] Registration endpoint

### In Progress 🔄
- [ ] Login endpoint (80% complete)
- [ ] JWT implementation (50% complete)

### Blocked 🚫
- [ ] Email service (waiting for credentials)

### Not Started ⏸
- [ ] Password reset
- [ ] Two-factor auth
```

### Detailed Tracking
```markdown
## Sprint 2 Status Report

### Velocity
- Planned: 21 points
- Completed: 18 points
- Carry over: 3 points

### By Developer
**Alice (6/8 points)**
- [x] User model (3 pts)
- [x] Registration (3 pts)
- [ ] Password reset (2 pts) - 50% done

**Bob (8/8 points)**
- [x] Database setup (5 pts)
- [x] Migrations (3 pts)

**Charlie (4/5 points)**
- [x] API structure (2 pts)
- [x] Error handling (2 pts)
- [ ] Validation (1 pt) - blocked
```

## Common Planning Patterns

### 1. Vertical Slicing
Complete features end-to-end:
```markdown
## Feature: User Profile

- [ ] Backend: Profile model
- [ ] Backend: Profile API endpoint
- [ ] Frontend: Profile form component
- [ ] Frontend: API integration
- [ ] Tests: Backend unit tests
- [ ] Tests: Frontend component tests
- [ ] Tests: E2E profile update flow
```

### 2. Infrastructure First
```markdown
## Week 1: Foundation Only
- [ ] Development environment
- [ ] CI/CD pipeline
- [ ] Database setup
- [ ] Monitoring
- [ ] Error tracking

## Week 2+: Feature Development
[Now build on solid foundation]
```

### 3. MVP Focus
```markdown
## MVP Tasks (Must Have)
- [ ] User registration
- [ ] Basic authentication
- [ ] Core feature X
- [ ] Minimal UI

## Post-MVP (Nice to Have)
- [ ] Social login
- [ ] Advanced feature Y
- [ ] Polish UI
- [ ] Analytics
```

## Anti-Patterns to Avoid

### 1. The Big Bang
❌ "Complete entire backend" (too vague)
✅ Specific, testable tasks

### 2. No Dependencies
❌ All tasks in flat list
✅ Clear task relationships

### 3. Over-Planning
❌ Planning 6 months in detail
✅ Detailed near-term, rough long-term

### 4. Under-Estimation
❌ "Add authentication (1 hour)"
✅ Realistic estimates with buffer

## Task Planning for AI

Write tasks that AI assistants can help implement:

### Clear Context
```markdown
## TASK-005: Implement Password Reset
**Context:** Based on REQ-003 and security requirements
**Reference:** See design.md section on email service

**Implementation:**
- [ ] Create password reset token model
- [ ] Add POST /api/auth/forgot-password endpoint
- [ ] Add POST /api/auth/reset-password endpoint
- [ ] Send reset email with token
- [ ] Expire tokens after 1 hour
```

### Testable Outcomes
```markdown
## TASK-010: Add Rate Limiting
**Success Criteria:**
- [ ] Limit login attempts to 5 per minute
- [ ] Return 429 status when exceeded
- [ ] Reset limits after timeout
- [ ] Log rate limit violations

**Test Command:** `npm run test:rate-limiting`
```

## Templates by Team Size

### Solo Developer
```markdown
# Project Timeline

## Week 1: Core Development
- Monday-Tuesday: Setup and structure
- Wednesday-Thursday: Main features
- Friday: Testing and documentation

## Week 2: Polish and Deploy
- Monday-Tuesday: UI improvements
- Wednesday: Final testing
- Thursday: Deployment
- Friday: Marketing/announcement
```

### Small Team (3-5)
```markdown
# Sprint Plan

## Roles
- Lead: Architecture and reviews
- Dev1: Backend features
- Dev2: Frontend features
- Dev3: Testing and DevOps

## Daily Sync Points
- 9 AM: Quick standup
- 2 PM: Blocker discussion
- 5 PM: Progress update
```

### Large Team (10+)
```markdown
# Release Plan

## Teams
- Platform Team: Infrastructure
- API Team: Backend services
- Web Team: Frontend
- Mobile Team: Apps
- QA Team: Testing
- DevOps: Deployment

## Coordination
- Monday: Team lead sync
- Wednesday: Cross-team demo
- Friday: Sprint review
```

Remember: The best task plan is one that gets updated. Start simple, add detail as needed, and always keep it current with reality.