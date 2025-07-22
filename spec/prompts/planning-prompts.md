# Planning Prompts

This collection of prompts helps you plan projects effectively using Prizm specifications with AI assistants. These prompts support everything from initial project setup to ongoing sprint planning and resource allocation.

## Prizm Template Planning Workflow

### Project Initialization Planning
```
Help me plan and initialize a new project using Prizm templates:

Project Overview:
- Name: {project_name}
- Type: {project_description}
- Team: {team_composition}
- Timeline: {timeline}

Steps:
1. Analyze project needs and recommend appropriate template (simple-cli, web-api, saas-app, microservices)
2. Copy template: cp -r spec/examples/{recommended_type}/* {project_name}/
3. Review template specifications and identify customizations needed
4. Plan team onboarding and document ownership strategy
5. Set up specification review and maintenance workflows

Please provide detailed recommendations for each step.
```

### Sprint Planning with CLI Tools
```
Help me plan our upcoming sprint using Prizm specifications:

Current Status:
1. Review specifications for completeness and consistency
2. Assess current implementation against specifications

Sprint Planning Goals:
- Sprint duration: {duration}
- Team capacity: {capacity}
- Priority features: {features}

Based on the validation and report output, help me:
1. Identify what specifications need updates before sprint start
2. Break down features into implementable tasks
3. Estimate effort based on specification completeness
4. Plan specification maintenance during sprint
5. Set up reporting for sprint tracking

Please provide a complete sprint plan with CLI integration.
```

### Project Health Check
```
Let's assess our project health using Prizm specifications:

1. Review all specification files for currency and accuracy
2. Compare specifications against current implementation
3. Analyze the specifications for:
   - Specification completeness
   - Task progress
   - Documentation quality
   - Team productivity indicators

Based on the analysis, recommend:
- Immediate actions needed
- Process improvements
- Tool optimization opportunities
- Team development needs

Provide a comprehensive health assessment with action items.
```

## Project Initiation Prompts

### Initial Project Assessment

#### Template
```
Help me assess this project and choose the appropriate Prizm complexity level:

Project Details:
- Project type: {project_type}
- Team size: {team_size} 
- Timeline: {timeline}
- Technology stack: {technology_stack}
- Complexity factors: {complexity_factors}

Business Context:
- Industry: {industry}
- Target users: {target_users}
- Success criteria: {success_criteria}
- Constraints: {constraints}

Please recommend:
1. Appropriate Prizm example to start with (simple-cli, web-api, saas-app, microservices)
2. Which extended documents will be needed
3. Suggested team structure and roles
4. High-level timeline and phases
5. Key risks and mitigation strategies

Provide a detailed assessment with rationale for recommendations.
```

#### Example
```
Help me assess this project and choose the appropriate Prizm complexity level:

Project Details:
- Project type: E-commerce mobile app with backend API
- Team size: 6 developers (2 mobile, 3 backend, 1 full-stack)
- Timeline: 16 weeks
- Technology stack: React Native, Node.js, PostgreSQL, AWS
- Complexity factors: Payment processing, inventory management, user reviews

Business Context:
- Industry: Retail/E-commerce
- Target users: 10,000+ customers, 100+ merchants
- Success criteria: 95% uptime, <2s load times, $1M revenue in first year
- Constraints: PCI compliance, mobile-first design, budget $500K

Please recommend:
1. Appropriate Prizm example to start with (simple-cli, web-api, saas-app, microservices)
2. Which extended documents will be needed
3. Suggested team structure and roles
4. High-level timeline and phases
5. Key risks and mitigation strategies

Provide a detailed assessment with rationale for recommendations.
```

### Requirements Discovery

