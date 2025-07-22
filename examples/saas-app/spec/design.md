# Design Document - ProjectFlow SaaS

## Overview

This document outlines the technical architecture and design for ProjectFlow, a comprehensive project management SaaS platform. The design supports multi-tenant architecture, high availability, and scalability to handle 10,000+ concurrent users across 10,000+ workspaces.

**System Type:** Multi-tenant SaaS Platform  
**Architecture Pattern:** Microservices with Event-Driven Architecture  
**Deployment:** Cloud-native with Kubernetes  
**Database:** Multi-tenant with data isolation

## Architecture Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Load Balancer                            │
│                    (AWS ALB / CloudFlare)                       │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────┴───────────────────────────────────────┐
│                    API Gateway                                  │
│              (Kong / AWS API Gateway)                          │
│              - Authentication                                   │
│              - Rate Limiting                                    │
│              - Request Routing                                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
     ┌────────────────────┼────────────────────┐
     │                    │                    │
┌────▼────┐         ┌────▼────┐         ┌────▼────┐
│  Auth   │         │  Core   │         │Analytics│
│ Service │         │ Service │         │ Service │
│ (Node)  │         │ (Node)  │         │ (Node)  │
└────┬────┘         └────┬────┘         └────┬────┘
     │                   │                   │
     │    ┌──────────────┼──────────────┐    │
     │    │              │              │    │
┌────▼────┐       ┌─────▼──────┐  ┌────▼────┐
│  User   │       │  Project   │  │  Task   │
│ Service │       │  Service   │  │ Service │
│ (Node)  │       │  (Node)    │  │ (Node)  │
└────┬────┘       └─────┬──────┘  └────┬────┘
     │                  │              │
     └──────────────────┼──────────────┘
                        │
          ┌─────────────┴─────────────┐
          │                           │
     ┌────▼────┐                ┌────▼────┐
     │PostgreSQL│                │  Redis  │
     │Cluster  │                │ Cluster │
     │(Primary)│                │(Cache)  │
     └─────────┘                └─────────┘
```

### Component Architecture

#### Frontend Layer
- **Technology:** React 18 with TypeScript
- **State Management:** Redux Toolkit + RTK Query
- **Styling:** Tailwind CSS with Headless UI
- **Routing:** React Router v6
- **Build Tool:** Vite with TypeScript

#### API Gateway Layer
- **Technology:** Kong API Gateway
- **Features:** Authentication, rate limiting, request routing
- **Load Balancing:** Round-robin with health checks
- **SSL Termination:** TLS 1.3 with automatic certificate management

#### Service Layer
- **Technology:** Node.js 18+ with TypeScript
- **Framework:** Express.js with Helmet security middleware
- **Authentication:** JWT with RS256 signing
- **Communication:** HTTP REST APIs + GraphQL subscriptions
- **Message Queue:** Redis Pub/Sub for real-time updates

#### Data Layer
- **Primary Database:** PostgreSQL 14+ with read replicas
- **Cache:** Redis 7+ for session storage and query caching
- **File Storage:** AWS S3 with CloudFront CDN
- **Search:** Elasticsearch for full-text search
- **Analytics:** ClickHouse for time-series data

## Database Design

### Multi-Tenant Schema Strategy

**Approach:** Shared Database, Shared Schema with Tenant Isolation

```sql
-- Core tenant table
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    subdomain VARCHAR(50) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    plan_type VARCHAR(20) NOT NULL DEFAULT 'free',
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Users table with tenant isolation
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    email VARCHAR(255) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    role VARCHAR(20) DEFAULT 'member',
    is_active BOOLEAN DEFAULT true,
    last_login_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    UNIQUE(tenant_id, email)
);

