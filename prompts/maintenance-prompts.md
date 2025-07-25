# Maintenance Prompts

This collection of prompts helps you maintain and update Prizm specifications using AI assistants. These prompts ensure your documentation stays current, accurate, and valuable as your project evolves.

## Specification-Driven Maintenance Workflows

### Regular Maintenance Check
```
Let's perform our weekly/monthly Prizm maintenance:

1. Review all specification files for currency and accuracy
2. Compare specifications against current implementation
3. Analyze specifications for completeness and consistency

Based on the results, help me:
- Fix any validation issues found
- Update specifications that are outdated
- Identify documentation debt
- Plan specification improvements
- Update task completion status

Please provide a maintenance action plan with priorities.
```

### Post-Release Specification Sync
```
We just completed a release. Help me sync our Prizm specifications:

Release Details:
- Features implemented: {features_list}
- Bug fixes: {bug_fixes}
- Technical changes: {technical_changes}

Maintenance Tasks:
1. Review specifications to assess current state
2. Update requirements.md for implemented features
3. Sync design.md with architectural changes
4. Update tasks.md to reflect completed work
5. Assess updated project status from specifications

Help me systematically update each specification file and ensure consistency.
```

### Specification Quality Improvement
```
Let's improve our specification quality through systematic review:

Current Analysis:
1. Review all specifications for completeness and accuracy
2. Compare specifications against current implementation

Quality Goals:
- Reduce validation errors to zero
- Improve specification completeness
- Enhance team usability
- Better AI assistant integration

Based on the specification review, help me:
1. Fix all validation issues
2. Identify gaps in documentation
3. Improve specification structure
4. Add missing cross-references
5. Enhance template usage

Provide a quality improvement roadmap.
```

### Team Onboarding Documentation Refresh
```
Help me refresh our specifications for better team onboarding:

1. Review all specification files as a comprehensive guide
2. Analyze current documentation for new team member perspective

Onboarding Improvements Needed:
- Simplify complex concepts
- Add more examples
- Improve navigation
- Update outdated information
- Enhance template usage guidance

Based on the specification review, help me update documentation to be more onboarding-friendly.
```

## Specification Update Prompts

### Requirements Update

#### Template
```
Update requirements.md to reflect the following changes:

Current Requirements:
[paste current requirement section]

Changes Made:
- {change_description_1}
- {change_description_2}
- {change_description_3}

Implementation Details:
- Code changes: {code_changes}
- New functionality: {new_functionality}
- Modified behavior: {modified_behavior}

Please update the requirements to:
1. Reflect the current implementation accurately
2. Update acceptance criteria based on changes
3. Modify business rules if applicable
4. Add new edge cases discovered
5. Update success metrics if relevant
6. Maintain consistency with other requirements

Provide the updated requirement section with clear change annotations.
```

#### Example
```
Update requirements.md to reflect the following changes:

Current Requirements:
REQ-001: User Authentication
- Basic JWT authentication
- Login with email and password
- Session timeout after 24 hours

Changes Made:
- Added multi-factor authentication support
- Implemented biometric login for mobile
- Extended session timeout to 7 days with refresh tokens

Implementation Details:
- Code changes: Added MFA middleware, biometric API integration
- New functionality: TOTP support, fingerprint authentication
- Modified behavior: Session management with refresh tokens

Please update the requirements to:
1. Reflect the current implementation accurately
2. Update acceptance criteria based on changes
3. Modify business rules if applicable
4. Add new edge cases discovered
5. Update success metrics if relevant
6. Maintain consistency with other requirements

Provide the updated requirement section with clear change annotations.
```

### Design Document Update

#### Template
```
Update design.md to reflect the following architectural changes:

Current Design Section:
[paste current design section]

Architectural Changes:
- {architectural_change_1}
- {architectural_change_2}
- {architectural_change_3}

Technical Details:
- Technology changes: {technology_changes}
- Pattern changes: {pattern_changes}
- Integration changes: {integration_changes}

Please update the design to:
1. Reflect current architecture accurately
2. Update component diagrams and relationships
3. Modify technology stack information
4. Update integration patterns
5. Add new architectural decisions with rationale
6. Maintain consistency across design sections

Provide the updated design section with architectural decision records.
```