#### Template
```
Help me conduct requirements discovery for this project:

Project Vision:
- Goal: {project_goal}
- Problem solving: {problem_being_solved}
- Value proposition: {value_proposition}

Stakeholder Information:
- Primary stakeholders: {primary_stakeholders}
- Users: {user_types}
- Decision makers: {decision_makers}

Discovery Areas:
1. Functional Requirements
   - Core features needed
   - User workflows
   - System capabilities

2. Non-Functional Requirements
   - Performance expectations
   - Security requirements
   - Scalability needs

3. Business Requirements
   - Success metrics
   - Compliance needs
   - Integration requirements

4. Technical Requirements
   - Platform constraints
   - Technology preferences
   - Integration needs

Please generate:
1. Comprehensive requirements discovery questions
2. User story templates for this domain
3. Acceptance criteria framework
4. Requirements prioritization matrix
```

#### Example
```
Help me conduct requirements discovery for this project:

Project Vision:
- Goal: Build a project management tool for remote teams
- Problem solving: Lack of real-time collaboration and visibility
- Value proposition: Improve team productivity by 40%

Stakeholder Information:
- Primary stakeholders: Product manager, engineering lead, sales team
- Users: Project managers, developers, designers, executives
- Decision makers: CTO, Head of Product

Discovery Areas:
1. Functional Requirements
   - Core features needed
   - User workflows
   - System capabilities

2. Non-Functional Requirements
   - Performance expectations
   - Security requirements
   - Scalability needs

3. Business Requirements
   - Success metrics
   - Compliance needs
   - Integration requirements

4. Technical Requirements
   - Platform constraints
   - Technology preferences
   - Integration needs

Please generate:
1. Comprehensive requirements discovery questions
2. User story templates for this domain
3. Acceptance criteria framework
4. Requirements prioritization matrix
```

### Architecture Planning

#### Template
```
Help me plan the technical architecture for this project:

Project Requirements:
- Functional requirements: {functional_requirements}
- Non-functional requirements: {non_functional_requirements}
- Scale requirements: {scale_requirements}
- Integration requirements: {integration_requirements}

Technical Context:
- Team expertise: {team_expertise}
- Technology constraints: {technology_constraints}
- Infrastructure preferences: {infrastructure_preferences}
- Budget constraints: {budget_constraints}

Architecture Decisions Needed:
1. System Architecture
   - Monolithic vs. microservices
   - Client-server vs. serverless
   - Database architecture

2. Technology Stack
   - Frontend framework
   - Backend framework
   - Database choices
   - Infrastructure platform

3. Integration Strategy
   - API design approach
   - Message passing patterns
   - External service integration

4. Security Architecture
   - Authentication strategy
   - Authorization patterns
   - Data protection approach

Please provide:
1. Recommended architecture with rationale
2. Technology stack recommendations
3. Integration and deployment strategy
4. Security implementation plan
5. Scalability and performance considerations
```

#### Example
```
Help me plan the technical architecture for this project:

Project Requirements:
- Functional requirements: User management, project tracking, real-time collaboration
- Non-functional requirements: 99.9% uptime, <200ms response time, 10,000 concurrent users
- Scale requirements: Support 100,000+ users, global deployment
- Integration requirements: Slack, GitHub, Google Workspace, Jira

Technical Context:
- Team expertise: JavaScript/TypeScript, React, Node.js, PostgreSQL
- Technology constraints: Must use AWS, prefer open-source tools
- Infrastructure preferences: Container-based deployment, CI/CD pipeline
- Budget constraints: $50K/month infrastructure budget

Architecture Decisions Needed:
1. System Architecture
   - Monolithic vs. microservices
   - Client-server vs. serverless
   - Database architecture

2. Technology Stack
   - Frontend framework
   - Backend framework
   - Database choices
   - Infrastructure platform

3. Integration Strategy
   - API design approach
   - Message passing patterns
   - External service integration

4. Security Architecture
   - Authentication strategy
   - Authorization patterns
   - Data protection approach

Please provide:
1. Recommended architecture with rationale
2. Technology stack recommendations
3. Integration and deployment strategy
4. Security implementation plan
5. Scalability and performance considerations
```

## Sprint Planning Prompts

### Sprint Goal Setting

