# Review Prompts

This collection of prompts helps you effectively review code against Prizm specifications using AI assistants. These prompts ensure code quality, specification compliance, and consistent implementation patterns.

## Basic Review Prompts

### Requirements Compliance Review

#### Template
```
Review this code against {requirement_id} in requirements.md:

Code to Review:
```
{code_block}
```

Requirement Context:
- Requirement: [paste relevant requirement]
- Acceptance Criteria: [paste acceptance criteria]
- Business Rules: [paste business rules]
- Edge Cases: [paste edge cases]

Please check:
1. Does the code meet all acceptance criteria?
2. Are business rules properly implemented?
3. Are edge cases handled correctly?
4. Are there any missing requirements?
5. Are error scenarios properly addressed?

Provide specific feedback with code examples where improvements are needed.
```

#### Example
```
Review this authentication middleware against REQ-001 in requirements.md:

Code to Review:
```javascript
const authenticate = async (req, res, next) => {
  const token = req.header('Authorization');
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token' });
  }
};
```

Requirement Context:
- Requirement: REQ-001 User Authentication with JWT tokens
- Acceptance Criteria: Validate JWT tokens, handle invalid tokens, support token refresh
- Business Rules: Tokens expire after 24 hours, rate limiting after 3 failures
- Edge Cases: Malformed tokens, expired tokens, missing authorization header

Please check:
1. Does the code meet all acceptance criteria?
2. Are business rules properly implemented?
3. Are edge cases handled correctly?
4. Are there any missing requirements?
5. Are error scenarios properly addressed?

Provide specific feedback with code examples where improvements are needed.
```

### Design Pattern Compliance Review

#### Template
```
Review this code against the design patterns in design.md:

Code to Review:
```
{code_block}
```

Design Context:
- Architecture: [paste relevant architecture section]
- Patterns: [paste relevant patterns]
- Technology Stack: [paste technology decisions]
- Integration: [paste integration patterns]

Please evaluate:
1. Does the code follow the specified architecture?
2. Are design patterns correctly implemented?
3. Are technology choices consistent with design.md?
4. Are integration patterns properly followed?
5. Are there any architectural violations?

Provide specific recommendations for alignment with design specifications.
```

#### Example
```
Review this user service against the design patterns in design.md:

Code to Review:
```javascript
class UserService {
  constructor() {
    this.users = [];
  }
  
  async createUser(userData) {
    const user = { id: Date.now(), ...userData };
    this.users.push(user);
    return user;
  }
  
  async findUser(id) {
    return this.users.find(user => user.id === id);
  }
}
```

Design Context:
- Architecture: Layered architecture with service and repository layers
- Patterns: Repository pattern for data access, dependency injection
- Technology Stack: PostgreSQL database, connection pooling
- Integration: Database integration with proper error handling

Please evaluate:
1. Does the code follow the specified architecture?
2. Are design patterns correctly implemented?
3. Are technology choices consistent with design.md?
4. Are integration patterns properly followed?
5. Are there any architectural violations?

Provide specific recommendations for alignment with design specifications.
```

### Style Guide Compliance Review

#### Template
```
Review this code against the style guide in style.md:

Code to Review:
```
{code_block}
```

Style Context:
- Coding Standards: [paste relevant coding standards]
- Naming Conventions: [paste naming conventions]
- Error Handling: [paste error handling patterns]
- Testing Patterns: [paste testing requirements]

Please check:
1. Are coding standards followed correctly?
2. Do naming conventions match the style guide?
3. Is error handling implemented per style.md?
4. Are testing patterns properly applied?
5. Are there any style violations?

Provide specific suggestions for style guide compliance.
```