#### Example
```
Update design.md to reflect the following architectural changes:

Current Design Section:
## System Architecture
- Monolithic Express.js application
- Single PostgreSQL database
- In-memory session storage

Architectural Changes:
- Migrated to microservices architecture
- Implemented event-driven communication
- Added distributed caching with Redis

Technical Details:
- Technology changes: Split into 5 microservices, added message queue
- Pattern changes: Implemented CQRS, event sourcing for audit
- Integration changes: API Gateway, service mesh with Istio

Please update the design to:
1. Reflect current architecture accurately
2. Update component diagrams and relationships
3. Modify technology stack information
4. Update integration patterns
5. Add new architectural decisions with rationale
6. Maintain consistency across design sections

Provide the updated design section with architectural decision records.
```

### Tasks Update

#### Template
```
Update tasks.md to reflect current project progress:

Current Tasks Section:
[paste current tasks section]

Progress Updates:
- Completed tasks: {completed_tasks}
- In-progress tasks: {in_progress_tasks}
- Blocked tasks: {blocked_tasks}
- New tasks discovered: {new_tasks}

Timeline Changes:
- Delays: {delays_and_reasons}
- Accelerations: {accelerations_and_reasons}
- Scope changes: {scope_changes}

Please update the tasks to:
1. Mark completed tasks as done
2. Update progress on in-progress tasks
3. Identify and document blockers
4. Add newly discovered tasks
5. Adjust timeline based on actual progress
6. Update resource assignments if changed

Provide updated task section with progress tracking.
```

#### Example
```
Update tasks.md to reflect current project progress:

Current Tasks Section:
## Phase 1: Authentication System (Weeks 1-2)
- TASK-001: Basic JWT authentication (In Progress)
- TASK-002: User registration (Pending)
- TASK-003: Password reset (Pending)

Progress Updates:
- Completed tasks: TASK-001 (JWT auth with MFA added)
- In-progress tasks: TASK-002 (User registration 80% complete)
- Blocked tasks: TASK-003 (waiting for email service approval)
- New tasks discovered: TASK-004 (biometric auth), TASK-005 (social login)

Timeline Changes:
- Delays: TASK-003 delayed 1 week due to email service procurement
- Accelerations: TASK-001 completed early with enhanced security
- Scope changes: Added biometric and social login requirements

Please update the tasks to:
1. Mark completed tasks as done
2. Update progress on in-progress tasks
3. Identify and document blockers
4. Add newly discovered tasks
5. Adjust timeline based on actual progress
6. Update resource assignments if changed

Provide updated task section with progress tracking.
```

## Consistency Maintenance Prompts

### Cross-Document Consistency Check

#### Template
```
Check consistency between these Prizm specification files:

Files to Check:
1. requirements.md: [paste relevant sections]
2. design.md: [paste relevant sections]
3. tasks.md: [paste relevant sections]
4. {other_relevant_files}: [paste relevant sections]

Consistency Areas to Verify:
- Requirement-to-design alignment
- Design-to-task alignment
- Technology stack consistency
- Timeline consistency
- Success metrics alignment

Please identify:
1. Any inconsistencies between documents
2. Missing links between requirements and design
3. Gaps in task coverage for requirements
4. Conflicting information across documents
5. Outdated references or information

Provide specific recommendations for resolving inconsistencies.
```

#### Example
```
Check consistency between these Prizm specification files:

Files to Check:
1. requirements.md: REQ-001 specifies JWT authentication with MFA
2. design.md: Shows basic JWT implementation without MFA
3. tasks.md: No tasks for MFA implementation
4. style.md: No security patterns for MFA

Consistency Areas to Verify:
- Requirement-to-design alignment
- Design-to-task alignment
- Technology stack consistency
- Timeline consistency
- Success metrics alignment

Please identify:
1. Any inconsistencies between documents
2. Missing links between requirements and design
3. Gaps in task coverage for requirements
4. Conflicting information across documents
5. Outdated references or information

Provide specific recommendations for resolving inconsistencies.
```

### Specification Health Check