#### Template
```
Help me set sprint goals based on current project specifications:

Current Sprint: Sprint {sprint_number}
Sprint Duration: {sprint_duration}
Team Capacity: {team_capacity}

Project Context:
- Current requirements: [paste relevant requirements]
- Current design: [paste relevant design sections]
- Current tasks: [paste current task status]

Sprint Planning Context:
- Previous sprint outcomes: {previous_sprint_outcomes}
- Current blockers: {current_blockers}
- Stakeholder priorities: {stakeholder_priorities}
- Technical dependencies: {technical_dependencies}

Please help me:
1. Define clear sprint goals aligned with project objectives
2. Select appropriate requirements/tasks for this sprint
3. Break down large tasks into sprint-sized work items
4. Identify potential risks and mitigation strategies
5. Create sprint success criteria and metrics

Provide a comprehensive sprint plan with clear objectives and measurable outcomes.
```

#### Example
```
Help me set sprint goals based on current project specifications:

Current Sprint: Sprint 3
Sprint Duration: 2 weeks
Team Capacity: 80 story points (4 developers)

Project Context:
- Current requirements: REQ-001 (Authentication) completed, REQ-002 (User management) in progress
- Current design: Basic API architecture implemented, database schema defined
- Current tasks: User registration complete, profile management 50% done

Sprint Planning Context:
- Previous sprint outcomes: Authentication system completed ahead of schedule
- Current blockers: Email service integration pending vendor approval
- Stakeholder priorities: User onboarding flow is critical for demo
- Technical dependencies: Profile management depends on file upload service

Please help me:
1. Define clear sprint goals aligned with project objectives
2. Select appropriate requirements/tasks for this sprint
3. Break down large tasks into sprint-sized work items
4. Identify potential risks and mitigation strategies
5. Create sprint success criteria and metrics

Provide a comprehensive sprint plan with clear objectives and measurable outcomes.
```

### Backlog Prioritization

#### Template
```
Help me prioritize the product backlog based on project specifications:

Current Backlog Items:
- {backlog_item_1}: {description_and_effort}
- {backlog_item_2}: {description_and_effort}
- {backlog_item_3}: {description_and_effort}

Prioritization Factors:
1. Business Value
   - Revenue impact: {revenue_impact}
   - User satisfaction: {user_satisfaction}
   - Strategic importance: {strategic_importance}

2. Technical Factors
   - Implementation complexity: {complexity}
   - Technical dependencies: {dependencies}
   - Risk level: {risk_level}

3. External Factors
   - Market timing: {market_timing}
   - Compliance requirements: {compliance}
   - Resource availability: {resource_availability}

Please provide:
1. Prioritized backlog with rationale
2. Recommended sprint assignments
3. Dependency mapping and sequencing
4. Risk assessment for high-priority items
5. Alternative prioritization scenarios
```

#### Example
```
Help me prioritize the product backlog based on project specifications:

Current Backlog Items:
- User authentication: Core login/logout functionality (13 points)
- Payment processing: Stripe integration for subscriptions (21 points)
- Real-time notifications: WebSocket-based updates (8 points)
- Admin dashboard: User management interface (13 points)
- Mobile app: React Native implementation (34 points)

Prioritization Factors:
1. Business Value
   - Revenue impact: Payment processing (high), others (medium)
   - User satisfaction: Authentication (high), notifications (medium)
   - Strategic importance: Mobile app (high), admin dashboard (low)

2. Technical Factors
   - Implementation complexity: Mobile app (high), payment (medium)
   - Technical dependencies: Payment depends on auth, notifications depend on auth
   - Risk level: Payment (high), mobile (medium)

3. External Factors
   - Market timing: Mobile app needed for Q2 launch
   - Compliance requirements: Payment processing has PCI requirements
   - Resource availability: Mobile developer available next sprint

Please provide:
1. Prioritized backlog with rationale
2. Recommended sprint assignments
3. Dependency mapping and sequencing
4. Risk assessment for high-priority items
5. Alternative prioritization scenarios
```

### Capacity Planning