#### Example
```
Review this API controller against the style guide in style.md:

Code to Review:
```javascript
const createuser = async (req, res) => {
  try {
    const user = await UserService.create(req.body);
    res.json(user);
  } catch (err) {
    res.status(500).json({ error: 'Error' });
  }
};
```

Style Context:
- Coding Standards: camelCase naming, descriptive variable names
- Naming Conventions: Functions use verbs, clear parameter names
- Error Handling: Specific error types, proper HTTP status codes
- Testing Patterns: Unit tests for all functions, integration tests for endpoints

Please check:
1. Are coding standards followed correctly?
2. Do naming conventions match the style guide?
3. Is error handling implemented per style.md?
4. Are testing patterns properly applied?
5. Are there any style violations?

Provide specific suggestions for style guide compliance.
```

## Advanced Review Prompts

### Security Review

#### Template
```
Perform a security review of this code against security.md:

Code to Review:
```
{code_block}
```

Security Context:
- Threat Model: [paste relevant threats]
- Security Controls: [paste security controls]
- Authentication: [paste auth requirements]
- Data Protection: [paste data protection rules]

Security Checklist:
1. Are inputs properly validated and sanitized?
2. Are authentication mechanisms correctly implemented?
3. Are authorization checks present and correct?
4. Is sensitive data properly protected?
5. Are security headers implemented?
6. Are there any potential vulnerabilities?

Provide specific security recommendations with code examples.
```

#### Example
```
Perform a security review of this password reset function against security.md:

Code to Review:
```javascript
const resetPassword = async (req, res) => {
  const { email, token, newPassword } = req.body;
  
  const user = await User.findByEmail(email);
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  
  if (user.resetToken !== token) {
    return res.status(400).json({ error: 'Invalid token' });
  }
  
  user.password = newPassword;
  await user.save();
  
  res.json({ message: 'Password reset successful' });
};
```

Security Context:
- Threat Model: Account takeover, token manipulation, timing attacks
- Security Controls: Token expiration, rate limiting, secure token generation
- Authentication: Multi-factor for sensitive operations
- Data Protection: Password hashing, secure token storage

Security Checklist:
1. Are inputs properly validated and sanitized?
2. Are authentication mechanisms correctly implemented?
3. Are authorization checks present and correct?
4. Is sensitive data properly protected?
5. Are security headers implemented?
6. Are there any potential vulnerabilities?

Provide specific security recommendations with code examples.
```

### Performance Review

#### Template
```
Review this code for performance against the constraints in constraints.md:

Code to Review:
```
{code_block}
```

Performance Context:
- Performance Requirements: [paste performance requirements]
- Scalability Constraints: [paste scalability constraints]
- Resource Limits: [paste resource limits]
- Optimization Guidelines: [paste optimization guidelines]

Performance Checklist:
1. Does the code meet response time requirements?
2. Are database queries optimized?
3. Is caching implemented where appropriate?
4. Are resource usage patterns efficient?
5. Will the code scale with increased load?
6. Are there any performance bottlenecks?

Provide specific performance improvement recommendations.
```

#### Example
```
Review this user search function for performance against constraints in constraints.md:

Code to Review:
```javascript
const searchUsers = async (req, res) => {
  const { query, page = 1, limit = 10 } = req.query;
  
  const users = await User.findAll();
  const filtered = users.filter(user => 
    user.name.includes(query) || user.email.includes(query)
  );
  
  const paginated = filtered.slice((page - 1) * limit, page * limit);
  
  res.json({
    users: paginated,
    total: filtered.length,
    page,
    limit
  });
};
```

Performance Context:
- Performance Requirements: <200ms response time, 10,000 concurrent users
- Scalability Constraints: Support 1M+ users, efficient pagination
- Resource Limits: 512MB memory per instance, optimized database queries
- Optimization Guidelines: Use database-level filtering, implement caching

Performance Checklist:
1. Does the code meet response time requirements?
2. Are database queries optimized?
3. Is caching implemented where appropriate?
4. Are resource usage patterns efficient?
5. Will the code scale with increased load?
6. Are there any performance bottlenecks?

Provide specific performance improvement recommendations.
```

### Integration Review

