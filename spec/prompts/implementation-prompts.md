# Implementation Prompts

This collection of prompts helps you effectively implement features using Prizm specifications with AI assistants. These prompts are optimized for specification-driven development.

## Project Setup and Validation Prompts

### Initial Project Setup
```
Help me set up a new project using Prizm. The project is a {project_type} with these characteristics:
- {project_description}
- {team_size}
- {complexity_level}

First, let's copy the appropriate template:
cp -r spec/examples/{project_type}/* {project_name}/

Then help me customize the generated specifications based on the specific requirements above.
```

### Specification Validation
```
Let's review our project specifications and fix any issues:

1. Review all specification files for completeness and consistency
2. Help me identify and fix any issues found
3. Ensure all specifications are complete and consistent
4. Assess current project status from specifications

Please review the validation output and suggest improvements for specification quality.
```

### Pre-Implementation Check
```
Before implementing {feature_name}, let's ensure our specifications are ready:

1. Review all specifications for current status and consistency
2. Review requirements.md for {feature_name} completeness
3. Check design.md for architectural guidance
4. Verify tasks.md has proper breakdown for this feature
5. Assess current project state from specifications

Based on the validation and report output, is this feature ready for implementation? If not, what specifications need updates?
```

## Basic Implementation Prompts

### Feature Implementation from Requirements

#### Template
```
Based on {requirement_id} in requirements.md, implement {feature_description} following the patterns in {relevant_docs}.

Context:
- Requirement: [paste relevant requirement section]
- Design patterns: [reference design.md sections]
- Style guide: [reference style.md patterns]

Please provide:
1. Complete implementation with proper error handling
2. Unit tests following testing-strategy.md
3. Documentation updates if needed
4. Any design.md updates required
```

#### Example
```
Based on REQ-001 in requirements.md, implement the user authentication system following the patterns in design.md and security.md.

Context:
- Requirement: REQ-001 specifies JWT authentication with rate limiting
- Design patterns: Repository pattern from design.md section 3.2
- Style guide: Error handling patterns from style.md section 4.1
- Security: JWT implementation from security.md section 2.1

Please provide:
1. Complete authentication middleware implementation
2. Unit tests for all authentication functions
3. Integration tests for auth endpoints
4. Update design.md if architecture changes
```

### API Endpoint Implementation

#### Template
```
Based on the API specification in design.md, implement the {endpoint_name} endpoint.

Requirements:
- Endpoint: {method} {path}
- Input: {input_specification}
- Output: {output_specification}
- Error handling: Follow patterns in style.md
- Validation: Use patterns from style.md section on validation

Please include:
1. Route handler implementation
2. Request/response validation
3. Error handling with proper HTTP status codes
4. Unit and integration tests
5. API documentation updates
```

#### Example
```
Based on the API specification in design.md, implement the user registration endpoint.

Requirements:
- Endpoint: POST /api/users/register
- Input: { email: string, password: string, profile: UserProfile }
- Output: { user: UserPublic, token: string }
- Error handling: Follow patterns in style.md section 4
- Validation: Use Joi validation patterns from style.md section 5

Please include:
1. Route handler with proper middleware
2. Input validation with detailed error messages
3. Password hashing and user creation
4. JWT token generation
5. Comprehensive tests for all scenarios
```

### Database Integration

#### Template
```
Based on the database design in design.md, implement the {entity_name} data access layer.

Requirements:
- Entity: {entity_description}
- Schema: [reference schema from design.md]
- Repository pattern: Follow patterns in style.md
- Error handling: Database error patterns from style.md

Please provide:
1. Repository interface and implementation
2. Database model/schema validation
3. CRUD operations with proper error handling
4. Unit tests with mocked database
5. Integration tests with test database
```

#### Example
```
Based on the database design in design.md, implement the User data access layer.

Requirements:
- Entity: User with authentication and profile data
- Schema: Users table from design.md section 3.1
- Repository pattern: Follow UserRepository interface in style.md
- Error handling: Database error patterns from style.md section 4.3

Please provide:
1. UserRepository interface and PostgreSQL implementation
2. User model with validation
3. CRUD operations with connection pooling
4. Unit tests with mocked database calls
5. Integration tests with test database setup
```

