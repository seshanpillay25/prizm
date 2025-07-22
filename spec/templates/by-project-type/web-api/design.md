# Web API Design

## Architecture Overview

### API Design Philosophy
- **RESTful Design**: Follow REST principles for resource-based URLs
- **Stateless**: Each request contains all information needed
- **Cacheable**: Support HTTP caching where appropriate
- **Layered**: Clear separation of concerns
- **Uniform Interface**: Consistent patterns across all endpoints

### High-Level Architecture
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Client    │────▶│  API Gateway│────▶│ Application │
└─────────────┘     └─────────────┘     └─────────────┘
                           │                     │
                           ▼                     ▼
                    ┌─────────────┐     ┌─────────────┐
                    │Load Balancer│     │  Database   │
                    └─────────────┘     └─────────────┘
```

## Technology Stack

### Backend Framework
**Framework:** [Express.js/Django/Spring Boot/FastAPI]
- **Why:** [Reasoning for choice]
- **Version:** [Specific version]
- **Key Libraries:** [Authentication, validation, etc.]

### Database
**Primary Database:** [PostgreSQL/MySQL/MongoDB]
- **Why:** [ACID compliance, JSON support, etc.]
- **Version:** [Specific version]
- **ORM/ODM:** [Prisma/TypeORM/Mongoose/SQLAlchemy]

### Caching
**Solution:** [Redis/Memcached/In-memory]
- **Use Cases:** Session storage, API response caching
- **TTL Strategy:** Configurable by endpoint type

### External Services
- **Authentication:** [Auth0/Firebase Auth/Custom JWT]
- **Email:** [SendGrid/AWS SES/Mailgun]
- **File Storage:** [AWS S3/Google Cloud Storage/Cloudinary]
- **Payment:** [Stripe/PayPal/Square]

## API Design Patterns

### URL Structure
```
/api/v1/{resource}
/api/v1/{resource}/{id}
/api/v1/{resource}/{id}/{sub-resource}
```

### Resource Naming
- Use nouns, not verbs
- Plural resource names
- Consistent casing (kebab-case or snake_case)
- Nested resources for relationships

### HTTP Methods
- **GET**: Retrieve resources
- **POST**: Create new resources
- **PUT**: Update entire resource
- **PATCH**: Partial resource update
- **DELETE**: Remove resources

### Status Codes
```
200 OK          - Successful GET, PUT, PATCH
201 Created     - Successful POST
204 No Content  - Successful DELETE
400 Bad Request - Client error (validation, etc.)
401 Unauthorized - Authentication required
403 Forbidden   - Insufficient permissions
404 Not Found   - Resource doesn't exist
409 Conflict    - Resource conflict
429 Too Many Requests - Rate limit exceeded
500 Internal Server Error - Server error
```

## Request/Response Format

### Request Format
```http
POST /api/v1/users
Content-Type: application/json
Authorization: Bearer {token}
X-Request-ID: {uuid}

{
  "name": "John Doe",
  "email": "john@example.com",
  "metadata": {}
}
```

### Response Format
```json
{
  "data": {
    "id": "user_123",
    "name": "John Doe",
    "email": "john@example.com",
    "created_at": "2024-01-15T10:00:00Z",
    "updated_at": "2024-01-15T10:00:00Z"
  },
  "meta": {
    "request_id": "req_456",
    "timestamp": "2024-01-15T10:00:00Z"
  }
}
```

### Error Response Format
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "code": "INVALID_FORMAT",
        "message": "Invalid email format"
      }
    ]
  },
  "meta": {
    "request_id": "req_456",
    "timestamp": "2024-01-15T10:00:00Z"
  }
}
```

## Authentication & Authorization

### JWT Token Structure
```javascript
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "sub": "user_123",
    "iat": 1674648000,
    "exp": 1674651600,
    "scope": ["read:users", "write:users"],
    "role": "user"
  }
}
```