-- Projects table
CREATE TABLE projects (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    status VARCHAR(20) DEFAULT 'active',
    start_date DATE,
    end_date DATE,
    budget_amount DECIMAL(12,2),
    budget_currency VARCHAR(3) DEFAULT 'USD',
    owner_id UUID NOT NULL REFERENCES users(id),
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tasks table
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    project_id UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    status VARCHAR(20) DEFAULT 'pending',
    priority VARCHAR(10) DEFAULT 'medium',
    assignee_id UUID REFERENCES users(id),
    reporter_id UUID NOT NULL REFERENCES users(id),
    due_date TIMESTAMP WITH TIME ZONE,
    estimated_hours DECIMAL(5,2),
    actual_hours DECIMAL(5,2),
    tags TEXT[],
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Task dependencies
CREATE TABLE task_dependencies (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    task_id UUID NOT NULL REFERENCES tasks(id) ON DELETE CASCADE,
    depends_on_task_id UUID NOT NULL REFERENCES tasks(id) ON DELETE CASCADE,
    dependency_type VARCHAR(20) DEFAULT 'finish_to_start',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    UNIQUE(task_id, depends_on_task_id)
);
```

### Indexing Strategy

```sql
-- Performance indexes
CREATE INDEX idx_users_tenant_email ON users(tenant_id, email);
CREATE INDEX idx_projects_tenant_status ON projects(tenant_id, status);
CREATE INDEX idx_tasks_tenant_project ON tasks(tenant_id, project_id);
CREATE INDEX idx_tasks_assignee_status ON tasks(assignee_id, status) WHERE assignee_id IS NOT NULL;
CREATE INDEX idx_tasks_due_date ON tasks(due_date) WHERE due_date IS NOT NULL;

-- Full-text search indexes
CREATE INDEX idx_projects_search ON projects USING GIN(to_tsvector('english', name || ' ' || COALESCE(description, '')));
CREATE INDEX idx_tasks_search ON tasks USING GIN(to_tsvector('english', title || ' ' || COALESCE(description, '')));

-- Partial indexes for active records
CREATE INDEX idx_projects_active ON projects(tenant_id, created_at) WHERE status = 'active';
CREATE INDEX idx_tasks_active ON tasks(tenant_id, project_id, created_at) WHERE status != 'completed';
```

### Data Partitioning

```sql
-- Partition large tables by tenant_id ranges
CREATE TABLE tasks_partition_1 PARTITION OF tasks
FOR VALUES FROM ('00000000-0000-0000-0000-000000000000') TO ('33333333-3333-3333-3333-333333333333');

CREATE TABLE tasks_partition_2 PARTITION OF tasks
FOR VALUES FROM ('33333333-3333-3333-3333-333333333333') TO ('66666666-6666-6666-6666-666666666666');

CREATE TABLE tasks_partition_3 PARTITION OF tasks
FOR VALUES FROM ('66666666-6666-6666-6666-666666666666') TO ('99999999-9999-9999-9999-999999999999');
```

## Service Design

### Authentication Service

**Purpose:** Handle user authentication, JWT token management, and session management

```typescript
interface AuthService {
  // User authentication
  login(email: string, password: string, tenantId: string): Promise<AuthResult>;
  logout(token: string): Promise<void>;
  refreshToken(refreshToken: string): Promise<AuthResult>;
  
  // Token management
  generateTokens(user: User): Promise<TokenPair>;
  verifyToken(token: string): Promise<UserPayload>;
  revokeToken(token: string): Promise<void>;
  
  // Password management
  changePassword(userId: string, currentPassword: string, newPassword: string): Promise<void>;
  resetPassword(email: string, tenantId: string): Promise<void>;
  confirmPasswordReset(token: string, newPassword: string): Promise<void>;
}

interface AuthResult {
  user: UserProfile;
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
}

interface TokenPair {
  accessToken: string;
  refreshToken: string;
}
```

### User Service

**Purpose:** User management, profile management, and role-based access control

```typescript
interface UserService {
  // User management
  createUser(userData: CreateUserRequest): Promise<User>;
  getUserById(id: string): Promise<User | null>;
  updateUser(id: string, updates: UpdateUserRequest): Promise<User>;
  deleteUser(id: string): Promise<void>;
  
  // Profile management
  updateProfile(userId: string, profile: UserProfile): Promise<User>;
  uploadAvatar(userId: string, file: File): Promise<string>;
  
  // Role management
  assignRole(userId: string, role: UserRole): Promise<void>;
  getUserPermissions(userId: string): Promise<Permission[]>;
  
  // Team management
  addToTeam(userId: string, teamId: string): Promise<void>;
  removeFromTeam(userId: string, teamId: string): Promise<void>;
  getUserTeams(userId: string): Promise<Team[]>;
}
```

### Project Service

**Purpose:** Project lifecycle management, timeline tracking, and resource allocation

```typescript
interface ProjectService {
  // Project CRUD
  createProject(projectData: CreateProjectRequest): Promise<Project>;
  getProject(id: string): Promise<ProjectDetails>;
  updateProject(id: string, updates: UpdateProjectRequest): Promise<Project>;
  deleteProject(id: string): Promise<void>;
  
  // Project management
  getProjectTasks(projectId: string, filters: TaskFilters): Promise<PaginatedTasks>;
  getProjectTimeline(projectId: string): Promise<Timeline>;
  getProjectMetrics(projectId: string): Promise<ProjectMetrics>;
  
  // Resource management
  assignResources(projectId: string, resources: ResourceAssignment[]): Promise<void>;
  getResourceUtilization(projectId: string): Promise<ResourceUtilization>;
  
  // Milestone management
  createMilestone(projectId: string, milestone: CreateMilestoneRequest): Promise<Milestone>;
  updateMilestone(milestoneId: string, updates: UpdateMilestoneRequest): Promise<Milestone>;
  deleteMilestone(milestoneId: string): Promise<void>;
}
```

### Task Service

**Purpose:** Task management, dependency handling, and time tracking

```typescript
interface TaskService {
  // Task CRUD
  createTask(taskData: CreateTaskRequest): Promise<Task>;
  getTask(id: string): Promise<TaskDetails>;
  updateTask(id: string, updates: UpdateTaskRequest): Promise<Task>;
  deleteTask(id: string): Promise<void>;
  
  // Task management
  assignTask(taskId: string, assigneeId: string): Promise<void>;
  updateTaskStatus(taskId: string, status: TaskStatus): Promise<void>;
  addTaskComment(taskId: string, comment: CreateCommentRequest): Promise<Comment>;
  
  // Dependencies
  addDependency(taskId: string, dependsOnTaskId: string): Promise<void>;
  removeDependency(taskId: string, dependsOnTaskId: string): Promise<void>;
  getDependencyGraph(taskId: string): Promise<DependencyGraph>;
  
  // Time tracking
  startTimeTracking(taskId: string, userId: string): Promise<TimeEntry>;
  stopTimeTracking(entryId: string): Promise<TimeEntry>;
  getTimeEntries(taskId: string): Promise<TimeEntry[]>;
}
```

### Analytics Service

**Purpose:** Data analytics, reporting, and business intelligence

```typescript
interface AnalyticsService {
  // Project analytics
  getProjectMetrics(projectId: string, timeRange: TimeRange): Promise<ProjectMetrics>;
  getTeamPerformance(teamId: string, timeRange: TimeRange): Promise<TeamMetrics>;
  getTaskCompletionTrends(projectId: string, timeRange: TimeRange): Promise<TrendData>;
  
  // Business analytics
  getTenantMetrics(tenantId: string, timeRange: TimeRange): Promise<TenantMetrics>;
  getUsageAnalytics(tenantId: string): Promise<UsageAnalytics>;
  generateCustomReport(reportConfig: ReportConfig): Promise<Report>;
  
  // Predictive analytics
  predictProjectCompletion(projectId: string): Promise<CompletionPrediction>;
  identifyResourceBottlenecks(projectId: string): Promise<Bottleneck[]>;
  suggestTaskPrioritization(projectId: string): Promise<PrioritySuggestion[]>;
}
```

## Security Architecture

### Authentication & Authorization

**JWT Token Structure:**
```typescript
interface JWTPayload {
  sub: string;        // User ID
  tenant: string;     // Tenant ID
  role: string;       // User role
  permissions: string[]; // User permissions
  iat: number;        // Issued at
  exp: number;        // Expiration
  aud: string;        // Audience
  iss: string;        // Issuer
}
```

**Role-Based Access Control:**
```typescript
enum UserRole {
  OWNER = 'owner',
  ADMIN = 'admin',
  MANAGER = 'manager',
  MEMBER = 'member',
  VIEWER = 'viewer'
}

interface Permission {
  resource: string;   // e.g., 'project', 'task', 'user'
  action: string;     // e.g., 'read', 'create', 'update', 'delete'
  conditions?: any;   // Optional conditions
}

const rolePermissions: Record<UserRole, Permission[]> = {
  [UserRole.OWNER]: [
    { resource: '*', action: '*' }
  ],
  [UserRole.ADMIN]: [
    { resource: 'project', action: '*' },
    { resource: 'task', action: '*' },
    { resource: 'user', action: 'read' },
    { resource: 'user', action: 'update', conditions: { not_owner: true } }
  ],
  [UserRole.MANAGER]: [
    { resource: 'project', action: 'read' },
    { resource: 'project', action: 'update', conditions: { is_member: true } },
    { resource: 'task', action: '*', conditions: { in_project: true } },
    { resource: 'user', action: 'read' }
  ],
  [UserRole.MEMBER]: [
    { resource: 'project', action: 'read', conditions: { is_member: true } },
    { resource: 'task', action: 'read', conditions: { in_project: true } },
    { resource: 'task', action: 'update', conditions: { is_assignee: true } },
    { resource: 'user', action: 'read' }
  ],
  [UserRole.VIEWER]: [
    { resource: 'project', action: 'read', conditions: { is_member: true } },
    { resource: 'task', action: 'read', conditions: { in_project: true } }
  ]
};
```

### Data Security

**Encryption Strategy:**
```typescript
interface EncryptionService {
  // Data at rest
  encryptSensitiveData(data: string): Promise<string>;
  decryptSensitiveData(encryptedData: string): Promise<string>;
  
  // Data in transit
  generateAPIKey(): Promise<string>;
  validateAPIKey(apiKey: string): Promise<boolean>;
  
  // Password hashing
  hashPassword(password: string): Promise<string>;
  verifyPassword(password: string, hash: string): Promise<boolean>;
}

// Implementation
class EncryptionService {
  async encryptSensitiveData(data: string): Promise<string> {
    const cipher = crypto.createCipher('aes-256-gcm', process.env.ENCRYPTION_KEY);
    let encrypted = cipher.update(data, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    return encrypted;
  }
  
  async hashPassword(password: string): Promise<string> {
    const saltRounds = 12;
    return bcrypt.hash(password, saltRounds);
  }
  
  async verifyPassword(password: string, hash: string): Promise<boolean> {
    return bcrypt.compare(password, hash);
  }
}
```

### Rate Limiting & Security Middleware

```typescript
// Rate limiting configuration
const rateLimits = {
  auth: {
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 5, // 5 attempts per window
    message: 'Too many authentication attempts'
  },
  api: {
    windowMs: 60 * 1000, // 1 minute
    max: 100, // 100 requests per minute
    message: 'Too many API requests'
  },
  upload: {
    windowMs: 60 * 60 * 1000, // 1 hour
    max: 10, // 10 file uploads per hour
    message: 'Too many file uploads'
  }
};

// Security middleware
const securityMiddleware = [
  helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'"],
        imgSrc: ["'self'", "data:", "https:"],
        connectSrc: ["'self'"],
        fontSrc: ["'self'"]
      }
    }
  }),
  cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
    credentials: true
  }),
  rateLimit(rateLimits.api)
];
```

## Performance Optimization

### Caching Strategy

**Multi-Level Caching:**
```typescript
interface CacheService {
  // L1 Cache - In-memory (Node.js)
  getFromMemory<T>(key: string): T | null;
  setInMemory<T>(key: string, value: T, ttl: number): void;
  