#### Template
```
Perform a comprehensive health check on these Prizm specifications:

Specification Files:
- requirements.md: [paste or reference file]
- design.md: [paste or reference file]
- tasks.md: [paste or reference file]
- {extended_docs}: [paste or reference files]

Health Check Areas:
1. Content Quality
   - Are requirements clear and testable?
   - Are design decisions documented with rationale?
   - Are tasks specific and actionable?

2. Currency
   - Do specifications match current implementation?
   - Are recent changes reflected?
   - Are outdated sections updated?

3. Completeness
   - Are all features covered?
   - Are edge cases documented?
   - Are non-functional requirements included?

4. Consistency
   - Are documents aligned with each other?
   - Are naming conventions consistent?
   - Are references accurate?

Provide a detailed health assessment with specific improvement recommendations.
```

#### Example
```
Perform a comprehensive health check on these Prizm specifications:

Specification Files:
- requirements.md: 8 requirements for project management SaaS
- design.md: Microservices architecture with event sourcing
- tasks.md: 16-week development timeline with 20 tasks
- style.md: TypeScript coding standards
- security.md: Zero-trust security model

Health Check Areas:
1. Content Quality
   - Are requirements clear and testable?
   - Are design decisions documented with rationale?
   - Are tasks specific and actionable?

2. Currency
   - Do specifications match current implementation?
   - Are recent changes reflected?
   - Are outdated sections updated?

3. Completeness
   - Are all features covered?
   - Are edge cases documented?
   - Are non-functional requirements included?

4. Consistency
   - Are documents aligned with each other?
   - Are naming conventions consistent?
   - Are references accurate?

Provide a detailed health assessment with specific improvement recommendations.
```

### Gap Analysis

#### Template
```
Identify gaps in Prizm specification coverage:

Current Implementation:
- Features implemented: {implemented_features}
- Code structure: {code_structure}
- Technology stack: {technology_stack}

Current Specifications:
- Requirements covered: {requirements_covered}
- Design documented: {design_documented}
- Tasks planned: {tasks_planned}

Gap Analysis Areas:
1. Missing Requirements
   - Features without requirements
   - Undocumented business rules
   - Missing acceptance criteria

2. Design Gaps
   - Undocumented architectural decisions
   - Missing integration patterns
   - Unexplained technology choices

3. Task Coverage
   - Work done without tasks
   - Missing estimates and timelines
   - Undocumented dependencies

Please identify:
1. Specific gaps in each specification area
2. Priority level for addressing each gap
3. Recommended actions to close gaps
4. Timeline for gap resolution

Provide a comprehensive gap analysis with action plan.
```

#### Example
```
Identify gaps in Prizm specification coverage:

Current Implementation:
- Features implemented: User auth, project management, real-time notifications
- Code structure: Express.js with microservices, PostgreSQL, Redis
- Technology stack: Node.js, TypeScript, React, Docker

Current Specifications:
- Requirements covered: Authentication, project CRUD, basic notifications
- Design documented: Monolithic architecture, basic database schema
- Tasks planned: Authentication and project management only

Gap Analysis Areas:
1. Missing Requirements
   - Features without requirements
   - Undocumented business rules
   - Missing acceptance criteria

2. Design Gaps
   - Undocumented architectural decisions
   - Missing integration patterns
   - Unexplained technology choices

3. Task Coverage
   - Work done without tasks
   - Missing estimates and timelines
   - Undocumented dependencies

Please identify:
1. Specific gaps in each specification area
2. Priority level for addressing each gap
3. Recommended actions to close gaps
4. Timeline for gap resolution

Provide a comprehensive gap analysis with action plan.
```

## Quality Improvement Prompts

### Specification Enhancement

#### Template
```
Enhance this specification section for better clarity and completeness:

Current Specification:
[paste current specification section]

Enhancement Goals:
- Improve clarity and readability
- Add missing details
- Enhance actionability
- Improve AI assistant compatibility

Areas to Enhance:
1. Language and Structure
   - Clear, concise language
   - Logical organization
   - Consistent formatting

2. Content Completeness
   - All necessary information included
   - Examples and code snippets
   - Edge cases and exceptions

3. Actionability
   - Specific acceptance criteria
   - Clear implementation guidance
   - Measurable outcomes

4. AI Optimization
   - Structured format for AI parsing
   - Clear context and relationships
   - Specific prompts and patterns

Please provide an enhanced version with improvement explanations.
```

