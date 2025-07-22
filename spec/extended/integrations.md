# Integrations Document

## Overview

This document outlines all external integrations for [PROJECT NAME], including APIs, services, and third-party systems. It serves as a comprehensive guide for understanding external dependencies and integration patterns.

**Project:** [PROJECT NAME]  
**Integration Architecture:** [Overall integration strategy]  
**Authentication Strategy:** [How external services are authenticated]

## Integration Summary

| Service | Type | Status | Purpose | Owner |
|---------|------|--------|---------|-------|
| [Service 1] | REST API | Active | [Purpose] | [Team/Person] |
| [Service 2] | GraphQL | Active | [Purpose] | [Team/Person] |
| [Service 3] | Webhook | Planned | [Purpose] | [Team/Person] |

## Authentication & Authorization

### API Authentication

#### Service 1: [Service Name]

**Authentication Type:** [OAuth 2.0 / API Key / JWT / Basic Auth]  
**Credentials Storage:** [Environment variables / Secret manager]  
**Token Refresh:** [How tokens are refreshed]

**Implementation:**
```javascript
// Example authentication setup
const authConfig = {
  clientId: process.env.SERVICE1_CLIENT_ID,
  clientSecret: process.env.SERVICE1_CLIENT_SECRET,
  tokenEndpoint: 'https://api.service1.com/oauth/token',
  scopes: ['read', 'write']
};
```

**Environment Variables:**
```bash
SERVICE1_CLIENT_ID=your_client_id
SERVICE1_CLIENT_SECRET=your_client_secret
SERVICE1_BASE_URL=https://api.service1.com
```

#### Service 2: [Service Name]

**Authentication Type:** [Authentication method]  
**Credentials Storage:** [Where credentials are stored]  
**Rate Limiting:** [Rate limit details]

**Implementation:**
```javascript
// Example authentication setup
const headers = {
  'Authorization': `Bearer ${process.env.SERVICE2_API_KEY}`,
  'Content-Type': 'application/json'
};
```

## External Services

### Service 1: [Service Name]

**Purpose:** [What this service provides]  
**Provider:** [Company/Organization]  
**Documentation:** [Link to API documentation]  
**Status:** Active | Planned | Deprecated

#### Connection Details

**Base URL:** `https://api.service1.com`  
**Protocol:** REST  
**Data Format:** JSON  
**Authentication:** OAuth 2.0

#### API Endpoints

##### GET /users

**Purpose:** Retrieve user information  
**Rate Limit:** 100 requests/minute  
**Response Time:** < 200ms

**Request:**
```http
GET /users/{userId}
Authorization: Bearer {token}
```

**Response:**
```json
{
  "id": "123",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2024-01-01T00:00:00Z"
}
```

**Error Handling:**
- `404` - User not found
- `401` - Unauthorized
- `429` - Rate limit exceeded

##### POST /users

**Purpose:** Create a new user  
**Rate Limit:** 10 requests/minute

**Request:**
```json
{
  "name": "John Doe",
  "email": "john@example.com"
}
```

**Response:**
```json
{
  "id": "123",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2024-01-01T00:00:00Z"
}
```

#### Integration Implementation

**Service Class:**
```javascript
class Service1Client {
  constructor(baseUrl, credentials) {
    this.baseUrl = baseUrl;
    this.credentials = credentials;
    this.httpClient = new HttpClient();
  }

  async getUser(userId) {
    try {
      const response = await this.httpClient.get(
        `${this.baseUrl}/users/${userId}`,
        {
          headers: {
            'Authorization': `Bearer ${this.credentials.token}`
          }
        }
      );
      return response.data;
    } catch (error) {
      throw new Service1Error(`Failed to get user: ${error.message}`);
    }
  }

  async createUser(userData) {
    try {
      const response = await this.httpClient.post(
        `${this.baseUrl}/users`,
        userData,
        {
          headers: {
            'Authorization': `Bearer ${this.credentials.token}`,
            'Content-Type': 'application/json'
          }
        }
      );
      return response.data;
    } catch (error) {
      throw new Service1Error(`Failed to create user: ${error.message}`);
    }
  }
}
```

#### Error Handling

**Custom Error Types:**
```javascript
class Service1Error extends Error {
  constructor(message, statusCode = null) {
    super(message);
    this.name = 'Service1Error';
    this.statusCode = statusCode;
  }
}

class Service1RateLimitError extends Service1Error {
  constructor(retryAfter) {
    super('Rate limit exceeded');
    this.name = 'Service1RateLimitError';
    this.retryAfter = retryAfter;
  }
}
```