### Authorization Middleware
```javascript
async function authorize(requiredScope) {
  return async (req, res, next) => {
    const token = extractToken(req);
    const payload = verifyToken(token);
    
    if (!hasScope(payload.scope, requiredScope)) {
      return res.status(403).json({
        error: { code: 'FORBIDDEN', message: 'Insufficient permissions' }
      });
    }
    
    req.user = payload;
    next();
  };
}
```

## Data Modeling

### User Entity
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'user',
  email_verified BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

### Database Relationships
```
Users ──┬── UserSessions
        ├── UserRoles
        └── UserProfiles
            └── UserPreferences
```

### Data Access Layer
```javascript
class UserRepository {
  async create(userData) {
    return await this.db.users.create({
      data: userData
    });
  }
  
  async findById(id) {
    return await this.db.users.findUnique({
      where: { id },
      include: { profile: true }
    });
  }
  
  async update(id, updates) {
    return await this.db.users.update({
      where: { id },
      data: { ...updates, updated_at: new Date() }
    });
  }
}
```

## Caching Strategy

### Cache Layers
1. **CDN/Edge Cache**: Static assets, public API responses
2. **Application Cache**: User sessions, configuration
3. **Database Cache**: Query result caching

### Cache Keys
```
user:{user_id}                 # User profile data
user:{user_id}:permissions     # User permissions
api:users:list:{hash}          # Paginated user lists
config:app                     # Application configuration
```

### Cache Invalidation
```javascript
async function invalidateUserCache(userId) {
  await cache.del(`user:${userId}`);
  await cache.del(`user:${userId}:permissions`);
  await cache.delPattern('api:users:list:*');
}
```

## Rate Limiting

### Rate Limit Strategy
```javascript
const rateLimits = {
  'GET /api/v1/*': { requests: 1000, window: '15m' },
  'POST /api/v1/*': { requests: 100, window: '15m' },
  'POST /api/v1/auth/*': { requests: 10, window: '15m' }
};
```

### Rate Limit Headers
```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1674648900
Retry-After: 60
```

## Security Considerations

### Input Validation
```javascript
const userSchema = {
  type: 'object',
  properties: {
    name: { type: 'string', minLength: 1, maxLength: 255 },
    email: { type: 'string', format: 'email' },
    password: { type: 'string', minLength: 8 }
  },
  required: ['name', 'email', 'password']
};
```

### SQL Injection Prevention
- Use parameterized queries
- Input sanitization
- ORM/query builder usage
- Principle of least privilege for DB users

### Security Headers
```javascript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'"]
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true
  }
}));
```

## Error Handling

### Error Classification
```javascript
class APIError extends Error {
  constructor(message, statusCode, code, details = null) {
    super(message);
    this.statusCode = statusCode;
    this.code = code;
    this.details = details;
  }
}

class ValidationError extends APIError {
  constructor(details) {
    super('Validation failed', 400, 'VALIDATION_ERROR', details);
  }
}
```

### Global Error Handler
```javascript
function errorHandler(err, req, res, next) {
  const error = {
    code: err.code || 'INTERNAL_ERROR',
    message: err.message || 'Internal server error'
  };
  
  if (err.details) error.details = err.details;
  
  res.status(err.statusCode || 500).json({
    error,
    meta: {
      request_id: req.id,
      timestamp: new Date().toISOString()
    }
  });
}
```

## API Versioning

### Version Strategy
- URL-based versioning: `/api/v1/`, `/api/v2/`
- Semantic versioning for breaking changes
- Deprecation notices in headers
- Migration guides for version changes

### Version Headers
```http
API-Version: v1
API-Supported-Versions: v1,v2
API-Deprecated-Versions: v1
Sunset: Sat, 31 Dec 2024 23:59:59 GMT
```

## Performance Optimization

### Database Optimization
- Indexing strategy for common queries
- Connection pooling
- Read replicas for read-heavy operations
- Query optimization and monitoring

### Response Optimization
- Gzip compression
- Response pagination
- Field selection/sparse fieldsets
- ETags for conditional requests

### Monitoring Points
- Response time percentiles
- Error rates by endpoint
- Database query performance
- Cache hit rates
- Rate limit violations