  // L2 Cache - Redis
  getFromRedis<T>(key: string): Promise<T | null>;
  setInRedis<T>(key: string, value: T, ttl: number): Promise<void>;
  
  // L3 Cache - Database query cache
  getCachedQuery<T>(query: string, params: any[]): Promise<T | null>;
  setCachedQuery<T>(query: string, params: any[], result: T, ttl: number): Promise<void>;
}

// Cache keys and TTL configuration
const cacheConfig = {
  user: { ttl: 300 }, // 5 minutes
  project: { ttl: 600 }, // 10 minutes
  task: { ttl: 180 }, // 3 minutes
  analytics: { ttl: 1800 }, // 30 minutes
  session: { ttl: 3600 } // 1 hour
};
```

### Database Optimization

**Query Optimization:**
```typescript
class OptimizedTaskRepository {
  async findTasksWithPagination(
    tenantId: string,
    projectId: string,
    filters: TaskFilters,
    pagination: PaginationOptions
  ): Promise<PaginatedResult<Task>> {
    // Use prepared statements and proper indexing
    const baseQuery = `
      SELECT t.*, 
             u.first_name, u.last_name,
             p.name as project_name,
             COUNT(*) OVER() as total_count
      FROM tasks t
      JOIN users u ON t.assignee_id = u.id
      JOIN projects p ON t.project_id = p.id
      WHERE t.tenant_id = $1 
        AND t.project_id = $2
        AND t.deleted_at IS NULL
    `;
    
    const conditions = [];
    const params = [tenantId, projectId];
    let paramIndex = 3;
    
    // Dynamic filtering
    if (filters.status) {
      conditions.push(`t.status = $${paramIndex}`);
      params.push(filters.status);
      paramIndex++;
    }
    
    if (filters.assigneeId) {
      conditions.push(`t.assignee_id = $${paramIndex}`);
      params.push(filters.assigneeId);
      paramIndex++;
    }
    
    if (filters.search) {
      conditions.push(`(t.title ILIKE $${paramIndex} OR t.description ILIKE $${paramIndex})`);
      params.push(`%${filters.search}%`);
      paramIndex++;
    }
    
    const whereClause = conditions.length > 0 ? ` AND ${conditions.join(' AND ')}` : '';
    
    const orderBy = `ORDER BY t.${filters.sortBy || 'created_at'} ${filters.sortOrder || 'DESC'}`;
    const limitOffset = `LIMIT $${paramIndex} OFFSET $${paramIndex + 1}`;
    
    params.push(pagination.limit, pagination.offset);
    
    const finalQuery = `${baseQuery}${whereClause} ${orderBy} ${limitOffset}`;
    
    const result = await this.db.query(finalQuery, params);
    
    return {
      items: result.rows.map(row => this.mapToTask(row)),
      totalCount: result.rows[0]?.total_count || 0,
      hasMore: pagination.offset + pagination.limit < (result.rows[0]?.total_count || 0)
    };
  }
}
```

### Connection Pooling

```typescript
// Database connection pool
const dbPool = new Pool({
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || '5432'),
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  min: 5,
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
  acquireTimeoutMillis: 60000,
  createTimeoutMillis: 30000,
  destroyTimeoutMillis: 5000,
  reapIntervalMillis: 1000,
  createRetryIntervalMillis: 200
});