#### Template
```
Help me plan team capacity for the upcoming development period:

Team Composition:
- {role_1}: {number} people, {skill_level}, {availability}
- {role_2}: {number} people, {skill_level}, {availability}
- {role_3}: {number} people, {skill_level}, {availability}

Work Requirements:
- Requirements to implement: [paste relevant requirements]
- Tasks to complete: [paste relevant tasks]
- Technical debt to address: {technical_debt}

Planning Context:
- Planning period: {planning_period}
- Known commitments: {known_commitments}
- Team velocity: {team_velocity}
- Uncertainty factors: {uncertainty_factors}

Please help me:
1. Calculate realistic team capacity
2. Allocate work based on skills and availability
3. Identify capacity constraints and bottlenecks
4. Plan for contingencies and risks
5. Optimize team utilization

Provide a detailed capacity plan with work allocation and timeline.
```

#### Example
```
Help me plan team capacity for the upcoming development period:

Team Composition:
- Senior Backend Developer: 2 people, expert level, 90% availability
- Frontend Developer: 2 people, intermediate level, 85% availability
- DevOps Engineer: 1 person, expert level, 60% availability
- QA Engineer: 1 person, intermediate level, 100% availability

Work Requirements:
- Requirements to implement: REQ-003 (API endpoints), REQ-004 (UI components)
- Tasks to complete: Authentication system, user dashboard, deployment pipeline
- Technical debt to address: Test coverage improvement, code refactoring

Planning Context:
- Planning period: 6 weeks (3 sprints)
- Known commitments: 1 developer on vacation week 4-5
- Team velocity: 45 story points per sprint
- Uncertainty factors: New technology learning curve, external API dependencies

Please help me:
1. Calculate realistic team capacity
2. Allocate work based on skills and availability
3. Identify capacity constraints and bottlenecks
4. Plan for contingencies and risks
5. Optimize team utilization

Provide a detailed capacity plan with work allocation and timeline.
```

## Resource Planning Prompts

### Team Structure Planning

#### Template
```
Help me plan the optimal team structure for this project:

Project Characteristics:
- Project type: {project_type}
- Duration: {project_duration}
- Complexity: {complexity_level}
- Technology stack: {technology_stack}

Requirements Analysis:
- Total requirements: {total_requirements}
- Complexity breakdown: {complexity_breakdown}
- Skill requirements: {skill_requirements}
- Timeline constraints: {timeline_constraints}

Current Resources:
- Available team members: {available_team_members}
- Skill levels: {skill_levels}
- Availability: {availability}
- Budget constraints: {budget_constraints}

Please recommend:
1. Optimal team composition (roles and quantities)
2. Skill development needs for current team
3. External hiring or contracting needs
4. Team organization and reporting structure
5. Communication and collaboration patterns

Provide a detailed team structure plan with justification.
```

#### Example
```
Help me plan the optimal team structure for this project:

Project Characteristics:
- Project type: SaaS project management platform
- Duration: 6 months
- Complexity: High (multi-tenant, real-time, integrations)
- Technology stack: React, Node.js, PostgreSQL, AWS

Requirements Analysis:
- Total requirements: 15 major features
- Complexity breakdown: 5 high, 7 medium, 3 low complexity
- Skill requirements: Full-stack development, DevOps, security, UX
- Timeline constraints: MVP in 3 months, full launch in 6 months

Current Resources:
- Available team members: 3 developers, 1 designer
- Skill levels: 1 senior, 2 junior developers
- Availability: 100% for 4 months, then 75%
- Budget constraints: $800K total budget

Please recommend:
1. Optimal team composition (roles and quantities)
2. Skill development needs for current team
3. External hiring or contracting needs
4. Team organization and reporting structure
5. Communication and collaboration patterns

Provide a detailed team structure plan with justification.
```

### Technology Planning

