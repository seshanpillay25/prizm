# Style Guide

## Overview

This document establishes coding standards, conventions, and best practices for [PROJECT NAME]. Consistent style improves code readability, maintainability, and team collaboration.

**Project:** [PROJECT NAME]  
**Scope:** [What aspects of coding style are covered]  
**Enforcement:** [How style guidelines are enforced]

## General Principles

### Code Quality Principles

1. **Readability First:** Code should be self-documenting and easy to understand
2. **Consistency:** Follow established patterns throughout the codebase
3. **Simplicity:** Prefer simple, clear solutions over complex ones
4. **Maintainability:** Write code that's easy to modify and extend
5. **Performance:** Consider performance implications of style choices

### Team Collaboration

- **Code Reviews:** All code must be reviewed before merging
- **Documentation:** Complex logic must be documented
- **Testing:** All code must include appropriate tests
- **Refactoring:** Continuous improvement is encouraged

## Language-Specific Guidelines

### JavaScript/TypeScript

#### Naming Conventions

**Variables and Functions:**
```javascript
// ✅ Good - camelCase
const userName = 'john_doe';
const calculateTotalPrice = (items) => { /* ... */ };

// ❌ Bad - inconsistent naming
const user_name = 'john_doe';
const Calculate_Total_Price = (items) => { /* ... */ };
```

**Constants:**
```javascript
// ✅ Good - UPPER_SNAKE_CASE
const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.example.com';

// ❌ Bad - lowercase
const max_retry_attempts = 3;
```

**Classes:**
```javascript
// ✅ Good - PascalCase
class UserService {
  constructor() { /* ... */ }
}

// ❌ Bad - camelCase
class userService {
  constructor() { /* ... */ }
}
```

#### Code Structure

**Function Declaration:**
```javascript
// ✅ Good - clear, single responsibility
function validateUserInput(input) {
  if (!input || typeof input !== 'string') {
    throw new Error('Invalid input: must be a non-empty string');
  }
  return input.trim();
}

// ❌ Bad - unclear, multiple responsibilities
function doStuff(x) {
  // unclear what this function does
  return x ? x.trim().toLowerCase() : '';
}
```

**Import/Export:**
```javascript
// ✅ Good - explicit imports
import { UserService, EmailService } from './services';
import { validateEmail } from './utils/validation';

// ❌ Bad - wildcard imports
import * as services from './services';
import * as utils from './utils';
```

#### TypeScript Specific

**Type Definitions:**
```typescript
// ✅ Good - explicit types
interface User {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}

function createUser(userData: Omit<User, 'id' | 'createdAt'>): User {
  return {
    id: generateId(),
    createdAt: new Date(),
    ...userData
  };
}

// ❌ Bad - any types
function createUser(userData: any): any {
  return { id: generateId(), createdAt: new Date(), ...userData };
}
```

### Python

#### Naming Conventions

**Variables and Functions:**
```python
# ✅ Good - snake_case
user_name = "john_doe"
def calculate_total_price(items):
    pass

# ❌ Bad - camelCase
userName = "john_doe"
def calculateTotalPrice(items):
    pass
```

**Classes:**
```python
# ✅ Good - PascalCase
class UserService:
    def __init__(self):
        pass

# ❌ Bad - snake_case
class user_service:
    def __init__(self):
        pass
```

#### Code Structure

**Function Definition:**
```python
# ✅ Good - clear docstring and type hints
def validate_user_input(input_data: str) -> str:
    """
    Validate and clean user input.
    
    Args:
        input_data: Raw user input string
        
    Returns:
        Cleaned and validated input string
        
    Raises:
        ValueError: If input is invalid
    """
    if not input_data or not isinstance(input_data, str):
        raise ValueError("Input must be a non-empty string")
    return input_data.strip()

# ❌ Bad - no documentation or type hints
def validate(x):
    return x.strip() if x else ""
```

## File Organization

### Directory Structure

```
src/
├── components/          # Reusable UI components
│   ├── common/         # Shared components
│   └── specific/       # Feature-specific components
├── services/           # Business logic services
├── utils/              # Utility functions
├── types/              # Type definitions
├── constants/          # Application constants
├── hooks/              # Custom hooks (React)
├── stores/             # State management
└── tests/              # Test files
```

### File Naming

**Components:**
```
// ✅ Good - PascalCase for components
UserProfile.tsx
UserProfile.test.tsx
UserProfile.stories.tsx

// ❌ Bad - inconsistent naming
userProfile.tsx
user-profile.tsx
```

**Services and Utilities:**
```
// ✅ Good - camelCase for services
userService.ts
emailService.ts
validationUtils.ts

// ❌ Bad - PascalCase for non-components
UserService.ts
EmailService.ts
```

### Import Organization

```javascript
// ✅ Good - organized imports
// 1. External libraries
import React from 'react';
import { Router } from 'express';
import axios from 'axios';

// 2. Internal modules
import { UserService } from '../services/userService';
import { validateEmail } from '../utils/validation';

// 3. Relative imports
import './Component.css';
```

