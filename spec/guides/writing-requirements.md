# Writing Effective Requirements

## The Art of Clear Requirements

Good requirements are the foundation of successful projects. They bridge the gap between what stakeholders want and what developers build.

## Requirement Structure

### Basic Format
```markdown
## REQ-XXX: [Short Descriptive Title]
**Priority:** [High/Medium/Low]
**Category:** [Feature/Performance/Security/UX]
**Story:** As a [user type], I want [capability] so that [benefit]

**Acceptance Criteria:**
- [ ] Specific, measurable outcome 1
- [ ] Specific, measurable outcome 2
- [ ] Specific, measurable outcome 3

**Notes:**
- Additional context or constraints
- Dependencies on other requirements
```

## Writing Principles

### 1. Be Specific
❌ **Poor:** "The system should be fast"
✅ **Good:** "API responses must return within 200ms for 95% of requests"

### 2. Make it Testable
❌ **Poor:** "The UI should be user-friendly"
✅ **Good:** "Users can complete checkout in under 3 clicks from cart"

### 3. Avoid Implementation Details
❌ **Poor:** "Use PostgreSQL to store user data in a normalized schema"
✅ **Good:** "System must persistently store user profiles with sub-second retrieval"

### 4. Focus on the Why
❌ **Poor:** "Add a search bar"
✅ **Good:** "As a user, I want to search products by name so that I can quickly find what I need"

## Requirement Categories

### Functional Requirements
Define what the system should do:
```markdown
## REQ-001: User Registration
**Priority:** High
**Category:** Feature
**Story:** As a new user, I want to create an account so that I can access personalized features

**Acceptance Criteria:**
- [ ] User can register with email and password
- [ ] Email validation prevents invalid formats
- [ ] Duplicate emails show appropriate error
- [ ] Successful registration logs user in automatically
```

### Non-Functional Requirements
Define how the system should behave:
```markdown
## REQ-010: API Performance
**Priority:** High
**Category:** Performance
**Story:** As an API consumer, I want fast responses so that my application remains responsive

**Acceptance Criteria:**
- [ ] 95th percentile response time < 200ms
- [ ] 99th percentile response time < 500ms
- [ ] System handles 1000 requests/second
- [ ] No more than 0.1% error rate under normal load
```

### Constraint Requirements
Define limitations and boundaries:
```markdown
## REQ-020: Data Privacy Compliance
**Priority:** High
**Category:** Security
**Story:** As a business, we must comply with GDPR so that we can legally operate in the EU

**Acceptance Criteria:**
- [ ] User data can be exported on request
- [ ] User accounts can be fully deleted
- [ ] Consent is obtained before data collection
- [ ] Data retention follows defined policies
```

## Progressive Requirement Detail

### For Simple Projects
Keep requirements brief and focused:
```markdown
## REQ-001: Create Todo
- User can add new todo with title
- Todo is saved persistently
- New todos appear in the list
```

### For Team Projects
Add more context and criteria:
```markdown
## REQ-001: Create Todo Item
**Priority:** High
**Story:** As a user, I want to create todo items

**Acceptance Criteria:**
- [ ] Title is required (max 200 chars)
- [ ] Description is optional (max 1000 chars)
- [ ] Due date is optional
- [ ] Creation timestamp is automatic
- [ ] New todos appear at top of list
```

### For Enterprise Projects
Include comprehensive details:
```markdown
## REQ-001: Multi-tenant Todo Creation
**Priority:** High
**Category:** Feature
**Story:** As a team member, I want to create todos within my workspace

**Acceptance Criteria:**
- [ ] Todos are scoped to user's current workspace
- [ ] Title supports Unicode (max 200 chars)
- [ ] Rich text description with attachments (max 10MB)
- [ ] Due date with timezone handling
- [ ] Priority levels (Critical/High/Medium/Low)
- [ ] Assignment to team members
- [ ] Audit trail for all changes
- [ ] Real-time sync across devices

**Performance Criteria:**
- [ ] Creation completes in < 100ms
- [ ] Supports 1000 concurrent creates
- [ ] No data loss under failure

**Security:**
- [ ] Only workspace members can create
- [ ] Input sanitization prevents XSS
- [ ] Rate limiting prevents abuse
```