#### Template
```
Help me plan the technology adoption strategy for this project:

Current Technology Context:
- Team expertise: {team_expertise}
- Existing systems: {existing_systems}
- Infrastructure: {current_infrastructure}
- Constraints: {technology_constraints}

Project Requirements:
- Technical requirements: {technical_requirements}
- Performance requirements: {performance_requirements}
- Security requirements: {security_requirements}
- Integration requirements: {integration_requirements}

Technology Decisions Needed:
1. Core Technology Stack
   - Programming languages
   - Frameworks and libraries
   - Database systems
   - Infrastructure platforms

2. Development Tools
   - IDE and development environment
   - Version control and CI/CD
   - Testing frameworks
   - Monitoring and logging

3. Adoption Strategy
   - Learning curve considerations
   - Migration planning
   - Risk mitigation
   - Timeline integration

Please provide:
1. Recommended technology stack with rationale
2. Adoption timeline and learning plan
3. Risk assessment and mitigation strategies
4. Integration with existing systems
5. Future scalability considerations
```

#### Example
```
Help me plan the technology adoption strategy for this project:

Current Technology Context:
- Team expertise: Java, Spring Boot, MySQL, basic React knowledge
- Existing systems: Legacy Java monolith, MySQL database
- Infrastructure: On-premise servers, basic CI/CD
- Constraints: Must maintain existing system during transition

Project Requirements:
- Technical requirements: Modern web app, API-first architecture
- Performance requirements: <200ms response time, 10K concurrent users
- Security requirements: OAuth2, data encryption, audit logging
- Integration requirements: Legacy system integration, third-party APIs

Technology Decisions Needed:
1. Core Technology Stack
   - Programming languages
   - Frameworks and libraries
   - Database systems
   - Infrastructure platforms

2. Development Tools
   - IDE and development environment
   - Version control and CI/CD
   - Testing frameworks
   - Monitoring and logging

3. Adoption Strategy
   - Learning curve considerations
   - Migration planning
   - Risk mitigation
   - Timeline integration

Please provide:
1. Recommended technology stack with rationale
2. Adoption timeline and learning plan
3. Risk assessment and mitigation strategies
4. Integration with existing systems
5. Future scalability considerations
```

## Risk Assessment Prompts

### Project Risk Analysis

#### Template
```
Conduct a comprehensive risk analysis for this project:

Project Context:
- Project scope: {project_scope}
- Timeline: {project_timeline}
- Team: {team_composition}
- Budget: {project_budget}

Current Specifications:
- Requirements: [paste key requirements]
- Design: [paste architecture overview]
- Tasks: [paste critical tasks]

Risk Categories:
1. Technical Risks
   - Technology complexity
   - Integration challenges
   - Performance risks
   - Security vulnerabilities

2. Project Risks
   - Timeline risks
   - Resource risks
   - Scope creep
   - Quality risks

3. Business Risks
   - Market changes
   - Stakeholder changes
   - Competition
   - Regulatory changes

4. Team Risks
   - Key person dependencies
   - Skill gaps
   - Communication issues
   - Morale and motivation

Please provide:
1. Comprehensive risk register with probability and impact
2. Risk mitigation strategies for each identified risk
3. Contingency plans for high-impact risks
4. Risk monitoring and early warning indicators
5. Risk management timeline and responsibilities
```

#### Example
```
Conduct a comprehensive risk analysis for this project:

Project Context:
- Project scope: Multi-tenant SaaS platform with real-time features
- Timeline: 6 months to MVP, 12 months to full launch
- Team: 8 developers, 2 designers, 1 PM
- Budget: $1.2M total budget

Current Specifications:
- Requirements: 12 major features including real-time collaboration
- Design: Microservices architecture with event sourcing
- Tasks: 156 tasks across 8 phases

Risk Categories:
1. Technical Risks
   - Technology complexity
   - Integration challenges
   - Performance risks
   - Security vulnerabilities

2. Project Risks
   - Timeline risks
   - Resource risks
   - Scope creep
   - Quality risks

3. Business Risks
   - Market changes
   - Stakeholder changes
   - Competition
   - Regulatory changes

4. Team Risks
   - Key person dependencies
   - Skill gaps
   - Communication issues
   - Morale and motivation

Please provide:
1. Comprehensive risk register with probability and impact
2. Risk mitigation strategies for each identified risk
3. Contingency plans for high-impact risks
4. Risk monitoring and early warning indicators
5. Risk management timeline and responsibilities
```