// Redis connection pool
const redisPool = new Pool({
  create: () => redis.createClient({ url: process.env.REDIS_URL }),
  destroy: (client) => client.quit(),
  min: 2,
  max: 10,
  acquireTimeoutMillis: 60000,
  createTimeoutMillis: 30000,
  destroyTimeoutMillis: 5000,
  idleTimeoutMillis: 30000,
  reapIntervalMillis: 1000
});
```

## Scalability Design

### Horizontal Scaling

**Load Balancing:**
```yaml
# Kubernetes deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: projectflow-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: projectflow-api
  template:
    metadata:
      labels:
        app: projectflow-api
    spec:
      containers:
      - name: api
        image: projectflow/api:latest
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: production
        - name: DB_HOST
          valueFrom:
            secretKeyRef:
              name: db-secrets
              key: host
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
```

### Database Scaling

**Read Replicas:**
```typescript
class DatabaseService {
  constructor() {
    this.masterPool = new Pool({ /* master config */ });
    this.readReplicas = [
      new Pool({ /* read replica 1 config */ }),
      new Pool({ /* read replica 2 config */ })
    ];
    this.currentReadReplica = 0;
  }
  
  async executeQuery(query: string, params: any[], readOnly = false): Promise<any> {
    if (readOnly) {
      return this.executeReadQuery(query, params);
    }
    return this.masterPool.query(query, params);
  }
  
