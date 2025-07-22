# Prizm Template Library

This directory contains template variations for different project types and industries. Each template is designed to be a starting point that you can customize for your specific needs.

## 📁 Template Organization

```
templates/
├── by-project-type/     # Templates organized by project type
│   ├── cli-tool/       # Command-line applications
│   ├── web-api/        # RESTful APIs
│   ├── web-app/        # Frontend applications
│   ├── mobile-app/     # Mobile applications
│   ├── desktop-app/    # Desktop applications
│   ├── library/        # Reusable libraries/packages
│   ├── microservice/   # Individual microservices
│   └── data-pipeline/  # ETL and data processing
│
├── by-industry/        # Industry-specific templates
│   ├── fintech/       # Financial technology
│   ├── healthcare/    # Medical and health
│   ├── e-commerce/    # Online retail
│   ├── education/     # EdTech
│   ├── gaming/        # Game development
│   └── enterprise/    # Enterprise software
│
├── by-framework/       # Framework-specific templates
│   ├── react/         # React applications
│   ├── vue/           # Vue.js applications
│   ├── angular/       # Angular applications
│   ├── django/        # Django applications
│   ├── rails/         # Ruby on Rails
│   ├── spring/        # Spring Boot
│   └── express/       # Express.js APIs
│
└── by-size/           # Templates by team/project size
    ├── solo/          # Individual developers
    ├── small-team/    # 2-5 developers
    ├── medium-team/   # 5-15 developers
    └── large-team/    # 15+ developers
```

## 🎯 Choosing the Right Template

### By Project Type
Choose based on what you're building:
- **CLI Tool**: Terminal applications, scripts, automation
- **Web API**: REST/GraphQL services, backends
- **Web App**: SPAs, traditional web applications
- **Mobile App**: iOS, Android, React Native
- **Library**: NPM packages, shared code

### By Industry
Industry templates include specific compliance and requirements:
- **Fintech**: PCI compliance, transaction requirements
- **Healthcare**: HIPAA compliance, patient data handling
- **E-commerce**: Payment processing, inventory management

### By Framework
Pre-configured for specific technology stacks:
- Includes framework-specific patterns
- Common integrations documented
- Best practices for that ecosystem

### By Team Size
Scaled complexity for your team:
- **Solo**: Minimal process, maximum flexibility
- **Small Team**: Light coordination, clear ownership
- **Large Team**: Full process, detailed coordination

## 📋 Template Components

Each template includes customized versions of:

### Core Files (Always Included)
- `requirements.md` - Tailored to project type
- `design.md` - Architecture patterns for the domain
- `tasks.md` - Typical development phases

### Extended Files (Included as Needed)
- `style.md` - Language/framework specific
- `testing-strategy.md` - Testing approaches
- `security.md` - Security requirements
- Additional files based on complexity

## 🚀 Using Templates

### Method 1: Direct Copy
```bash
# Copy a specific template
cp -r spec/templates/by-project-type/web-api/* my-project/

# Copy from examples directory
cp -r spec/examples/web-api/* my-project/
```

### Method 2: Mix and Match
```bash
# Start with a base
cp -r spec/templates/by-project-type/web-api/* my-project/

# Add industry specifics
cp spec/templates/by-industry/fintech/security.md my-project/

# Add framework patterns
cp spec/templates/by-framework/express/design.md my-project/
```

### Method 3: Browse and Learn
Study different templates to understand patterns and approaches for various project types.

## 🔧 Customization Tips

1. **Start with the closest match** - Don't start from scratch
2. **Remove what you don't need** - Better to delete than ignore
3. **Add your specifics** - Make it yours
4. **Keep the structure** - Maintain Prizm's organization
5. **Evolve as needed** - Templates are starting points

## 📚 Template Examples

### Web API Template
```
requirements.md - API endpoints, authentication, data models
design.md      - RESTful patterns, database design, caching
tasks.md       - API development phases, testing milestones
style.md       - API conventions, error handling
testing.md     - API testing, load testing, mocking
```

### Mobile App Template
```
requirements.md - User flows, offline capability, platforms
design.md      - Mobile architecture, state management
tasks.md       - Platform-specific tasks, store submission
style.md       - Mobile UI patterns, platform guidelines
security.md    - App security, data protection
```

### Enterprise Template
```
requirements.md - Business requirements, compliance needs
design.md      - Enterprise architecture, integration points
tasks.md       - Governance phases, approval gates
+ 8 extended specifications for full coverage
```

## 💡 Template Ideas

Templates cover common project patterns:
- **Generic** - Usable across different projects
- **Complete** - All necessary specification files
- **Documented** - Clear guidance on when to use
- **Tested** - Based on real-world usage