### Timeline Risk Assessment

#### Template
```
Assess timeline risks for this project schedule:

Project Schedule:
- Total duration: {total_duration}
- Major milestones: {major_milestones}
- Critical path: {critical_path}
- Buffer time: {buffer_time}

Task Dependencies:
- Critical dependencies: {critical_dependencies}
- External dependencies: {external_dependencies}
- Resource dependencies: {resource_dependencies}

Risk Factors:
1. Estimation Accuracy
   - Historical accuracy: {historical_accuracy}
   - Complexity factors: {complexity_factors}
   - Unknown requirements: {unknown_requirements}

2. Resource Availability
   - Team availability: {team_availability}
   - Skill availability: {skill_availability}
   - External resource dependencies: {external_resources}

3. External Factors
   - Stakeholder availability: {stakeholder_availability}
   - Third-party dependencies: {third_party_dependencies}
   - Regulatory approvals: {regulatory_approvals}

Please provide:
1. Timeline risk assessment with impact analysis
2. Critical path analysis and bottleneck identification
3. Schedule optimization recommendations
4. Contingency planning for timeline risks
5. Monitoring and early warning systems
```

#### Example
```
Assess timeline risks for this project schedule:

Project Schedule:
- Total duration: 24 weeks
- Major milestones: MVP (12 weeks), Beta (18 weeks), Launch (24 weeks)
- Critical path: Authentication → User Management → Core Features → Integration
- Buffer time: 2 weeks total (8% buffer)

Task Dependencies:
- Critical dependencies: Authentication blocks all other features
- External dependencies: Payment processor approval, security audit
- Resource dependencies: Senior developer needed for architecture decisions

Risk Factors:
1. Estimation Accuracy
   - Historical accuracy: 85% (tend to underestimate by 15%)
   - Complexity factors: New technology, complex integrations
   - Unknown requirements: 20% of features not fully defined

2. Resource Availability
   - Team availability: 1 developer on vacation weeks 10-12
   - Skill availability: Only 1 senior developer for complex tasks
   - External resource dependencies: Security consultant, UX designer

3. External Factors
   - Stakeholder availability: Product owner travels frequently
   - Third-party dependencies: Payment processor, email service
   - Regulatory approvals: Security audit required for launch

Please provide:
1. Timeline risk assessment with impact analysis
2. Critical path analysis and bottleneck identification
3. Schedule optimization recommendations
4. Contingency planning for timeline risks
5. Monitoring and early warning systems
```

## Milestone Planning Prompts

### Release Planning

#### Template
```
Help me plan releases for this project:

Product Vision:
- End goal: {end_goal}
- Success metrics: {success_metrics}
- User segments: {user_segments}
- Market constraints: {market_constraints}

Current Specifications:
- Requirements: [paste prioritized requirements]
- Technical architecture: [paste architecture overview]
- Development timeline: [paste timeline]

Release Strategy Context:
- Release frequency: {release_frequency}
- Deployment approach: {deployment_approach}
- User feedback integration: {feedback_integration}
- Quality standards: {quality_standards}

Please help me:
1. Define release goals and success criteria
2. Group features into logical release increments
3. Plan release timeline with dependencies
4. Design user feedback and validation approach
5. Create rollback and risk mitigation strategies

Provide a comprehensive release plan with clear milestones and deliverables.
```

#### Example
```
Help me plan releases for this project:

Product Vision:
- End goal: Comprehensive project management SaaS platform
- Success metrics: 10K users, 95% satisfaction, $1M ARR
- User segments: Small teams, enterprise teams, freelancers
- Market constraints: Competitive market, need fast time-to-market

Current Specifications:
- Requirements: 15 major features from basic to advanced
- Technical architecture: Microservices with React frontend
- Development timeline: 24 weeks total development

Release Strategy Context:
- Release frequency: Monthly releases after MVP
- Deployment approach: Blue-green deployment with feature flags
- User feedback integration: Beta testing, user interviews, analytics
- Quality standards: 95% uptime, <200ms response time

Please help me:
1. Define release goals and success criteria
2. Group features into logical release increments
3. Plan release timeline with dependencies
4. Design user feedback and validation approach
5. Create rollback and risk mitigation strategies

Provide a comprehensive release plan with clear milestones and deliverables.
```