  private async executeReadQuery(query: string, params: any[]): Promise<any> {
    const replica = this.readReplicas[this.currentReadReplica];
    this.currentReadReplica = (this.currentReadReplica + 1) % this.readReplicas.length;
    
    try {
      return await replica.query(query, params);
    } catch (error) {
      // Fallback to master if read replica fails
      logger.warn('Read replica failed, falling back to master', error);
      return this.masterPool.query(query, params);
    }
  }
}
```

## Monitoring & Observability

### Health Checks

```typescript
class HealthService {
  async getHealthStatus(): Promise<HealthStatus> {
    const checks = await Promise.allSettled([
      this.checkDatabase(),
      this.checkRedis(),
      this.checkExternalServices()
    ]);
    
    const dbHealth = this.getCheckResult(checks[0]);
    const redisHealth = this.getCheckResult(checks[1]);
    const externalHealth = this.getCheckResult(checks[2]);
    
    const overall = dbHealth.status === 'healthy' && redisHealth.status === 'healthy' 
      ? 'healthy' : 'unhealthy';
    
    return {
      status: overall,
      timestamp: new Date().toISOString(),
      services: {
        database: dbHealth,
        redis: redisHealth,
        external: externalHealth
      }
    };
  }
}
```

### Metrics Collection

```typescript
// Prometheus metrics
const httpRequestDuration = new prometheus.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status_code', 'tenant_id'],
  buckets: [0.1, 0.3, 0.5, 0.7, 1, 3, 5, 7, 10]
});