## Advanced Implementation Prompts

### Microservices Implementation

#### Template
```
Based on the microservices architecture in architecture.md, implement the {service_name} service.

Architecture Context:
- Service boundaries: [reference architecture.md]
- Communication patterns: [reference integration patterns]
- Data consistency: [reference data management strategy]
- Error handling: [reference resilience patterns]

Requirements:
- Service: {service_description}
- API contract: [reference API specification]
- Dependencies: [list dependent services]
- Data storage: [reference data strategy]

Please provide:
1. Service implementation with proper structure
2. API endpoints following service contract
3. Inter-service communication logic
4. Error handling and circuit breaker patterns
5. Health checks and monitoring
6. Docker configuration
7. Unit and integration tests
```

#### Example
```
Based on the microservices architecture in architecture.md, implement the User Management service.

Architecture Context:
- Service boundaries: User domain from architecture.md section 2.1
- Communication patterns: REST + event streaming from section 3.2
- Data consistency: Per-service database from section 4.1
- Error handling: Circuit breaker patterns from section 5.3

Requirements:
- Service: User registration, authentication, profile management
- API contract: UserService interface from architecture.md
- Dependencies: Email service, audit service
- Data storage: PostgreSQL with user-specific schema

Please provide:
1. Express.js service with proper middleware
2. Authentication and profile management endpoints
3. Event publishing for user lifecycle events
4. Circuit breaker for external service calls
5. Health checks for database and dependencies
6. Dockerfile and docker-compose configuration
7. Comprehensive test suite
```

### Security Implementation

#### Template
```
Based on the security requirements in security.md, implement {security_feature}.

Security Context:
- Threat model: [reference threat analysis]
- Security controls: [reference control requirements]
- Compliance: [reference compliance requirements]
- Authentication: [reference auth patterns]

Requirements:
- Security feature: {feature_description}
- Implementation: [reference implementation guidelines]
- Testing: [reference security testing requirements]

Please provide:
1. Secure implementation following security.md
2. Input validation and sanitization
3. Proper error handling without information leakage
4. Security tests including edge cases
5. Documentation of security decisions
```

#### Example
```
Based on the security requirements in security.md, implement multi-factor authentication.

Security Context:
- Threat model: Account takeover prevention from security.md section 2.1
- Security controls: MFA requirement from section 3.2
- Compliance: SOC 2 MFA requirements from section 4.1
- Authentication: JWT + TOTP patterns from section 5.1

Requirements:
- Security feature: Time-based OTP (TOTP) second factor
- Implementation: QR code setup + validation from security.md
- Testing: Security test scenarios from security.md section 6

Please provide:
1. TOTP generation and validation logic
2. QR code generation for setup
3. Backup code generation and validation
4. Rate limiting for MFA attempts
5. Security tests for all attack vectors
6. User-friendly error messages
```

## Testing Implementation Prompts

### Test-Driven Development

#### Template
```
Based on {requirement_id} in requirements.md and the testing strategy in testing-strategy.md, implement {feature_name} using test-driven development.

Requirements:
- Feature: {feature_description}
- Acceptance criteria: [paste acceptance criteria]
- Testing approach: [reference testing strategy]

Please provide:
1. Comprehensive test cases covering all acceptance criteria
2. Test implementation using TDD approach
3. Feature implementation that passes all tests
4. Integration tests for external dependencies
5. Performance tests if specified in requirements
```

#### Example
```
Based on REQ-003 in requirements.md and the testing strategy in testing-strategy.md, implement password reset functionality using test-driven development.

Requirements:
- Feature: Password reset via email verification
- Acceptance criteria: Email sent, token validation, password update
- Testing approach: Unit tests 70%, integration 25%, E2E 5%

Please provide:
1. Unit tests for all password reset functions
2. Integration tests for email service interaction
3. E2E tests for complete password reset flow
4. Password reset implementation passing all tests
5. Performance tests for email delivery
```

### Error Handling Implementation