## Requirement Relationships

### Dependencies
```markdown
## REQ-003: Password Reset
**Depends on:** REQ-001 (User Registration), REQ-002 (Email Service)
```

### Conflicts
```markdown
## REQ-010: Offline Mode
**Conflicts with:** REQ-009 (Real-time Sync)
**Resolution:** Sync when connection restored
```

### Groups
```markdown
## Authentication Requirements
- REQ-001: User Registration
- REQ-002: User Login  
- REQ-003: Password Reset
- REQ-004: Two-Factor Auth
```

## AI-Friendly Requirements

Write requirements that AI can understand and implement:

### 1. Use Consistent Formatting
AI can parse structured requirements better than prose.

### 2. Include Examples
```markdown
**Example Request:**
POST /api/todos
{
  "title": "Buy groceries",
  "dueDate": "2024-01-15T10:00:00Z"
}

**Example Response:**
{
  "id": "todo_123",
  "title": "Buy groceries",
  "status": "pending"
}
```

### 3. Define Edge Cases
```markdown
**Edge Cases:**
- Empty title returns 400 error
- Past due dates show warning
- Maximum 1000 todos per user
```

## Requirement Evolution

### Start Simple
```markdown
## REQ-001: User Login
- Email and password authentication
- Session management
```

### Add Detail When Needed
```markdown
## REQ-001: User Login
**Priority:** High
**Story:** As a user, I want to securely access my account

**Acceptance Criteria:**
- [ ] Email/password authentication
- [ ] Sessions expire after 24 hours
- [ ] Failed login attempt limiting
- [ ] "Remember me" option
```

### Expand for Production
```markdown
## REQ-001: Secure User Authentication
[... previous content ...]

**Security Requirements:**
- [ ] Passwords hashed with bcrypt (12 rounds)
- [ ] Session tokens use secure random generation
- [ ] HTTPS required for all auth endpoints
- [ ] CSRF protection on state-changing operations

**Monitoring:**
- [ ] Track failed login attempts
- [ ] Alert on credential stuffing patterns
- [ ] Log all authentication events
```

## Common Pitfalls

### 1. Solution Masquerading as Requirement
❌ "Use Redis for session storage"
✅ "Sessions must persist across server restarts"

### 2. Vague Success Criteria
❌ "Good performance"
✅ "Page loads in under 2 seconds on 3G connection"

### 3. Missing Edge Cases
❌ "Users can upload images"
✅ "Users can upload images (JPEG/PNG, max 5MB, max 10 per post)"

### 4. Assumed Knowledge
❌ "Standard authentication flow"
✅ "Email/password with optional 2FA, password reset via email"

## Checklist for Good Requirements

- [ ] Has unique identifier (REQ-XXX)
- [ ] States who needs it and why
- [ ] Includes measurable acceptance criteria
- [ ] Free of implementation details
- [ ] Testable by QA/users
- [ ] Prioritized appropriately
- [ ] Dependencies identified
- [ ] Edge cases considered
- [ ] Examples provided where helpful

## Templates by Project Type

### API Endpoint Requirement
```markdown
## REQ-XXX: [Endpoint Name]
**Method:** [GET/POST/PUT/DELETE]
**Path:** /api/resource
**Purpose:** [What this endpoint does]

**Request:**
- Parameters: [list]
- Body: [schema/example]
- Headers: [required headers]

**Response:**
- Success: [status code and schema]
- Errors: [possible error codes]

**Acceptance Criteria:**
- [ ] [Specific behaviors]
```

### UI Feature Requirement
```markdown
## REQ-XXX: [Feature Name]
**Screen:** [Where this appears]
**Purpose:** [User goal]

**Visual Requirements:**
- [Layout description]
- [Interactive elements]
- [States and transitions]

**Behavior:**
- [ ] [User interactions]
- [ ] [System responses]
- [ ] [Error handling]
```

Remember: Requirements are communication tools. Write for your audience—whether that's developers, stakeholders, or AI assistants.