#### Example
```
Enhance this specification section for better clarity and completeness:

Current Specification:
REQ-001: User Authentication
Users should be able to log in to the system securely.

Enhancement Goals:
- Improve clarity and readability
- Add missing details
- Enhance actionability
- Improve AI assistant compatibility

Areas to Enhance:
1. Language and Structure
   - Clear, concise language
   - Logical organization
   - Consistent formatting

2. Content Completeness
   - All necessary information included
   - Examples and code snippets
   - Edge cases and exceptions

3. Actionability
   - Specific acceptance criteria
   - Clear implementation guidance
   - Measurable outcomes

4. AI Optimization
   - Structured format for AI parsing
   - Clear context and relationships
   - Specific prompts and patterns

Please provide an enhanced version with improvement explanations.
```

### Documentation Standards Update

#### Template
```
Update specification sections to meet current documentation standards:

Current Standards:
- Format: [paste current format requirements]
- Style: [paste style guidelines]
- Content: [paste content requirements]

Specification to Update:
[paste specification section]

Standards Compliance Check:
1. Format Standards
   - Consistent markdown formatting
   - Proper heading hierarchy
   - Structured templates

2. Style Standards
   - Clear, professional language
   - Consistent terminology
   - Appropriate level of detail

3. Content Standards
   - Complete information
   - Accurate and current
   - Actionable guidance

Please update the specification to meet all current standards with explanations of changes made.
```

#### Example
```
Update specification sections to meet current documentation standards:

Current Standards:
- Format: Markdown with consistent heading structure, code blocks, tables
- Style: Professional tone, active voice, specific language
- Content: Complete acceptance criteria, business rules, edge cases

Specification to Update:
## Database
We use PostgreSQL for data storage. It's good for our needs.

Standards Compliance Check:
1. Format Standards
   - Consistent markdown formatting
   - Proper heading hierarchy
   - Structured templates

2. Style Standards
   - Clear, professional language
   - Consistent terminology
   - Appropriate level of detail

3. Content Standards
   - Complete information
   - Accurate and current
   - Actionable guidance

Please update the specification to meet all current standards with explanations of changes made.
```

## Change Management Prompts

### Change Impact Analysis

#### Template
```
Analyze the impact of these changes on Prizm specifications:

Proposed Changes:
- {change_1}: {description}
- {change_2}: {description}
- {change_3}: {description}

Current Specifications:
- requirements.md: [paste relevant sections]
- design.md: [paste relevant sections]
- tasks.md: [paste relevant sections]

Impact Analysis:
1. Requirements Impact
   - Which requirements are affected?
   - Do new requirements need to be added?
   - Are acceptance criteria still valid?

2. Design Impact
   - Are architectural changes needed?
   - Do technology choices need updating?
   - Are integration patterns affected?

3. Task Impact
   - Which tasks are affected?
   - Are new tasks needed?
   - How does timeline change?

4. Risk Assessment
   - What are the risks of these changes?
   - Are there potential conflicts?
   - What mitigation strategies are needed?

Provide detailed impact analysis with recommended specification updates.
```

#### Example
```
Analyze the impact of these changes on Prizm specifications:

Proposed Changes:
- Add real-time collaboration features
- Implement offline-first architecture
- Integrate with third-party calendar systems

Current Specifications:
- requirements.md: Basic project management features
- design.md: REST API with PostgreSQL
- tasks.md: 12-week development timeline

Impact Analysis:
1. Requirements Impact
   - Which requirements are affected?
   - Do new requirements need to be added?
   - Are acceptance criteria still valid?

2. Design Impact
   - Are architectural changes needed?
   - Do technology choices need updating?
   - Are integration patterns affected?

3. Task Impact
   - Which tasks are affected?
   - Are new tasks needed?
   - How does timeline change?

4. Risk Assessment
   - What are the risks of these changes?
   - Are there potential conflicts?
   - What mitigation strategies are needed?

Provide detailed impact analysis with recommended specification updates.
```

### Version Control for Specifications

