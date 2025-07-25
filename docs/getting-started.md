# Getting Started with Prizm

Welcome to Prizm - the adaptive specification framework for AI-assisted development! This guide will help you understand Prizm's approach and get started with your first project.

## What is Prizm?

Prizm is a specification framework that grows with your project complexity. It provides:

- **Core Trinity**: Essential files every project needs (requirements.md, design.md, tasks.md)
- **Extended Steering Documents**: Optional files that scale with project complexity
- **AI-Optimized Structure**: Designed for seamless AI assistant integration
- **Progressive Complexity**: Start simple, add complexity as needed

## Getting Templates

Prizm is a specification framework - copy the templates you need:

```bash
# Clone or download the repository
git clone https://github.com/your-org/prizm.git

# Copy templates to your project
cp prizm/spec/core/*.md your-project/
```

## Quick Start

### Step 1: Choose Your Templates

Select the appropriate template set for your project complexity:

```bash
# Simple project (Core Trinity only)
cp spec/core/*.md my-project/

# Web API project (add essential extended docs)
cp spec/core/*.md my-project/
cp spec/extended/style.md my-project/
cp spec/extended/testing-strategy.md my-project/

# SaaS application (add comprehensive docs)
cp spec/core/*.md my-project/
cp spec/extended/{style,domain,constraints,testing-strategy,integrations,security}.md my-project/

# Enterprise (full documentation suite)
cp -r spec/ my-project/
```

### Step 2: Customize Your Project

Navigate to your project and customize the generated files:

```bash
cd my-project
ls -la  # See the generated specification files
```

**Project complexity levels:**
```
simple-cli     → Core Trinity only
web-api        → Core Trinity + 3 extended docs
saas-app       → Core Trinity + 6 extended docs  
microservices  → Core Trinity + 11 extended docs
```

### Step 3: Fill Out the Core Trinity

Start with these three essential files:

#### 1. requirements.md
Define what your project needs to accomplish:
```markdown
# Requirements Document

## REQ-001: User Authentication
**User Story:** As a user, I want to log in securely...
**Priority:** High
**Complexity:** Medium

### Acceptance Criteria
1. WHEN a user enters valid credentials THEN they are authenticated
2. WHEN authentication fails THEN clear error messages are shown
```

#### 2. design.md
Outline your technical approach:
```markdown
# Design Document

## Architecture Overview
- Frontend: React with TypeScript
- Backend: Node.js with Express
- Database: PostgreSQL

## API Design
- RESTful endpoints
- JWT authentication
- Input validation
```

#### 3. tasks.md
Plan your development work:
```markdown
# Tasks Document

## Phase 1: Foundation (Week 1)
**TASK-001: Setup Development Environment**
- Priority: High
- Dependencies: None
```

### Step 4: Review and Refine

Validate your specifications manually or with your preferred tools:

```bash
# Review specifications for completeness
# - Are requirements clear and testable?
# - Does design address all requirements?
# - Are tasks specific and actionable?

# Use any validation tools you prefer:
# - Markdown linters
# - Custom scripts
# - Manual review processes
```

### Step 5: Work with Your AI Assistant

Prizm files are designed to work seamlessly with AI assistants:

```
🤖 "Based on these requirements, implement the authentication system"
🤖 "Update the design document with the new API endpoints"
🤖 "Create tasks for implementing the user dashboard"
```

## Template Reference

### Core Templates

- **`spec/core/requirements.md`** - What to build (user stories, features)
- **`spec/core/design.md`** - How to build it (architecture, technical approach)
- **`spec/core/tasks.md`** - When to build it (planning, timeline)

### Extended Templates

- **`spec/extended/style.md`** - Coding standards and conventions
- **`spec/extended/testing-strategy.md`** - Testing approach and quality
- **`spec/extended/integrations.md`** - External service integrations
- **`spec/extended/domain.md`** - Business logic and domain modeling
- **`spec/extended/constraints.md`** - Project limitations and decisions
- **`spec/extended/architecture.md`** - System architecture details

## When to Add Extended Documents

Start with the Core Trinity and add extended documents when:

### style.md
- **Add when:** Team size > 2
- **Purpose:** Coding standards and conventions
- **Benefits:** Consistent code quality, faster onboarding

### testing-strategy.md
- **Add when:** Quality requirements are high
- **Purpose:** Comprehensive testing approach
- **Benefits:** Reduced bugs, confident deployments

### integrations.md
- **Add when:** Multiple external services
- **Purpose:** Third-party integration documentation
- **Benefits:** Clear integration patterns, troubleshooting

### domain.md
- **Add when:** Complex business logic
- **Purpose:** Business domain modeling
- **Benefits:** Clear business rules, better architecture