## Documentation Standards

### Code Comments

**Function Comments:**
```javascript
/**
 * Calculates the total price including tax and discounts
 * @param {Array} items - Array of items with price and quantity
 * @param {number} taxRate - Tax rate as decimal (e.g., 0.08 for 8%)
 * @param {number} discountPercent - Discount percentage (e.g., 10 for 10%)
 * @returns {number} Total price after tax and discount
 */
function calculateTotalPrice(items, taxRate, discountPercent) {
  // Implementation
}
```

**Inline Comments:**
```javascript
// ✅ Good - explain why, not what
const retryLimit = 3; // Business requirement: max 3 attempts before failure

// ❌ Bad - explain what (obvious from code)
const retryLimit = 3; // Set retry limit to 3
```

### README Files

Each module/component should have a README explaining:
- Purpose and functionality
- Usage examples
- API documentation
- Dependencies
- Testing instructions

## Testing Standards

### Test Structure

```javascript
// ✅ Good - descriptive test structure
describe('UserService', () => {
  describe('createUser', () => {
    it('should create a user with valid data', () => {
      // Arrange
      const userData = { name: 'John Doe', email: 'john@example.com' };
      
      // Act
      const result = userService.createUser(userData);
      
      // Assert
      expect(result).toHaveProperty('id');
      expect(result.name).toBe(userData.name);
    });
    
    it('should throw error with invalid email', () => {
      // Test implementation
    });
  });
});
```

### Test Naming

```javascript
// ✅ Good - descriptive test names
it('should return 404 when user does not exist');
it('should validate email format before saving user');
it('should handle network timeout gracefully');

// ❌ Bad - unclear test names
it('should work');
it('test user creation');
it('error handling');
```

## Error Handling

### Error Messages

```javascript
// ✅ Good - specific, actionable error messages
throw new Error('User email is required and must be a valid email address');
throw new ValidationError('Password must be at least 8 characters long');

// ❌ Bad - vague error messages
throw new Error('Invalid input');
throw new Error('Something went wrong');
```

### Error Types

```javascript
// ✅ Good - specific error types
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'ValidationError';
  }
}

class AuthenticationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'AuthenticationError';
  }
}
```

## Performance Guidelines

### Code Optimization

```javascript
// ✅ Good - efficient operations
const userIds = users.map(user => user.id);
const activeUsers = users.filter(user => user.isActive);

// ❌ Bad - inefficient operations
const userIds = [];
for (let i = 0; i < users.length; i++) {
  userIds.push(users[i].id);
}
```

### Memory Management

```javascript
// ✅ Good - proper cleanup
useEffect(() => {
  const subscription = eventSource.subscribe(handler);
  return () => subscription.unsubscribe();
}, []);

// ❌ Bad - memory leak
useEffect(() => {
  eventSource.subscribe(handler);
}, []);
```

## Security Guidelines

### Input Validation

```javascript
// ✅ Good - validate and sanitize input
function createUser(userData) {
  const sanitizedData = {
    name: sanitizeString(userData.name),
    email: validateEmail(userData.email)
  };
  
  return userService.create(sanitizedData);
}

// ❌ Bad - use input directly
function createUser(userData) {
  return userService.create(userData);
}
```

### Sensitive Data

```javascript
// ✅ Good - avoid logging sensitive data
logger.info('User login attempt', { userId: user.id });

// ❌ Bad - log sensitive data
logger.info('User login attempt', { password: user.password });
```

## Code Review Checklist

### Before Submitting

- [ ] Code follows naming conventions
- [ ] Functions are small and focused
- [ ] Complex logic is documented
- [ ] Tests are included and passing
- [ ] No sensitive data is exposed
- [ ] Performance considerations addressed
- [ ] Error handling is appropriate

### Reviewer Checklist

- [ ] Code is readable and maintainable
- [ ] Logic is sound and efficient
- [ ] Tests cover edge cases
- [ ] Documentation is adequate
- [ ] Style guidelines are followed
- [ ] Security best practices applied
- [ ] No code duplication

## Tools and Automation

### Linting

**ESLint Configuration:**
```json
{
  "extends": ["eslint:recommended", "@typescript-eslint/recommended"],
  "rules": {
    "no-unused-vars": "error",
    "no-console": "warn",
    "prefer-const": "error",
    "no-var": "error"
  }
}
```

### Formatting

**Prettier Configuration:**
```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5"
}
```

### Pre-commit Hooks

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,ts,tsx}": ["eslint --fix", "prettier --write"]
  }
}
```

## Exceptions and Waivers

### When to Deviate

1. **Performance Critical Code:** Style may be sacrificed for performance
2. **Third-party Integration:** External API constraints may require deviation
3. **Legacy Code:** Gradual migration approach may be necessary

### Exception Process

1. Document the reason for deviation
2. Get approval from tech lead
3. Add TODO comments for future cleanup
4. Include in code review discussion

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**Style Guide Owner:** [Name and Role]  
**Next Review:** [Date]