#### Template
```
Create a version control strategy for these specification changes:

Current Version: {current_version}
Proposed Changes: {proposed_changes}

Change Categories:
- Major changes: {major_changes}
- Minor changes: {minor_changes}
- Patch changes: {patch_changes}

Versioning Requirements:
1. Semantic Versioning
   - Major: Breaking changes to requirements
   - Minor: New features or capabilities
   - Patch: Bug fixes or clarifications

2. Change Documentation
   - What changed and why
   - Impact on implementation
   - Migration guidance

3. Rollback Strategy
   - How to revert changes
   - Compatibility considerations
   - Risk mitigation

Please provide:
1. Appropriate version number for changes
2. Detailed change log entry
3. Migration guide for implementers
4. Rollback procedures if needed
```

#### Example
```
Create a version control strategy for these specification changes:

Current Version: 1.2.0
Proposed Changes: Add multi-tenant architecture, update API endpoints

Change Categories:
- Major changes: Multi-tenant data model
- Minor changes: New API endpoints for tenant management
- Patch changes: Clarification of existing requirements

Versioning Requirements:
1. Semantic Versioning
   - Major: Breaking changes to requirements
   - Minor: New features or capabilities
   - Patch: Bug fixes or clarifications

2. Change Documentation
   - What changed and why
   - Impact on implementation
   - Migration guidance

3. Rollback Strategy
   - How to revert changes
   - Compatibility considerations
   - Risk mitigation

Please provide:
1. Appropriate version number for changes
2. Detailed change log entry
3. Migration guide for implementers
4. Rollback procedures if needed
```

## Automation Prompts

### Automated Specification Updates

#### Template
```
Create automated updates for specifications based on code changes:

Code Changes:
```
{code_diff}
```

Affected Files:
- Source files: {source_files}
- Test files: {test_files}
- Configuration files: {config_files}

Specification Files to Update:
- requirements.md: [identify relevant sections]
- design.md: [identify relevant sections]
- tasks.md: [identify relevant sections]

Automated Update Rules:
1. API Changes → Update design.md API section
2. New Features → Update requirements.md
3. Architecture Changes → Update design.md architecture
4. Completed Tasks → Update tasks.md progress

Please generate:
1. Specific specification updates needed
2. Automated update scripts/rules
3. Validation checks for updates
4. Rollback procedures for incorrect updates
```

#### Example
```
Create automated updates for specifications based on code changes:

Code Changes:
```diff
+ const authenticateWithMFA = async (req, res, next) => {
+   // Multi-factor authentication logic
+ };
+ 
+ router.post('/auth/mfa', authenticateWithMFA);
```

Affected Files:
- Source files: auth.js, routes.js
- Test files: auth.test.js
- Configuration files: none

Specification Files to Update:
- requirements.md: REQ-001 authentication section
- design.md: Authentication architecture section
- tasks.md: TASK-003 MFA implementation

Automated Update Rules:
1. API Changes → Update design.md API section
2. New Features → Update requirements.md
3. Architecture Changes → Update design.md architecture
4. Completed Tasks → Update tasks.md progress

Please generate:
1. Specific specification updates needed
2. Automated update scripts/rules
3. Validation checks for updates
4. Rollback procedures for incorrect updates
```

### Specification Monitoring

#### Template
```
Set up monitoring for specification quality and currency:

Monitoring Areas:
1. Specification Currency
   - Last update timestamps
   - Code-to-spec alignment
   - Outdated sections

2. Quality Metrics
   - Completeness scores
   - Clarity ratings
   - Consistency checks

3. Usage Metrics
   - How often specs are referenced
   - Which sections are most used
   - Team feedback on usefulness

4. Automated Checks
   - Link validation
   - Format compliance
   - Content consistency

Please create:
1. Monitoring dashboard specification
2. Automated check scripts
3. Alert criteria and thresholds
4. Reporting and improvement processes
```

#### Example
```
Set up monitoring for specification quality and currency:

Monitoring Areas:
1. Specification Currency
   - Last update timestamps
   - Code-to-spec alignment
   - Outdated sections

2. Quality Metrics
   - Completeness scores
   - Clarity ratings
   - Consistency checks

3. Usage Metrics
   - How often specs are referenced
   - Which sections are most used
   - Team feedback on usefulness

4. Automated Checks
   - Link validation
   - Format compliance
   - Content consistency

Please create:
1. Monitoring dashboard specification
2. Automated check scripts
3. Alert criteria and thresholds
4. Reporting and improvement processes
```