#### Template
```
Review this integration code against integrations.md:

Code to Review:
```
{code_block}
```

Integration Context:
- Service Integration: [paste service integration patterns]
- Error Handling: [paste integration error handling]
- Rate Limiting: [paste rate limiting requirements]
- Monitoring: [paste monitoring requirements]

Integration Checklist:
1. Are integration patterns correctly implemented?
2. Is error handling robust for external failures?
3. Are rate limits respected and handled?
4. Is retry logic implemented appropriately?
5. Are circuit breakers used for protection?
6. Is monitoring and logging adequate?

Provide specific integration improvement recommendations.
```

#### Example
```
Review this email service integration against integrations.md:

Code to Review:
```javascript
const sendEmail = async (to, subject, body) => {
  const response = await fetch('https://api.sendgrid.com/v3/mail/send', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.SENDGRID_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      personalizations: [{ to: [{ email: to }] }],
      from: { email: 'noreply@example.com' },
      subject,
      content: [{ type: 'text/plain', value: body }]
    })
  });
  
  return response.json();
};
```

Integration Context:
- Service Integration: RESTful API with proper authentication
- Error Handling: Retry logic, circuit breaker patterns
- Rate Limiting: SendGrid rate limits, queuing system
- Monitoring: Success/failure tracking, latency monitoring

Integration Checklist:
1. Are integration patterns correctly implemented?
2. Is error handling robust for external failures?
3. Are rate limits respected and handled?
4. Is retry logic implemented appropriately?
5. Are circuit breakers used for protection?
6. Is monitoring and logging adequate?

Provide specific integration improvement recommendations.
```

## Test Review Prompts

### Test Coverage Review

#### Template
```
Review this test suite against testing-strategy.md:

Test Code to Review:
```
{test_code_block}
```

Implementation Code:
```
{implementation_code_block}
```

Testing Context:
- Testing Strategy: [paste testing strategy]
- Coverage Requirements: [paste coverage requirements]
- Test Types: [paste test type requirements]
- Quality Standards: [paste quality standards]

Test Review Checklist:
1. Are all requirements covered by tests?
2. Are test cases comprehensive and clear?
3. Do tests follow the testing strategy?
4. Is coverage adequate for the code complexity?
5. Are edge cases and error conditions tested?
6. Are tests maintainable and reliable?

Provide specific recommendations for test improvements.
```

#### Example
```
Review this user authentication test suite against testing-strategy.md:

Test Code to Review:
```javascript
describe('User Authentication', () => {
  it('should authenticate valid user', async () => {
    const result = await authService.authenticate('user@example.com', 'password');
    expect(result).toBeDefined();
  });
  
  it('should reject invalid password', async () => {
    const result = await authService.authenticate('user@example.com', 'wrong');
    expect(result).toBeNull();
  });
});
```

Implementation Code:
```javascript
const authenticate = async (email, password) => {
  const user = await User.findByEmail(email);
  if (!user || !bcrypt.compareSync(password, user.passwordHash)) {
    return null;
  }
  return generateToken(user);
};
```

Testing Context:
- Testing Strategy: Unit tests 70%, Integration 25%, E2E 5%
- Coverage Requirements: 90% line coverage, 80% branch coverage
- Test Types: Unit tests for business logic, integration for endpoints
- Quality Standards: Clear test names, isolated tests, fast execution

Test Review Checklist:
1. Are all requirements covered by tests?
2. Are test cases comprehensive and clear?
3. Do tests follow the testing strategy?
4. Is coverage adequate for the code complexity?
5. Are edge cases and error conditions tested?
6. Are tests maintainable and reliable?

Provide specific recommendations for test improvements.
```

### End-to-End Test Review

#### Template
```
Review this E2E test against the user scenarios in requirements.md:

E2E Test Code:
```
{e2e_test_code}
```

Requirements Context:
- User Scenarios: [paste user scenarios]
- Acceptance Criteria: [paste acceptance criteria]
- User Journey: [paste user journey]

E2E Review Checklist:
1. Do tests cover complete user scenarios?
2. Are acceptance criteria validated end-to-end?
3. Are user interactions realistic?
4. Are error scenarios included?
5. Are tests stable and maintainable?
6. Do tests validate the complete user journey?

Provide recommendations for E2E test improvements.
```