### constraints.md
- **Add when:** Technical or business limitations
- **Purpose:** Document project constraints
- **Benefits:** Better decision-making, risk management

### architecture.md
- **Add when:** Complex system architecture
- **Purpose:** Detailed architectural documentation
- **Benefits:** System understanding, maintenance

## Working with AI Assistants

### AI Assistant Integration

Prizm works especially well with AI assistants:

```
# Example prompts
"Help me implement the user authentication based on requirements.md"
"Review the design.md and suggest improvements"
"Create the next development tasks based on current progress"
```

### Other AI Assistants

Prizm also works with:
- **GitHub Copilot**: Use spec files for context
- **Cursor**: Point AI to relevant spec files
- **ChatGPT**: Share spec content for focused assistance

### Best Practices for AI Integration

1. **Reference Specific Files**: "Based on requirements.md, implement..."
2. **Update Specs Regularly**: Keep docs current with code changes
3. **Use Structured Prompts**: Follow the document formats
4. **Validate AI Output**: Check against your specifications

## Project Examples

### Simple CLI Tool
Perfect for learning Prizm basics:
- **Files**: requirements.md, design.md, tasks.md
- **Features**: Basic functionality, simple architecture
- **Team**: 1 person, 2 weeks

### Web API
Demonstrates scaling to medium complexity:
- **Files**: Core Trinity + style.md, testing-strategy.md, integrations.md
- **Features**: REST API, authentication, database
- **Team**: 2-3 people, 3 weeks

### SaaS Application
Shows comprehensive documentation:
- **Files**: Core Trinity + 6 extended documents
- **Features**: Multi-tenant, analytics, enterprise features
- **Team**: 8+ developers, 4 months

### Microservices Platform
Full enterprise-scale example:
- **Files**: Core Trinity + 11 extended documents
- **Features**: Distributed architecture, compliance, security
- **Team**: 20+ developers, 6 months

## Common Patterns

### Starting a New Project

1. **Analyze Complexity**: Match your project to our examples
2. **Copy Templates**: Start with appropriate template set
3. **Fill Core Trinity**: Complete the essential three files
4. **Add Extended Docs**: Include as complexity demands
5. **Iterate**: Update docs as project evolves

### Joining an Existing Project

1. **Read README**: Understand project overview
2. **Review Core Trinity**: Grasp requirements, design, tasks
3. **Check Extended Docs**: Understand patterns and constraints
4. **Ask Questions**: Use specs to guide conversations
5. **Contribute**: Update docs as you learn

### Working with Teams

1. **Establish Ownership**: Assign document maintainers
2. **Regular Reviews**: Update docs in planning sessions
3. **Version Control**: Track changes in Git
4. **Link to Code**: Reference specs in code comments
5. **Onboard New Members**: Use docs for team education

## Integration with Development Tools

### Version Control

```bash
# Recommended Git structure
project/
├── spec/              # Prizm specifications
│   ├── requirements.md
│   ├── design.md
│   └── tasks.md
├── src/               # Source code
├── tests/             # Test files
└── docs/              # Additional documentation
```

### Project Management

- **Jira**: Link tasks to task IDs in tasks.md
- **Trello**: Create boards based on task phases
- **Asana**: Import tasks from structured task documents
- **GitHub Issues**: Reference requirements in issue templates

### Documentation Systems

- **GitBook**: Publish specs as team knowledge base
- **Confluence**: Import specs into company wiki
- **Notion**: Create linked database from specifications
- **Markdown Renderers**: Display specs in development tools

## Troubleshooting

### Common Issues

**Problem**: Don't know which documents to create
**Solution**: Start with Core Trinity, add extended docs as needed

**Problem**: Documents get out of sync with code
**Solution**: Update docs during development, not after

**Problem**: Team doesn't use the documents
**Solution**: Integrate docs into development workflow

**Problem**: AI assistants aren't using the context effectively
**Solution**: Reference specific files and sections in prompts

### Getting Help

- **Examples**: Study the four provided examples
- **Documentation**: Read the complete docs directory
- **Community**: Join discussions in GitHub issues
- **Support**: Ask questions in project forums

## Next Steps

1. **Choose Your Example**: Pick the complexity level that matches your project
2. **Copy Templates**: Start with the appropriate template set
3. **Customize**: Adapt the templates to your specific needs
4. **Start Development**: Use the specs to guide your work
5. **Iterate**: Update docs as your project evolves

## Advanced Topics

Once you're comfortable with the basics:

- **[Extended Docs Guide](extended-docs-guide.md)**: When and how to use each document
- **[AI Integration](ai-integration.md)**: Advanced AI assistant techniques
- **[Best Practices](best-practices.md)**: Proven patterns and approaches

---

**Ready to start?** Choose an example that matches your project complexity and begin with the Core Trinity!