**Retry Logic:**
```javascript
async function withRetry(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (error instanceof Service1RateLimitError) {
        await sleep(error.retryAfter * 1000);
        continue;
      }
      if (i === maxRetries - 1) throw error;
      await sleep(1000 * Math.pow(2, i)); // Exponential backoff
    }
  }
}
```

#### Health Check

**Endpoint:** `/health`  
**Method:** GET  
**Expected Response:** 200 OK

**Implementation:**
```javascript
async function checkService1Health() {
  try {
    const response = await httpClient.get(`${baseUrl}/health`);
    return response.status === 200;
  } catch (error) {
    return false;
  }
}
```

---

### Service 2: [Service Name]

**Purpose:** [What this service provides]  
**Provider:** [Company/Organization]  
**Documentation:** [Link to API documentation]  
**Status:** Active | Planned | Deprecated

#### Connection Details

**Base URL:** `https://api.service2.com`  
**Protocol:** GraphQL  
**Data Format:** JSON  
**Authentication:** API Key

#### GraphQL Schema

**Queries:**
```graphql
type Query {
  user(id: ID!): User
  users(limit: Int, offset: Int): [User]
}

type User {
  id: ID!
  name: String!
  email: String!
  createdAt: DateTime!
}
```

**Mutations:**
```graphql
type Mutation {
  createUser(input: CreateUserInput!): User
  updateUser(id: ID!, input: UpdateUserInput!): User
}

input CreateUserInput {
  name: String!
  email: String!
}

input UpdateUserInput {
  name: String
  email: String
}
```

#### Integration Implementation

**GraphQL Client:**
```javascript
class Service2Client {
  constructor(endpoint, apiKey) {
    this.endpoint = endpoint;
    this.apiKey = apiKey;
    this.client = new GraphQLClient(endpoint, {
      headers: {
        'Authorization': `Bearer ${apiKey}`
      }
    });
  }

  async getUser(id) {
    const query = `
      query GetUser($id: ID!) {
        user(id: $id) {
          id
          name
          email
          createdAt
        }
      }
    `;
    
    try {
      const data = await this.client.request(query, { id });
      return data.user;
    } catch (error) {
      throw new Service2Error(`Failed to get user: ${error.message}`);
    }
  }

  async createUser(userData) {
    const mutation = `
      mutation CreateUser($input: CreateUserInput!) {
        createUser(input: $input) {
          id
          name
          email
          createdAt
        }
      }
    `;
    
    try {
      const data = await this.client.request(mutation, { input: userData });
      return data.createUser;
    } catch (error) {
      throw new Service2Error(`Failed to create user: ${error.message}`);
    }
  }
}
```

---

## Webhooks

### Incoming Webhooks

#### Service 1 Webhooks

**Purpose:** Receive notifications when user data changes  
**Endpoint:** `/webhooks/service1`  
**Method:** POST  
**Authentication:** HMAC signature verification

**Payload Structure:**
```json
{
  "event": "user.updated",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "user_id": "123",
    "changes": {
      "name": "New Name",
      "email": "new@example.com"
    }
  }
}
```

**Implementation:**
```javascript
app.post('/webhooks/service1', (req, res) => {
  const signature = req.headers['x-service1-signature'];
  const payload = req.body;
  
  // Verify signature
  if (!verifySignature(payload, signature, webhookSecret)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }
  
  // Process webhook
  try {
    processService1Webhook(payload);
    res.status(200).json({ success: true });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

**Signature Verification:**
```javascript
function verifySignature(payload, signature, secret) {
  const computedSignature = crypto
    .createHmac('sha256', secret)
    .update(JSON.stringify(payload))
    .digest('hex');
  
  return `sha256=${computedSignature}` === signature;
}
```

### Outgoing Webhooks

#### Notification Service

**Purpose:** Send notifications to external systems  
**Endpoint:** `https://external-system.com/webhooks/notifications`  
**Method:** POST  
**Authentication:** API Key

**Payload Structure:**
```json
{
  "event": "order.completed",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "order_id": "456",
    "customer_id": "123",
    "total": 99.99
  }
}
```

**Implementation:**
```javascript
async function sendWebhook(event, data) {
  const payload = {
    event,
    timestamp: new Date().toISOString(),
    data
  };
  
  try {
    const response = await httpClient.post(
      'https://external-system.com/webhooks/notifications',
      payload,
      {
        headers: {
          'Authorization': `Bearer ${process.env.EXTERNAL_API_KEY}`,
          'Content-Type': 'application/json'
        }
      }
    );
    
    return response.status === 200;
  } catch (error) {
    logger.error('Failed to send webhook', { error: error.message });
    throw error;
  }
}
```