### Feature Roadmap Planning

#### Template
```
Create a feature roadmap based on project specifications:

Product Strategy:
- Vision: {product_vision}
- Target market: {target_market}
- Competitive advantages: {competitive_advantages}
- Success metrics: {success_metrics}

Current Requirements:
- Core features: [paste core requirements]
- Advanced features: [paste advanced requirements]
- Integration features: [paste integration requirements]

Roadmap Context:
- Planning horizon: {planning_horizon}
- Resource constraints: {resource_constraints}
- Market timing: {market_timing}
- Technical dependencies: {technical_dependencies}

Please create:
1. Feature prioritization with business value assessment
2. Roadmap timeline with major milestones
3. Resource allocation across features
4. Risk assessment for roadmap execution
5. Flexibility points for market changes

Provide a detailed feature roadmap with rationale and success metrics.
```

#### Example
```
Create a feature roadmap based on project specifications:

Product Strategy:
- Vision: Become the leading project management tool for remote teams
- Target market: 10-100 person companies with remote/hybrid teams
- Competitive advantages: Real-time collaboration, AI-powered insights
- Success metrics: 50K users, 4.5+ rating, 85% retention

Current Requirements:
- Core features: Project management, task tracking, team collaboration
- Advanced features: Time tracking, reporting, integrations
- Integration features: Slack, GitHub, Google Workspace, Jira

Roadmap Context:
- Planning horizon: 18 months
- Resource constraints: 8 developers, $2M budget
- Market timing: Need MVP in 6 months for funding round
- Technical dependencies: Real-time infrastructure, AI/ML capabilities

Please create:
1. Feature prioritization with business value assessment
2. Roadmap timeline with major milestones
3. Resource allocation across features
4. Risk assessment for roadmap execution
5. Flexibility points for market changes

Provide a detailed feature roadmap with rationale and success metrics.
```

## Continuous Planning Prompts

### Retrospective Planning

#### Template
```
Help me conduct a retrospective and plan improvements:

Sprint/Project Context:
- Period: {time_period}
- Team: {team_composition}
- Goals: {original_goals}
- Outcomes: {actual_outcomes}

Retrospective Data:
- Completed work: {completed_work}
- Blocked or delayed work: {blocked_work}
- Quality metrics: {quality_metrics}
- Team feedback: {team_feedback}

Areas to Analyze:
1. Process Effectiveness
   - Planning accuracy
   - Execution efficiency
   - Communication quality
   - Tool effectiveness

2. Technical Outcomes
   - Code quality
   - Architecture decisions
   - Technical debt
   - Performance achievements

3. Team Performance
   - Productivity metrics
   - Collaboration effectiveness
   - Skill development
   - Morale and satisfaction

Please provide:
1. Comprehensive retrospective analysis
2. Specific improvement recommendations
3. Action items with owners and timelines
4. Process changes for next iteration
5. Success metrics for improvements
```

#### Example
```
Help me conduct a retrospective and plan improvements:

Sprint/Project Context:
- Period: Sprint 4 (2 weeks)
- Team: 5 developers, 1 designer, 1 PM
- Goals: Complete user authentication and basic project management
- Outcomes: Authentication completed, project management 70% done

Retrospective Data:
- Completed work: 32 story points (goal was 40)
- Blocked or delayed work: File upload feature blocked by AWS permissions
- Quality metrics: 85% test coverage, 2 production bugs
- Team feedback: Too many meetings, unclear requirements

Areas to Analyze:
1. Process Effectiveness
   - Planning accuracy
   - Execution efficiency
   - Communication quality
   - Tool effectiveness

2. Technical Outcomes
   - Code quality
   - Architecture decisions
   - Technical debt
   - Performance achievements

3. Team Performance
   - Productivity metrics
   - Collaboration effectiveness
   - Skill development
   - Morale and satisfaction

Please provide:
1. Comprehensive retrospective analysis
2. Specific improvement recommendations
3. Action items with owners and timelines
4. Process changes for next iteration
5. Success metrics for improvements
```