const databaseQueryDuration = new prometheus.Histogram({
  name: 'database_query_duration_seconds',
  help: 'Duration of database queries in seconds',
  labelNames: ['query_type', 'table', 'tenant_id'],
  buckets: [0.01, 0.05, 0.1, 0.2, 0.5, 1, 2, 5]
});

const activeUsers = new prometheus.Gauge({
  name: 'active_users_total',
  help: 'Number of active users',
  labelNames: ['tenant_id']
});
```

## Error Handling & Resilience

### Circuit Breaker Pattern

```typescript
class CircuitBreaker {
  private failures = 0;
  private lastFailureTime = 0;
  private state: 'closed' | 'open' | 'half-open' = 'closed';
  
  constructor(
    private threshold: number = 5,
    private timeout: number = 60000,
    private monitoringPeriod: number = 60000
  ) {}
  
  async execute<T>(operation: () => Promise<T>): Promise<T> {
    if (this.state === 'open') {
      if (Date.now() - this.lastFailureTime > this.timeout) {
        this.state = 'half-open';
      } else {
        throw new Error('Circuit breaker is open');
      }
    }
    
    try {
      const result = await operation();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }
  
  private onSuccess() {
    this.failures = 0;
    this.state = 'closed';
  }
  
  private onFailure() {
    this.failures++;
    this.lastFailureTime = Date.now();
    
    if (this.failures >= this.threshold) {
      this.state = 'open';
    }
  }
}
```

### Retry Strategy

```typescript
class RetryService {
  async executeWithRetry<T>(
    operation: () => Promise<T>,
    maxRetries: number = 3,
    backoffMs: number = 1000
  ): Promise<T> {
    let lastError: Error;
    
    for (let attempt = 0; attempt <= maxRetries; attempt++) {
      try {
        return await operation();
      } catch (error) {
        lastError = error;
        
        if (attempt < maxRetries) {
          await this.delay(backoffMs * Math.pow(2, attempt));
        }
      }
    }
    
    throw lastError!;
  }
  
  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

## Deployment Architecture

### Container Configuration

```dockerfile
# Production Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app

RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

USER nodejs

EXPOSE 3000

ENV NODE_ENV=production
ENV PORT=3000

CMD ["node", "dist/server.js"]
```

### Infrastructure as Code

```yaml
# Kubernetes namespace
apiVersion: v1
kind: Namespace
metadata:
  name: projectflow-production

---
# ConfigMap for environment variables
apiVersion: v1
kind: ConfigMap
metadata:
  name: projectflow-config
  namespace: projectflow-production
data:
  NODE_ENV: production
  PORT: "3000"
  LOG_LEVEL: info

---
# Secret for sensitive data
apiVersion: v1
kind: Secret
metadata:
  name: projectflow-secrets
  namespace: projectflow-production
type: Opaque
data:
  DB_PASSWORD: <base64-encoded-password>
  JWT_PRIVATE_KEY: <base64-encoded-private-key>
  ENCRYPTION_KEY: <base64-encoded-encryption-key>
```

---

**Document Version:** 1.0  
**Last Updated:** 2024-01-01  
**Architecture Owner:** Senior Engineering Team  
**Next Review:** 2024-02-01