#### Example
```
Review this E2E test against the user registration scenario in requirements.md:

E2E Test Code:
```javascript
test('User Registration Flow', async ({ page }) => {
  await page.goto('/register');
  await page.fill('[data-testid="email"]', 'user@example.com');
  await page.fill('[data-testid="password"]', 'password123');
  await page.click('[data-testid="submit"]');
  
  await expect(page).toHaveURL('/dashboard');
});
```

Requirements Context:
- User Scenarios: New user registration with email verification
- Acceptance Criteria: Email validation, password strength, welcome email
- User Journey: Register → Email verification → First login → Dashboard

E2E Review Checklist:
1. Do tests cover complete user scenarios?
2. Are acceptance criteria validated end-to-end?
3. Are user interactions realistic?
4. Are error scenarios included?
5. Are tests stable and maintainable?
6. Do tests validate the complete user journey?

Provide recommendations for E2E test improvements.
```

## Compliance Review Prompts

### Accessibility Review

#### Template
```
Review this code for accessibility compliance:

Code to Review:
```
{code_block}
```

Accessibility Context:
- Standards: [paste accessibility standards]
- Requirements: [paste accessibility requirements]
- User Types: [paste user type considerations]

Accessibility Checklist:
1. Are proper ARIA labels and roles used?
2. Is keyboard navigation supported?
3. Are color contrast requirements met?
4. Are screen reader compatible elements used?
5. Are form labels and error messages accessible?
6. Are focus indicators visible and logical?

Provide specific accessibility improvement recommendations.
```

#### Example
```
Review this login form for accessibility compliance:

Code to Review:
```html
<form onSubmit={handleLogin}>
  <input type="email" placeholder="Email" value={email} onChange={setEmail} />
  <input type="password" placeholder="Password" value={password} onChange={setPassword} />
  <button type="submit">Login</button>
</form>
```

Accessibility Context:
- Standards: WCAG 2.1 AA compliance
- Requirements: Screen reader support, keyboard navigation
- User Types: Visually impaired users, motor impaired users

Accessibility Checklist:
1. Are proper ARIA labels and roles used?
2. Is keyboard navigation supported?
3. Are color contrast requirements met?
4. Are screen reader compatible elements used?
5. Are form labels and error messages accessible?
6. Are focus indicators visible and logical?

Provide specific accessibility improvement recommendations.
```

### GDPR Compliance Review

#### Template
```
Review this code for GDPR compliance against compliance.md:

Code to Review:
```
{code_block}
```

GDPR Context:
- Data Processing: [paste data processing requirements]
- User Rights: [paste user rights requirements]
- Consent: [paste consent requirements]
- Data Protection: [paste data protection requirements]

GDPR Checklist:
1. Is consent properly obtained and recorded?
2. Are user rights (access, deletion, portability) supported?
3. Is data minimization principle followed?
4. Are data retention policies implemented?
5. Is personal data properly protected?
6. Are data breach procedures followed?

Provide specific GDPR compliance recommendations.
```

#### Example
```
Review this user data collection for GDPR compliance against compliance.md:

Code to Review:
```javascript
const collectUserData = async (req, res) => {
  const userData = {
    email: req.body.email,
    name: req.body.name,
    phone: req.body.phone,
    address: req.body.address,
    preferences: req.body.preferences,
    trackingId: generateTrackingId(),
    ipAddress: req.ip,
    userAgent: req.headers['user-agent']
  };
  
  await User.create(userData);
  res.json({ message: 'User created successfully' });
};
```

GDPR Context:
- Data Processing: Only collect necessary data with explicit consent
- User Rights: Support data export, deletion, and modification
- Consent: Granular consent for different data types
- Data Protection: Encrypt personal data, audit access

GDPR Checklist:
1. Is consent properly obtained and recorded?
2. Are user rights (access, deletion, portability) supported?
3. Is data minimization principle followed?
4. Are data retention policies implemented?
5. Is personal data properly protected?
6. Are data breach procedures followed?

Provide specific GDPR compliance recommendations.
```