## Data Synchronization

### Sync Strategy

**Type:** Real-time | Batch | Event-driven  
**Frequency:** [How often sync occurs]  
**Direction:** Bidirectional | Unidirectional  
**Conflict Resolution:** [How conflicts are handled]

#### User Data Sync

**Source:** Internal User Service  
**Target:** External CRM System  
**Frequency:** Real-time via webhooks  
**Conflict Resolution:** Last-write-wins

**Implementation:**
```javascript
class UserSyncService {
  constructor(crmClient) {
    this.crmClient = crmClient;
  }

  async syncUser(userId, changes) {
    try {
      const user = await userService.getUser(userId);
      const crmUser = await this.crmClient.getUser(user.externalId);
      
      if (crmUser.lastModified > user.lastModified) {
        // CRM has newer data, update internal
        await userService.updateUser(userId, crmUser);
      } else {
        // Internal has newer data, update CRM
        await this.crmClient.updateUser(user.externalId, user);
      }
    } catch (error) {
      logger.error('User sync failed', { userId, error: error.message });
      throw error;
    }
  }
}
```

## Monitoring & Observability

### Health Checks

**Endpoint:** `/health/integrations`  
**Method:** GET  
**Response:**
```json
{
  "status": "healthy",
  "services": {
    "service1": {
      "status": "healthy",
      "response_time": "150ms",
      "last_check": "2024-01-01T00:00:00Z"
    },
    "service2": {
      "status": "degraded",
      "response_time": "800ms",
      "last_check": "2024-01-01T00:00:00Z"
    }
  }
}
```

### Metrics

**Key Metrics:**
- API response times
- Error rates
- Rate limit usage
- Webhook delivery success rate
- Data sync lag

**Implementation:**
```javascript
const metrics = {
  apiResponseTime: new Histogram({
    name: 'integration_api_response_time',
    help: 'API response time in milliseconds',
    labelNames: ['service', 'endpoint']
  }),
  
  apiErrorRate: new Counter({
    name: 'integration_api_errors_total',
    help: 'Total API errors',
    labelNames: ['service', 'error_type']
  }),
  
  webhookDeliveryRate: new Counter({
    name: 'webhook_delivery_total',
    help: 'Total webhook deliveries',
    labelNames: ['service', 'status']
  })
};
```

### Alerting

**Critical Alerts:**
- Service downtime (> 5 minutes)
- High error rate (> 5%)
- Webhook delivery failures (> 10%)

**Warning Alerts:**
- Slow response times (> 500ms)
- Rate limit approaching (> 80%)
- Data sync lag (> 1 hour)

## Testing Strategy

### Unit Tests

**Mock External Services:**
```javascript
// Mock Service1 client
jest.mock('./service1Client', () => ({
  Service1Client: jest.fn().mockImplementation(() => ({
    getUser: jest.fn().mockResolvedValue({ id: '123', name: 'Test User' }),
    createUser: jest.fn().mockResolvedValue({ id: '124', name: 'New User' })
  }))
}));
```

### Integration Tests

**Test Environment:**
- Use staging APIs when available
- Mock external services for CI/CD
- Test webhook endpoints with sample payloads

**Example Test:**
```javascript
describe('Service1 Integration', () => {
  it('should create user successfully', async () => {
    const userData = { name: 'Test User', email: 'test@example.com' };
    const result = await service1Client.createUser(userData);
    
    expect(result).toHaveProperty('id');
    expect(result.name).toBe(userData.name);
    expect(result.email).toBe(userData.email);
  });
  
  it('should handle rate limiting', async () => {
    // Mock rate limit response
    service1Client.httpClient.get.mockRejectedValue(
      new Service1RateLimitError(60)
    );
    
    await expect(service1Client.getUser('123')).rejects.toThrow(
      Service1RateLimitError
    );
  });
});
```

## Documentation

### API Documentation

**Location:** `/docs/api/integrations`  
**Format:** OpenAPI/Swagger  
**Auto-generated:** Yes | No

### Runbooks

**Service1 Troubleshooting:**
- Common error scenarios
- Resolution steps
- Escalation procedures

**Webhook Debugging:**
- Payload validation
- Signature verification
- Retry mechanisms

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**Integration Owner:** [Name and Role]  
**Next Review:** [Date]