#### Template
```
Based on the error handling patterns in style.md, implement comprehensive error handling for {feature_name}.

Error Handling Context:
- Error types: [reference error hierarchy]
- Logging: [reference logging patterns]
- User messaging: [reference user-friendly errors]
- Monitoring: [reference error monitoring]

Requirements:
- Feature: {feature_description}
- Error scenarios: [list expected error conditions]
- Recovery: [reference recovery strategies]

Please provide:
1. Custom error classes following style.md patterns
2. Proper error handling in all functions
3. User-friendly error messages
4. Comprehensive error logging
5. Error monitoring and alerting
6. Tests for all error scenarios
```

#### Example
```
Based on the error handling patterns in style.md, implement comprehensive error handling for the payment processing feature.

Error Handling Context:
- Error types: ValidationError, PaymentError, NetworkError hierarchy
- Logging: Structured logging with correlation IDs
- User messaging: No sensitive data in user errors
- Monitoring: Error rate and type monitoring

Requirements:
- Feature: Credit card payment processing
- Error scenarios: Invalid card, insufficient funds, network issues
- Recovery: Retry logic and fallback mechanisms

Please provide:
1. PaymentError class hierarchy
2. Retry logic with exponential backoff
3. User-friendly payment error messages
4. Comprehensive error logging with context
5. Error monitoring dashboard updates
6. Tests for all payment error scenarios
```

## Integration Implementation Prompts

### External Service Integration

#### Template
```
Based on the integration patterns in integrations.md, implement integration with {service_name}.

Integration Context:
- Service: {service_description}
- Authentication: [reference auth method]
- Rate limiting: [reference rate limits]
- Error handling: [reference error patterns]
- Monitoring: [reference monitoring requirements]

Requirements:
- Integration: {integration_description}
- API contract: [reference API specification]
- Fallback: [reference fallback strategy]

Please provide:
1. Service client implementation
2. Authentication handling
3. Rate limiting and retry logic
4. Error handling and fallback
5. Health checks and monitoring
6. Integration tests with mocked service
```

#### Example
```
Based on the integration patterns in integrations.md, implement integration with the Stripe payment service.

Integration Context:
- Service: Stripe payment processing API
- Authentication: API key authentication
- Rate limiting: 100 requests per second
- Error handling: Circuit breaker pattern
- Monitoring: Success rate and latency tracking

Requirements:
- Integration: Process payments and handle webhooks
- API contract: Stripe API v2023-10-16
- Fallback: Queue for retry and manual processing

Please provide:
1. Stripe client with proper error handling
2. API key management and rotation
3. Rate limiting with request queuing
4. Circuit breaker for service protection
5. Webhook signature validation
6. Health checks for Stripe connectivity
7. Integration tests with Stripe test mode
```

### Database Migration

#### Template
```
Based on the database design in design.md, implement database migration for {migration_description}.

Migration Context:
- Current schema: [reference current state]
- Target schema: [reference target state]
- Data migration: [reference data transformation]
- Rollback: [reference rollback strategy]

Requirements:
- Migration: {migration_description}
- Data preservation: [reference data requirements]
- Downtime: [reference downtime constraints]

Please provide:
1. Forward migration script
2. Rollback migration script
3. Data transformation logic
4. Migration validation tests
5. Performance impact assessment
6. Deployment documentation
```

#### Example
```
Based on the database design in design.md, implement database migration for adding user preferences table.

Migration Context:
- Current schema: Users table with basic profile
- Target schema: Add user_preferences table with foreign key
- Data migration: Default preferences for existing users
- Rollback: Safe removal of preferences table

Requirements:
- Migration: Add user_preferences table with relationships
- Data preservation: Maintain all existing user data
- Downtime: Zero-downtime migration required

Please provide:
1. CREATE TABLE migration for user_preferences
2. Default data insertion for existing users
3. Foreign key constraint addition
4. Rollback script for safe removal
5. Migration tests with production data copy
6. Performance impact analysis
```

## AI Assistant Optimization

### Context Optimization