## Automated Review Prompts

### Pull Request Review

#### Template
```
Perform a comprehensive code review of this pull request against all relevant Prizm specifications:

Pull Request Context:
- Title: {pr_title}
- Description: {pr_description}
- Files Changed: {changed_files}
- Related Requirements: {related_requirements}

Code Changes:
```
{code_diff}
```

Relevant Specifications:
- Requirements: [paste relevant requirements]
- Design: [paste relevant design sections]
- Style: [paste relevant style guidelines]
- Testing: [paste testing requirements]

Please provide a comprehensive review covering:
1. Requirements compliance
2. Design pattern adherence
3. Code quality and style
4. Test coverage and quality
5. Security considerations
6. Performance implications
7. Documentation updates needed

Format your review with specific line-by-line feedback and overall assessment.
```

#### Example
```
Perform a comprehensive code review of this pull request against all relevant Prizm specifications:

Pull Request Context:
- Title: Add user profile update functionality
- Description: Implements REQ-004 user profile management with validation
- Files Changed: UserController.js, UserService.js, userRoutes.js
- Related Requirements: REQ-004

Code Changes:
```javascript
// UserController.js
const updateProfile = async (req, res) => {
  try {
    const userId = req.user.id;
    const updates = req.body;
    
    const user = await userService.updateProfile(userId, updates);
    res.json({ user });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

Relevant Specifications:
- Requirements: REQ-004 user profile management with validation
- Design: Repository pattern, service layer architecture
- Style: TypeScript, error handling patterns, async/await
- Testing: Unit tests for services, integration tests for endpoints

Please provide a comprehensive review covering:
1. Requirements compliance
2. Design pattern adherence
3. Code quality and style
4. Test coverage and quality
5. Security considerations
6. Performance implications
7. Documentation updates needed

Format your review with specific line-by-line feedback and overall assessment.
```

### Pre-commit Review

#### Template
```
Perform a pre-commit review of these changes against project specifications:

Staged Changes:
```
{staged_changes}
```

Quick Review Checklist:
1. Do changes follow style guide?
2. Are tests included for new functionality?
3. Are error handling patterns followed?
4. Are security considerations addressed?
5. Are performance implications acceptable?
6. Are documentation updates needed?

Provide a quick assessment with any blocking issues that should prevent commit.
```

#### Example
```
Perform a pre-commit review of these changes against project specifications:

Staged Changes:
```javascript
// Added new function to AuthService.js
const validateToken = (token) => {
  return jwt.verify(token, process.env.JWT_SECRET);
};
```

Quick Review Checklist:
1. Do changes follow style guide?
2. Are tests included for new functionality?
3. Are error handling patterns followed?
4. Are security considerations addressed?
5. Are performance implications acceptable?
6. Are documentation updates needed?

Provide a quick assessment with any blocking issues that should prevent commit.
```

## Review Best Practices

### Effective Review Prompts

1. **Specific Context**: Always include relevant specification sections
2. **Clear Criteria**: Define specific review criteria
3. **Actionable Feedback**: Request specific improvement suggestions
4. **Comprehensive Coverage**: Cover all relevant aspects
5. **Code Examples**: Ask for specific code improvements

### Review Process Integration

1. **Automated Checks**: Use AI for initial review screening
2. **Human Validation**: Always validate AI review feedback
3. **Iterative Improvement**: Use reviews to improve specifications
4. **Learning Opportunities**: Use reviews for team learning
5. **Quality Metrics**: Track review effectiveness

### Common Review Patterns

1. **Requirements First**: Always start with requirements compliance
2. **Design Adherence**: Check architectural and design patterns
3. **Code Quality**: Verify style and quality standards
4. **Security Focus**: Always include security considerations
5. **Performance Impact**: Consider performance implications

---

**Use these review prompts to ensure consistent code quality and specification compliance. Adapt them to your specific project needs and integrate them into your development workflow.**