## Maintenance Best Practices

### Regular Maintenance Schedule

#### Template
```
Create a maintenance schedule for Prizm specifications:

Project Context:
- Team size: {team_size}
- Project complexity: {complexity_level}
- Development velocity: {velocity}

Maintenance Activities:
1. Daily Maintenance
   - Update task progress
   - Note blockers and issues
   - Quick consistency checks

2. Weekly Maintenance
   - Review completed features
   - Update design decisions
   - Sync with implementation

3. Monthly Maintenance
   - Comprehensive health check
   - Gap analysis
   - Quality improvements

4. Quarterly Maintenance
   - Major version updates
   - Process improvements
   - Tool and template updates

Please create:
1. Detailed maintenance schedule
2. Responsibility assignments
3. Maintenance checklists
4. Quality metrics and goals
```

#### Example
```
Create a maintenance schedule for Prizm specifications:

Project Context:
- Team size: 8 developers
- Project complexity: High (SaaS platform)
- Development velocity: 2-week sprints

Maintenance Activities:
1. Daily Maintenance
   - Update task progress
   - Note blockers and issues
   - Quick consistency checks

2. Weekly Maintenance
   - Review completed features
   - Update design decisions
   - Sync with implementation

3. Monthly Maintenance
   - Comprehensive health check
   - Gap analysis
   - Quality improvements

4. Quarterly Maintenance
   - Major version updates
   - Process improvements
   - Tool and template updates

Please create:
1. Detailed maintenance schedule
2. Responsibility assignments
3. Maintenance checklists
4. Quality metrics and goals
```

### Team Training for Maintenance

#### Template
```
Create training materials for specification maintenance:

Training Audience:
- Role: {team_roles}
- Experience level: {experience_level}
- Training goals: {training_goals}

Training Topics:
1. Specification Structure
   - Core Trinity understanding
   - Extended documents usage
   - Relationships and dependencies

2. Maintenance Procedures
   - Update processes
   - Quality standards
   - Consistency checking

3. Tool Usage
   - AI assistant integration
   - Automated checks
   - Version control

4. Best Practices
   - Writing effective specifications
   - Maintaining currency
   - Team collaboration

Please create:
1. Training curriculum outline
2. Practical exercises
3. Assessment criteria
4. Ongoing support resources
```

#### Example
```
Create training materials for specification maintenance:

Training Audience:
- Team members with varying experience levels
- Training goals: Effective spec maintenance

Training Topics:
1. Specification Structure
   - Core Trinity understanding
   - Extended documents usage
   - Relationships and dependencies

2. Maintenance Procedures
   - Update processes
   - Quality standards
   - Consistency checking

3. Tool Usage
   - AI assistant integration
   - Automated checks
   - Version control

4. Best Practices
   - Writing effective specifications
   - Maintaining currency
   - Team collaboration

Please create:
1. Training curriculum outline
2. Practical exercises
3. Assessment criteria
4. Ongoing support resources
```

## Maintenance Success Metrics

### Measuring Specification Health

1. **Currency Metrics**
   - Time since last update
   - Code-to-spec alignment percentage
   - Outdated section count

2. **Quality Metrics**
   - Completeness score
   - Clarity rating
   - Consistency index

3. **Usage Metrics**
   - Reference frequency
   - Team satisfaction
   - Implementation accuracy

4. **Maintenance Metrics**
   - Update frequency
   - Maintenance time
   - Issue resolution time

### Continuous Improvement

1. **Regular Assessment**
   - Monthly specification reviews
   - Quarterly process improvements
   - Annual methodology updates

2. **Feedback Integration**
   - Team feedback collection
   - Stakeholder input
   - User experience improvements

3. **Process Optimization**
   - Automation opportunities
   - Tool improvements
   - Workflow enhancements

4. **Knowledge Management**
   - Best practice documentation
   - Lesson learned capture
   - Training material updates

---

**Use these maintenance prompts to keep your Prizm specifications current, accurate, and valuable. Regular maintenance ensures your documentation remains a trusted source of truth for your development team.**