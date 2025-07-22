# Prizm Specifications

Welcome to the heart of the Prizm framework—the specification templates and guides that help you build better software through clear documentation.

## 📁 Directory Structure

```
spec/
├── core/               # The essential trinity - every project needs these
│   ├── requirements.md # What to build
│   ├── design.md      # How to build it  
│   └── tasks.md       # When to build it
│
├── extended/          # Add as your project grows
│   ├── architecture.md     # System architecture for complex projects
│   ├── constraints.md      # Technical and business limitations
│   ├── deployment.md       # Infrastructure and deployment strategy
│   ├── domain.md          # Business logic and domain modeling
│   ├── integrations.md    # External services and APIs
│   ├── security.md        # Security requirements and implementation
│   ├── style.md           # Coding standards and conventions
│   └── testing-strategy.md # Comprehensive testing approach
│
├── prompts/          # AI-optimized prompts for development phases  
│   ├── planning.md        # Project planning assistance
│   ├── implementation.md  # Code generation guidance
│   ├── review.md         # Code review and refactoring
│   └── maintenance.md    # Ongoing maintenance tasks
│
└── guides/           # Learn how to write effective specifications
    ├── writing-requirements.md # Crafting clear requirements
    ├── design-patterns.md     # Common design documentation patterns
    └── task-planning.md       # Breaking down work effectively
```

## 🚀 Quick Start

### For New Projects

1. **Start with the Core Trinity** - Copy these three files to your project:
   ```bash
   cp spec/core/*.md your-project/
   ```

2. **Fill in requirements.md** first - What are you building?

3. **Design your solution** in design.md - How will you build it?

4. **Plan your work** in tasks.md - When will you build each part?

### For Existing Projects

1. **Start where you are** - Don't try to document everything at once

2. **Begin with requirements.md** - Capture what your system does today

3. **Add other specs as needed** - Only when their absence causes problems

## 📋 The Core Trinity

Every Prizm project starts here:

### [requirements.md](core/requirements.md)
Define **what** you're building through user stories and acceptance criteria.

**When to write:** Before any code, during planning
**When to update:** When scope changes or features are added

### [design.md](core/design.md)  
Describe **how** you'll build it with architecture and technical decisions.

**When to write:** After requirements, before implementation
**When to update:** When architecture evolves or tech stack changes

### [tasks.md](core/tasks.md)
Plan **when** to build each part with sprints and task breakdowns.

**When to write:** After design, before starting work
**When to update:** Daily/weekly as work progresses

## 📈 Extended Specifications

Add these as your project grows:

### When to Add Each Spec

| Specification | Add When You Have |
|--------------|-------------------|
| **style.md** | 2+ developers who need consistency |
| **integrations.md** | External APIs or services |
| **testing-strategy.md** | Quality requirements beyond unit tests |
| **domain.md** | Complex business logic |
| **architecture.md** | Distributed systems or microservices |
| **constraints.md** | Technical limitations or compliance needs |
| **security.md** | Authentication or sensitive data |
| **deployment.md** | Production deployment requirements |

### Progressive Enhancement Path

```
Solo Project → Team Project → Production App → Enterprise System
     ↓              ↓               ↓                 ↓
Core Trinity → +style.md → +integrations.md → +all extended specs
                         → +testing.md
                         → +security.md
```

## 🤖 AI-Optimized Prompts

The prompts directory contains templates for working with AI assistants:

- **[planning.md](prompts/planning.md)** - Let AI help plan your project
- **[implementation.md](prompts/implementation.md)** - Generate code from specs
- **[review.md](prompts/review.md)** - Get AI code reviews
- **[maintenance.md](prompts/maintenance.md)** - Maintain and evolve

### Using Prompts with AI

```bash
# Example with Claude
"Using requirements.md and design.md, implement the user authentication system"

# Example with GitHub Copilot  
"Generate tests for the API endpoints defined in requirements.md"

# Example with Cursor
"Refactor this code to match the patterns in style.md"
```

## 📚 Specification Guides

Learn to write effective specifications:

### [Writing Requirements](guides/writing-requirements.md)
- Structure effective user stories
- Write testable acceptance criteria
- Avoid common requirement pitfalls

### [Design Patterns](guides/design-patterns.md)
- Document architecture decisions
- Choose appropriate design patterns
- Create clear technical documentation

### [Task Planning](guides/task-planning.md)
- Break down complex features
- Estimate and track progress
- Coordinate team efforts

## 🎯 Best Practices

### DO ✅
- Start with the Core Trinity
- Keep specifications up-to-date
- Write for your audience (team, AI, future you)
- Link between specifications
- Use examples and diagrams
- Version control your specs

### DON'T ❌
- Create specs you won't maintain
- Over-document simple projects
- Write vague requirements
- Mix implementation with specification
- Copy templates blindly
- Hide specs from the team

## 🔄 Specification Lifecycle

1. **Create** - Start with templates, customize for your needs
2. **Validate** - Ensure specs are complete and consistent
3. **Implement** - Use specs to guide development
4. **Update** - Keep specs current as project evolves
5. **Review** - Regularly check specs match reality

## 💡 Tips for Success

### For Solo Developers
- Keep it simple - Core Trinity is often enough
- Update tasks.md daily as a todo list
- Use specs as memory aids for future you

### For Small Teams
- Add style.md early to prevent conflicts
- Review specs in team meetings
- Assign spec ownership like code ownership

### For Large Projects
- Treat specs as deliverables
- Include specs in code reviews
- Automate spec validation
- Link specs to implementation

## 🚦 Getting Started Checklist

- [ ] Copy core templates to your project
- [ ] Read the writing guides
- [ ] Fill in requirements.md with 3-5 requirements
- [ ] Create high-level design.md
- [ ] Break down work in tasks.md
- [ ] Start implementing
- [ ] Update specs as you learn

## 📖 Example Projects

See complete specification examples:
- [Simple CLI](../examples/simple-cli/spec/) - Core Trinity only
- [Web API](../examples/web-api/spec/) - Core + 3 extended specs  
- [SaaS App](../examples/saas-app/spec/) - Core + 6 extended specs
- [Microservices](../examples/microservices/spec/) - All 11 specifications

---

Remember: **Specifications are living documents.** They're most valuable when they accurately reflect your project's current state and future direction. Start simple, add complexity only when needed, and always keep them updated.