#### Template
```
You are an expert software developer working on a project that uses Prizm specifications. 

Project Context:
- Project: {project_name}
- Technology: {technology_stack}
- Team: {team_size} developers
- Timeline: {project_timeline}

Specification Context:
- Requirements: [paste relevant requirements]
- Design: [paste relevant design sections]
- Constraints: [paste relevant constraints]
- Style: [paste relevant style patterns]

Task: {specific_task_description}

Please provide a complete implementation that:
1. Follows all specification requirements
2. Adheres to design patterns
3. Respects project constraints
4. Implements proper error handling
5. Includes comprehensive tests
6. Updates relevant documentation
```

#### Example
```
You are an expert software developer working on a project that uses Prizm specifications.

Project Context:
- Project: TaskFlow - Project Management SaaS
- Technology: Node.js, TypeScript, PostgreSQL, React
- Team: 5 developers
- Timeline: 12-week development cycle

Specification Context:
- Requirements: REQ-001 User Authentication with JWT and MFA
- Design: Microservices with API Gateway pattern
- Constraints: 99.9% uptime, GDPR compliance, <200ms response time
- Style: Repository pattern, TypeScript strict mode, Jest testing

Task: Implement user authentication service with JWT and optional MFA

Please provide a complete implementation that:
1. Meets REQ-001 acceptance criteria
2. Follows microservices architecture
3. Maintains performance requirements
4. Implements GDPR-compliant data handling
5. Includes comprehensive test coverage
6. Updates API documentation
```

### Iterative Development

#### Template
```
I'm working on {feature_name} for a project using Prizm specifications. This is iteration {iteration_number}.

Previous Context:
- What we've built: {previous_implementation}
- What we learned: {learnings}
- What needs improvement: {improvements_needed}

Current Specifications:
- Requirements: [paste updated requirements]
- Design: [paste updated design]
- Constraints: [paste any new constraints]

Next Steps:
{specific_next_steps}

Please help me implement the next iteration, ensuring:
1. Continuity with previous work
2. Incorporation of learnings
3. Adherence to updated specifications
4. Improved quality and performance
```

#### Example
```
I'm working on the notification system for a project using Prizm specifications. This is iteration 2.

Previous Context:
- What we've built: Basic email notifications using SendGrid
- What we learned: Need rate limiting and better error handling
- What needs improvement: Add push notifications and retry logic

Current Specifications:
- Requirements: REQ-005 now includes push notifications and delivery guarantees
- Design: Updated to include message queue for reliability
- Constraints: Must handle 10,000 notifications per minute

Next Steps:
1. Add push notification support
2. Implement message queue with Redis
3. Add retry logic with exponential backoff
4. Improve error handling and monitoring

Please help me implement the next iteration, ensuring:
1. Existing email functionality remains intact
2. New push notification system follows same patterns
3. Message queue provides reliability guarantees
4. Performance meets updated requirements
```

## Best Practices for AI Implementation

### Prompt Structure

1. **Clear Context**: Always provide relevant specification context
2. **Specific Requirements**: Reference exact requirement or design sections
3. **Expected Output**: Clearly state what you want the AI to provide
4. **Quality Standards**: Reference relevant quality and style requirements
5. **Validation**: Ask for tests and validation of the implementation

### Context Management

1. **Focused Context**: Include only relevant specification sections
2. **Layered Information**: Provide context in order of importance
3. **Specific References**: Use exact section numbers and requirement IDs
4. **Updated Information**: Ensure specifications are current
5. **Iterative Refinement**: Build on previous AI interactions

### Quality Assurance

1. **Specification Validation**: Always check output against specifications
2. **Pattern Consistency**: Ensure AI follows established patterns
3. **Error Handling**: Verify comprehensive error handling
4. **Test Coverage**: Ensure adequate test coverage
5. **Documentation**: Update specifications with new implementations

### Common Pitfalls to Avoid

1. **Vague Prompts**: Avoid generic implementation requests
2. **Missing Context**: Don't assume AI knows project specifics
3. **Outdated Specs**: Ensure specifications are current
4. **No Validation**: Always validate AI output against specs
5. **Ignoring Constraints**: Don't forget to include project constraints

---

**Use these prompts as starting points and adapt them to your specific project needs. The key is providing clear context from your Prizm specifications to get high-quality, consistent implementations.**