### Adaptive Planning

#### Template
```
Help me adapt project plans based on new information:

Current Plan:
- Timeline: {current_timeline}
- Scope: {current_scope}
- Resources: {current_resources}
- Assumptions: {current_assumptions}

New Information:
- Changed requirements: {changed_requirements}
- New constraints: {new_constraints}
- Resource changes: {resource_changes}
- Market changes: {market_changes}

Impact Analysis:
1. Scope Impact
   - Features affected
   - Priority changes
   - New requirements

2. Timeline Impact
   - Milestone shifts
   - Critical path changes
   - Dependency updates

3. Resource Impact
   - Team changes
   - Skill requirements
   - Budget implications

Please provide:
1. Comprehensive impact analysis
2. Revised project plan with options
3. Risk assessment for changes
4. Stakeholder communication plan
5. Change management strategy
```

#### Example
```
Help me adapt project plans based on new information:

Current Plan:
- Timeline: 20 weeks to MVP launch
- Scope: 12 core features for project management
- Resources: 6 developers, $600K budget
- Assumptions: Standard web app, basic integrations

New Information:
- Changed requirements: Must add mobile app for launch
- New constraints: GDPR compliance required for EU launch
- Resource changes: 1 senior developer leaving in 8 weeks
- Market changes: Competitor launched similar product

Impact Analysis:
1. Scope Impact
   - Features affected
   - Priority changes
   - New requirements

2. Timeline Impact
   - Milestone shifts
   - Critical path changes
   - Dependency updates

3. Resource Impact
   - Team changes
   - Skill requirements
   - Budget implications

Please provide:
1. Comprehensive impact analysis
2. Revised project plan with options
3. Risk assessment for changes
4. Stakeholder communication plan
5. Change management strategy
```

## Planning Best Practices

### Effective Planning Principles

1. **Specification-Driven Planning**
   - Always start with clear requirements
   - Ensure design supports requirements
   - Plan tasks based on specifications

2. **Iterative Planning**
   - Plan in short iterations
   - Adapt based on learnings
   - Validate assumptions early

3. **Risk-Aware Planning**
   - Identify risks early
   - Plan mitigation strategies
   - Build in contingencies

4. **Team-Centered Planning**
   - Involve team in planning
   - Consider team capabilities
   - Plan for team growth

### Planning Tools Integration

1. **Specification Tools**
   - Use Prizm specifications as planning foundation
   - Keep specifications updated during planning
   - Link plans to specific requirements

2. **Project Management Tools**
   - Sync plans with project management systems
   - Track progress against specifications
   - Maintain visibility across stakeholders

3. **AI Assistant Integration**
   - Use AI for planning analysis
   - Generate planning scenarios
   - Validate plans against specifications

### Common Planning Pitfalls

1. **Over-Planning**
   - Planning too far in advance
   - Too much detail too early
   - Ignoring uncertainty

2. **Under-Planning**
   - Insufficient detail for execution
   - Missing dependencies
   - Inadequate risk assessment

3. **Disconnected Planning**
   - Plans not based on specifications
   - Inconsistent across team
   - Not updated regularly

### Planning Success Metrics

1. **Plan Accuracy**
   - Estimate vs. actual variance
   - Timeline adherence
   - Scope completion rate

2. **Team Effectiveness**
   - Productivity metrics
   - Quality outcomes
   - Team satisfaction

3. **Business Outcomes**
   - Requirements satisfaction
   - Stakeholder satisfaction
   - Business value delivered

---

**Use these planning prompts to create effective, specification-driven project plans. Good planning with Prizm specifications ensures project success and team alignment.**