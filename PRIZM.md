# The Prizm Specification Framework

## Philosophy: Specifications as First-Class Citizens

Prizm treats project specifications not as mere documentation, but as the **primary artifacts** that drive development.  Prizm reveals the full spectrum of your project's requirements, design, and implementation strategy.

## Why Specifications Matter

### 1. **Clarity Before Code**
Writing specifications forces you to think through problems before implementing solutions. This prevents costly rewrites and ensures everyone understands what's being built.

### 2. **AI-Native Development**
Modern AI assistants (Claude, Copilot, Cursor) work best with clear context. Well-structured specifications enable AI to generate better code and make more informed decisions.

### 3. **Living Documentation**
Unlike traditional documentation that goes stale, Prizm specifications evolve with your project. They're not afterthoughts—they're the blueprint.

### 4. **Progressive Complexity**
Start with three essential files. Add more only when complexity demands it. This prevents over-engineering while ensuring you're never under-documented.

## The Core Trinity

Every Prizm project begins with three fundamental specifications:

### 📋 requirements.md
**What to build** - Features, user stories, and acceptance criteria
- Defines the problem space
- Establishes success criteria
- Creates shared understanding

### 🏗️ design.md
**How to build it** - Technical architecture and implementation approach
- System architecture
- Technology choices
- Key design decisions

### ✅ tasks.md
**When to build it** - Development phases and task breakdown
- Sprint planning
- Task dependencies
- Team coordination

## Progressive Enhancement

As your project grows, add specifications only when needed:

```
Solo Project (3 files)
    ↓ Add team members
Team Project (+ style.md)
    ↓ Add external services
API Project (+ integrations.md)
    ↓ Add quality requirements
Production Project (+ testing-strategy.md)
    ↓ Add complex domains
Enterprise Project (+ domain.md, architecture.md, ...)
```

## Specification Patterns

### Pattern 1: Feature-First Development
```markdown
## REQ-001: User Authentication
**Priority:** High
**Story:** As a user, I want to securely log in...
**Acceptance Criteria:**
- [ ] Email/password authentication
- [ ] Session management
- [ ] Password reset flow
```

### Pattern 2: Constraint-Driven Design
```markdown
## Performance Constraints
- Response time < 200ms (p95)
- Support 10,000 concurrent users
- Database queries < 50ms
```

### Pattern 3: Task Decomposition
```markdown
## Sprint 1: Foundation
- [ ] Setup development environment
- [ ] Design database schema
- [ ] Implement authentication
    - [ ] User model
    - [ ] Login endpoint
    - [ ] Session management
```

## Working with AI

Prizm specifications are optimized for AI pair programming:

### 1. Context Setting
```bash
# Tell AI about your project context
"Based on requirements.md, implement user authentication"
```

### 2. Design Validation
```bash
# AI reviews your implementation against specs
"Does this implementation match our design.md architecture?"
```

### 3. Task Generation
```bash
# AI helps break down complex features
"Generate subtasks for REQ-003 based on our current design"
```

## Best Practices

### ✅ DO:
- Start with the Core Trinity
- Add specifications only when needed
- Keep specifications up-to-date
- Use clear, numbered requirements
- Link between specifications
- Version control your specs

### ❌ DON'T:
- Create specifications you won't maintain
- Over-engineer for simple projects
- Treat specs as one-time documents
- Write vague requirements
- Skip the Core Trinity
- Hide specs in subfolders

## Evolution Examples

### Simple CLI Tool
```
spec/
├── core/
│   ├── requirements.md (5 requirements)
│   ├── design.md (basic architecture)
│   └── tasks.md (single sprint)
```

### Web API
```
spec/
├── core/
│   ├── requirements.md (10 requirements)
│   ├── design.md (API + database design)
│   └── tasks.md (3 sprints)
├── extended/
│   ├── integrations.md (external services)
│   └── testing-strategy.md (API testing)
```

### Enterprise Platform
```
spec/
├── core/
│   ├── requirements.md (20+ requirements)
│   ├── design.md (distributed architecture)
│   └── tasks.md (6+ sprints)
├── extended/
│   ├── domain.md (complex business logic)
│   ├── architecture.md (microservices)
│   ├── security.md (compliance)
│   ├── ... (7 more specifications)
```

## The Prizm Promise

**Start with three. Scale with confidence.**

Prizm ensures your project has exactly the right amount of specification—never too little, never too much. By treating specifications as first-class citizens, you'll build better software, faster, with less friction.

## Getting Started

1. **Choose your project type** based on complexity
2. **Start with the Core Trinity** - always
3. **Add extended specs** only when you feel their absence
4. **Keep specs living** - update as you build
5. **Use AI assistants** with your specs as context

Remember: The best specification is the one that gets used. Prizm makes that easy.

---

*Prizm is not just a tool—it's a philosophy of building software with clarity, intention, and adaptability.*