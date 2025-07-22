# Architecture Document - ProjectFlow SaaS

## Overview

This document provides a detailed architectural view of ProjectFlow SaaS, focusing on system structure, component interactions, deployment patterns, and architectural decisions. It complements the design document with deeper technical architecture details.

**Architecture Style:** Microservices with Event-Driven Architecture  
**Deployment Model:** Cloud-native with containerization  
**Scalability Approach:** Horizontal scaling with load balancing  
**Data Architecture:** Multi-tenant with shared infrastructure

## System Architecture

### Architectural Layers

```
┌─────────────────────────────────────────────────────────────┐
│                    Presentation Layer                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Web App   │  │ Mobile App  │  │    API      │        │
│  │   (React)   │  │  (Future)   │  │ Clients     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                     API Gateway Layer                      │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              Kong API Gateway                          │ │
│  │  • Authentication & Authorization                      │ │
│  │  • Rate Limiting & Throttling                         │ │
│  │  • Request Routing & Load Balancing                   │ │
│  │  • Request/Response Transformation                    │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                   Application Layer                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │    Auth     │  │   Project   │  │    Task     │        │
│  │  Service    │  │  Service    │  │  Service    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │    User     │  │ Analytics   │  │Notification │        │
│  │  Service    │  │  Service    │  │  Service    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      Data Layer                            │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ PostgreSQL  │  │    Redis    │  │     S3      │        │
│  │  Cluster    │  │   Cluster   │  │   Storage   │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │Elasticsearch│  │  Message    │  │   Metrics   │        │
│  │   Cluster   │  │   Queue     │  │   Store     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

### Component Interaction Diagram

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    Web      │    │     API     │    │    Auth     │
│  Frontend   │◄───┤   Gateway   │◄───┤  Service    │
│             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
                           │                  │
                           ▼                  ▼
                   ┌─────────────┐    ┌─────────────┐
                   │   Project   │    │    User     │
                   │  Service    │◄───┤  Service    │
                   │             │    │             │
                   └─────────────┘    └─────────────┘
                           │                  │
                           ▼                  ▼
                   ┌─────────────┐    ┌─────────────┐
                   │    Task     │    │    Event    │
                   │  Service    │◄───┤     Bus     │
                   │             │    │             │
                   └─────────────┘    └─────────────┘
                           │                  │
                           ▼                  ▼
                   ┌─────────────┐    ┌─────────────┐
                   │ PostgreSQL  │    │    Redis    │
                   │  Database   │    │    Cache    │
                   │             │    │             │
                   └─────────────┘    └─────────────┘
```

## Service Architecture

### Microservices Design

#### Authentication Service
**Purpose:** Centralized authentication and authorization

```typescript
interface AuthService {
  // Core authentication
  authenticateUser(credentials: LoginCredentials): Promise<AuthResult>;
  validateToken(token: string): Promise<UserPayload>;
  refreshToken(refreshToken: string): Promise<TokenPair>;
  
  // Session management
  createSession(userId: string, tenantId: string): Promise<Session>;
  invalidateSession(sessionId: string): Promise<void>;
  
  // Multi-factor authentication
  initiateMFA(userId: string): Promise<MFAChallenge>;
  verifyMFA(userId: string, token: string): Promise<boolean>;
  
  // Single sign-on
  initiateSAML(tenantId: string): Promise<SAMLRequest>;
  processSAMLResponse(response: SAMLResponse): Promise<AuthResult>;
}
```

**Architecture Decisions:**
- **Stateless Design:** JWT tokens for stateless authentication
- **Multi-Tenancy:** Tenant context embedded in all tokens
- **Security:** RS256 signing with rotating keys
- **Scalability:** Horizontal scaling with load balancing
- **Caching:** Redis for session management and token blacklisting

#### User Service
**Purpose:** User management and profile handling

```typescript
interface UserService {
  // User lifecycle
  createUser(userData: CreateUserRequest): Promise<User>;
  updateUser(userId: string, updates: UpdateUserRequest): Promise<User>;
  deactivateUser(userId: string): Promise<void>;
  
  // Profile management
  updateProfile(userId: string, profile: UserProfile): Promise<User>;
  uploadAvatar(userId: string, file: File): Promise<string>;
  
  // Team management
  getTeamMembers(teamId: string): Promise<User[]>;
  addTeamMember(teamId: string, userId: string): Promise<void>;
  removeTeamMember(teamId: string, userId: string): Promise<void>;
}
```

**Architecture Decisions:**
- **Domain-Driven Design:** User aggregate with bounded context
- **Event Sourcing:** User state changes generate events
- **CQRS:** Separate read/write models for user queries
- **Data Consistency:** Eventual consistency for team memberships
- **Performance:** Caching for frequently accessed user data

#### Project Service
**Purpose:** Project lifecycle and management

```typescript
interface ProjectService {
  // Project lifecycle
  createProject(projectData: CreateProjectRequest): Promise<Project>;
  updateProject(projectId: string, updates: UpdateProjectRequest): Promise<Project>;
  archiveProject(projectId: string): Promise<void>;
  
  // Project planning
  createTimeline(projectId: string, timeline: TimelineRequest): Promise<Timeline>;
  updateMilestones(projectId: string, milestones: Milestone[]): Promise<void>;
  
  // Resource management
  assignTeam(projectId: string, teamAssignments: TeamAssignment[]): Promise<void>;
  allocateResources(projectId: string, resources: ResourceAllocation[]): Promise<void>;
  
  // Analytics
  getProjectMetrics(projectId: string): Promise<ProjectMetrics>;
  generateReports(projectId: string, reportType: ReportType): Promise<Report>;
}
```

**Architecture Decisions:**
- **Aggregate Design:** Project as aggregate root with tasks and milestones
- **Event-Driven:** Project state changes trigger workflow events
- **Saga Pattern:** Complex project operations use saga orchestration
- **Caching Strategy:** Multi-level caching for project data
- **Database Partitioning:** Project data partitioned by tenant

#### Task Service
**Purpose:** Task management and workflow

```typescript
interface TaskService {
  // Task lifecycle
  createTask(taskData: CreateTaskRequest): Promise<Task>;
  updateTask(taskId: string, updates: UpdateTaskRequest): Promise<Task>;
  deleteTask(taskId: string): Promise<void>;
  
  // Task assignment
  assignTask(taskId: string, assigneeId: string): Promise<void>;
  reassignTask(taskId: string, newAssigneeId: string): Promise<void>;
  
  // Workflow management
  transitionStatus(taskId: string, newStatus: TaskStatus): Promise<void>;
  addComment(taskId: string, comment: CommentRequest): Promise<Comment>;
  
  // Dependencies
  addDependency(taskId: string, dependsOnTaskId: string): Promise<void>;
  removeDependency(taskId: string, dependsOnTaskId: string): Promise<void>;
  
  // Time tracking
  startTimeEntry(taskId: string, userId: string): Promise<TimeEntry>;
  stopTimeEntry(entryId: string): Promise<TimeEntry>;
}
```

**Architecture Decisions:**
- **State Machine:** Task status transitions controlled by state machine
- **Dependency Graph:** Directed acyclic graph for task dependencies
- **Event Streaming:** Task changes streamed for real-time updates
- **Optimistic Locking:** Prevent concurrent modification conflicts
- **Batch Processing:** Bulk operations for performance

#### Analytics Service
**Purpose:** Data analytics and reporting

```typescript
interface AnalyticsService {
  // Data aggregation
  aggregateProjectMetrics(projectId: string, timeRange: TimeRange): Promise<Metrics>;
  aggregateUserMetrics(userId: string, timeRange: TimeRange): Promise<Metrics>;
  
  // Reporting
  generateDashboard(tenantId: string, dashboardType: DashboardType): Promise<Dashboard>;
  generateCustomReport(reportConfig: ReportConfig): Promise<Report>;
  
  // Predictive analytics
  predictProjectCompletion(projectId: string): Promise<CompletionPrediction>;
  identifyBottlenecks(projectId: string): Promise<Bottleneck[]>;
  
  // Real-time analytics
  getRealtimeMetrics(tenantId: string): Promise<RealtimeMetrics>;
  subscribeToMetrics(tenantId: string): Observable<MetricsUpdate>;
}
```

**Architecture Decisions:**
- **OLAP Design:** Optimized for analytical queries
- **Event Processing:** Real-time stream processing for metrics
- **Data Warehouse:** Separate analytics database for reporting
- **Batch Processing:** Scheduled batch jobs for heavy calculations
- **Caching:** Aggressive caching for dashboard queries

#### Notification Service
**Purpose:** Multi-channel notification delivery

```typescript
interface NotificationService {
  // Notification delivery
  sendNotification(notification: Notification): Promise<void>;
  sendBulkNotifications(notifications: Notification[]): Promise<void>;
  
  // Channel management
  sendEmail(emailData: EmailData): Promise<void>;
  sendWebPush(pushData: WebPushData): Promise<void>;
  sendSlackMessage(slackData: SlackData): Promise<void>;
  
  // Subscription management
  subscribe(userId: string, notificationType: NotificationType): Promise<void>;
  unsubscribe(userId: string, notificationType: NotificationType): Promise<void>;
  
  // Template management
  createTemplate(templateData: TemplateData): Promise<Template>;
  renderTemplate(templateId: string, data: any): Promise<string>;
}
```

**Architecture Decisions:**
- **Message Queue:** Asynchronous message processing
- **Circuit Breaker:** Fault tolerance for external services
- **Template Engine:** Dynamic content generation
- **Retry Logic:** Exponential backoff for failed deliveries
- **Priority Queues:** Different priorities for notification types

## Data Architecture

### Multi-Tenant Database Design

#### Tenant Isolation Strategy

```sql
-- Tenant-aware base table structure
CREATE TABLE base_entity (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    version INTEGER DEFAULT 1
);

-- Row-level security policy
CREATE POLICY tenant_isolation_policy ON base_entity
    USING (tenant_id = current_setting('app.current_tenant_id')::uuid);

-- Tenant-specific indexes
CREATE INDEX idx_base_entity_tenant ON base_entity(tenant_id);
CREATE INDEX idx_base_entity_tenant_created ON base_entity(tenant_id, created_at);
```

#### Database Partitioning Strategy

```sql
-- Horizontal partitioning by tenant_id
CREATE TABLE tasks (
    id UUID PRIMARY KEY,
    tenant_id UUID NOT NULL,
    project_id UUID NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
) PARTITION BY HASH (tenant_id);

-- Create partitions
CREATE TABLE tasks_partition_0 PARTITION OF tasks
    FOR VALUES WITH (modulus 4, remainder 0);
CREATE TABLE tasks_partition_1 PARTITION OF tasks
    FOR VALUES WITH (modulus 4, remainder 1);
CREATE TABLE tasks_partition_2 PARTITION OF tasks
    FOR VALUES WITH (modulus 4, remainder 2);
CREATE TABLE tasks_partition_3 PARTITION OF tasks
    FOR VALUES WITH (modulus 4, remainder 3);
```

#### Connection Pooling Architecture

```typescript
class DatabaseConnectionManager {
  private readPool: Pool;
  private writePool: Pool;
  
  constructor() {
    this.writePool = new Pool({
      host: process.env.DB_WRITE_HOST,
      port: parseInt(process.env.DB_PORT || '5432'),
      database: process.env.DB_NAME,
      user: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      min: 5,
      max: 20,
      idleTimeoutMillis: 30000,
      connectionTimeoutMillis: 2000
    });
    
    this.readPool = new Pool({
      host: process.env.DB_READ_HOST,
      port: parseInt(process.env.DB_PORT || '5432'),
      database: process.env.DB_NAME,
      user: process.env.DB_READ_USER,
      password: process.env.DB_READ_PASSWORD,
      min: 10,
      max: 50,
      idleTimeoutMillis: 30000,
      connectionTimeoutMillis: 2000
    });
  }
  
  async executeQuery(query: string, params: any[], readOnly = false): Promise<any> {
    const pool = readOnly ? this.readPool : this.writePool;
    const client = await pool.connect();
    
    try {
      // Set tenant context
      await client.query(
        'SELECT set_config($1, $2, true)',
        ['app.current_tenant_id', params[0]]
      );
      
      return await client.query(query, params);
    } finally {
      client.release();
    }
  }
}
```

### Caching Architecture

#### Multi-Level Caching Strategy

```typescript
class CacheService {
  private l1Cache: Map<string, CacheEntry>; // In-memory
  private l2Cache: Redis.Redis; // Redis
  private l3Cache: DatabaseService; // Database
  
  async get<T>(key: string): Promise<T | null> {
    // L1 Cache (In-memory)
    const l1Result = this.l1Cache.get(key);
    if (l1Result && !this.isExpired(l1Result)) {
      return l1Result.value;
    }
    
    // L2 Cache (Redis)
    const l2Result = await this.l2Cache.get(key);
    if (l2Result) {
      const value = JSON.parse(l2Result);
      this.l1Cache.set(key, { value, timestamp: Date.now() });
      return value;
    }
    
    // L3 Cache (Database)
    const l3Result = await this.l3Cache.get(key);
    if (l3Result) {
      await this.set(key, l3Result, 300); // 5 minutes TTL
      return l3Result;
    }
    
    return null;
  }
  
  async set<T>(key: string, value: T, ttl: number): Promise<void> {
    // Set in all cache levels
    this.l1Cache.set(key, { value, timestamp: Date.now() });
    await this.l2Cache.setex(key, ttl, JSON.stringify(value));
  }
  
  async invalidate(pattern: string): Promise<void> {
    // Invalidate L1 cache
    for (const [key] of this.l1Cache.entries()) {
      if (key.match(pattern)) {
        this.l1Cache.delete(key);
      }
    }
    
    // Invalidate L2 cache
    const keys = await this.l2Cache.keys(pattern);
    if (keys.length > 0) {
      await this.l2Cache.del(...keys);
    }
  }
}
```

### Event-Driven Architecture

#### Event Bus Implementation

```typescript
interface EventBus {
  publish(event: DomainEvent): Promise<void>;
  subscribe(eventType: string, handler: EventHandler): void;
  unsubscribe(eventType: string, handler: EventHandler): void;
}

class RedisEventBus implements EventBus {
  private redis: Redis.Redis;
  private handlers: Map<string, EventHandler[]>;
  
  constructor(redis: Redis.Redis) {
    this.redis = redis;
    this.handlers = new Map();
    this.setupSubscriptions();
  }
  
  async publish(event: DomainEvent): Promise<void> {
    const eventData = {
      id: event.id,
      type: event.type,
      aggregateId: event.aggregateId,
      tenantId: event.tenantId,
      payload: event.payload,
      timestamp: event.timestamp,
      version: event.version
    };
    
    await this.redis.publish(
      `events:${event.type}`,
      JSON.stringify(eventData)
    );
  }
  
  subscribe(eventType: string, handler: EventHandler): void {
    const handlers = this.handlers.get(eventType) || [];
    handlers.push(handler);
    this.handlers.set(eventType, handlers);
  }
  
  private setupSubscriptions(): void {
    this.redis.psubscribe('events:*');
    this.redis.on('pmessage', async (pattern, channel, message) => {
      const eventType = channel.split(':')[1];
      const handlers = this.handlers.get(eventType) || [];
      
      const event = JSON.parse(message);
      
      for (const handler of handlers) {
        try {
          await handler(event);
        } catch (error) {
          console.error('Event handler failed:', error);
        }
      }
    });
  }
}
```

#### Saga Pattern Implementation

```typescript
class ProjectCreationSaga {
  private steps: SagaStep[] = [
    { name: 'createProject', compensate: 'deleteProject' },
    { name: 'createInitialTasks', compensate: 'deleteInitialTasks' },
    { name: 'assignTeam', compensate: 'unassignTeam' },
    { name: 'sendNotifications', compensate: 'sendCancellationNotifications' }
  ];
  
  async execute(sagaData: ProjectCreationData): Promise<void> {
    const sagaId = generateUUID();
    const sagaState = new SagaState(sagaId, this.steps);
    
    try {
      for (const step of this.steps) {
        await this.executeStep(step, sagaData, sagaState);
        sagaState.markStepCompleted(step.name);
      }
    } catch (error) {
      await this.compensate(sagaState, sagaData);
      throw error;
    }
  }
  
  private async executeStep(
    step: SagaStep,
    sagaData: ProjectCreationData,
    sagaState: SagaState
  ): Promise<void> {
    switch (step.name) {
      case 'createProject':
        sagaData.projectId = await this.projectService.createProject(sagaData.projectData);
        break;
      case 'createInitialTasks':
        sagaData.taskIds = await this.taskService.createInitialTasks(sagaData.projectId);
        break;
      case 'assignTeam':
        await this.projectService.assignTeam(sagaData.projectId, sagaData.teamMembers);
        break;
      case 'sendNotifications':
        await this.notificationService.sendProjectCreatedNotifications(sagaData.projectId);
        break;
    }
  }
  
  private async compensate(sagaState: SagaState, sagaData: ProjectCreationData): Promise<void> {
    const completedSteps = sagaState.getCompletedSteps().reverse();
    
    for (const step of completedSteps) {
      try {
        await this.executeCompensation(step, sagaData);
      } catch (error) {
        console.error('Compensation failed:', error);
      }
    }
  }
}
```

## Security Architecture

### Authentication Flow

```typescript
class AuthenticationFlow {
  async authenticate(credentials: LoginCredentials): Promise<AuthResult> {
    // Step 1: Rate limiting check
    await this.rateLimiter.checkLimit(credentials.email);
    
    // Step 2: Tenant resolution
    const tenant = await this.tenantService.findByEmail(credentials.email);
    if (!tenant) {
      throw new AuthenticationError('Invalid credentials');
    }
    
    // Step 3: User validation
    const user = await this.userService.validateCredentials(
      credentials.email,
      credentials.password,
      tenant.id
    );
    
    // Step 4: Multi-factor authentication
    if (user.mfaEnabled) {
      const mfaChallenge = await this.mfaService.createChallenge(user.id);
      return { requiresMFA: true, challenge: mfaChallenge };
    }
    
    // Step 5: Token generation
    const tokens = await this.tokenService.generateTokens(user, tenant);
    
    // Step 6: Session creation
    const session = await this.sessionService.createSession(user.id, tenant.id);
    
    // Step 7: Audit logging
    await this.auditService.logAuthentication(user.id, tenant.id);
    
    return {
      user: this.mapToUserProfile(user),
      accessToken: tokens.accessToken,
      refreshToken: tokens.refreshToken,
      sessionId: session.id
    };
  }
}
```

### Authorization Middleware

```typescript
class AuthorizationMiddleware {
  authorize(requiredPermissions: Permission[]) {
    return async (req: Request, res: Response, next: NextFunction) => {
      try {
        // Extract token from header
        const token = this.extractToken(req);
        
        // Validate token
        const payload = await this.tokenService.validateToken(token);
        
        // Set tenant context
        await this.tenantService.setContext(payload.tenantId);
        
        // Check permissions
        const hasPermission = await this.permissionService.checkPermissions(
          payload.userId,
          requiredPermissions,
          req.params
        );
        
        if (!hasPermission) {
          throw new AuthorizationError('Insufficient permissions');
        }
        
        // Attach user to request
        req.user = payload;
        req.tenantId = payload.tenantId;
        
        next();
      } catch (error) {
        next(error);
      }
    };
  }
}
```

## Deployment Architecture

### Container Architecture

```dockerfile
# Multi-stage build for production optimization
FROM node:18-alpine AS builder

WORKDIR /app

# Copy dependency files
COPY package*.json ./
COPY tsconfig.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY src/ ./src/

# Build application
RUN npm run build

# Production stage
FROM node:18-alpine AS production

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001

WORKDIR /app

# Copy built application
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/package.json ./package.json

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Start application
CMD ["node", "dist/server.js"]
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: projectflow-api
  namespace: projectflow
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
              name: database-secret
              key: host
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: password
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
      imagePullSecrets:
      - name: registry-secret
---
apiVersion: v1
kind: Service
metadata:
  name: projectflow-api-service
  namespace: projectflow
spec:
  selector:
    app: projectflow-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: ClusterIP
```

### Load Balancer Configuration

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: projectflow-ingress
  namespace: projectflow
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
spec:
  tls:
  - hosts:
    - "*.projectflow.com"
    secretName: projectflow-tls
  rules:
  - host: "*.projectflow.com"
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: projectflow-api-service
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: projectflow-web-service
            port:
              number: 80
```

## Monitoring Architecture

### Observability Stack

```typescript
class ObservabilityService {
  private metrics: prometheus.Registry;
  private tracer: Tracer;
  private logger: winston.Logger;
  
  constructor() {
    this.setupMetrics();
    this.setupTracing();
    this.setupLogging();
  }
  
  private setupMetrics(): void {
    this.metrics = new prometheus.Registry();
    
    // Custom metrics
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
    
    const activeConnections = new prometheus.Gauge({
      name: 'database_active_connections',
      help: 'Number of active database connections',
      labelNames: ['pool_type']
    });
    
    this.metrics.registerMetric(httpRequestDuration);
    this.metrics.registerMetric(databaseQueryDuration);
    this.metrics.registerMetric(activeConnections);
  }
  
  private setupTracing(): void {
    this.tracer = trace.getTracer('projectflow-api');
    
    // Auto-instrumentation
    registerInstrumentations({
      instrumentations: [
        new HttpInstrumentation(),
        new ExpressInstrumentation(),
        new PgInstrumentation(),
        new RedisInstrumentation()
      ]
    });
  }
  
  private setupLogging(): void {
    this.logger = winston.createLogger({
      level: process.env.LOG_LEVEL || 'info',
      format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.errors({ stack: true }),
        winston.format.json()
      ),
      defaultMeta: {
        service: 'projectflow-api',
        version: process.env.APP_VERSION || '1.0.0'
      },
      transports: [
        new winston.transports.Console(),
        new winston.transports.File({ filename: 'app.log' })
      ]
    });
  }
}
```

## Performance Optimization

### Database Query Optimization

```typescript
class OptimizedQueryService {
  async findTasksWithPagination(
    tenantId: string,
    projectId: string,
    filters: TaskFilters,
    pagination: PaginationRequest
  ): Promise<PaginatedTasks> {
    const baseQuery = `
      SELECT 
        t.id,
        t.title,
        t.description,
        t.status,
        t.priority,
        t.due_date,
        t.created_at,
        t.updated_at,
        u.first_name,
        u.last_name,
        u.avatar_url,
        COUNT(*) OVER() as total_count
      FROM tasks t
      LEFT JOIN users u ON t.assignee_id = u.id
      WHERE t.tenant_id = $1 
        AND t.project_id = $2
        AND t.deleted_at IS NULL
    `;
    
    const conditions = [];
    const params = [tenantId, projectId];
    let paramIndex = 3;
    
    // Dynamic filtering with proper indexing
    if (filters.status) {
      conditions.push(`t.status = ANY($${paramIndex})`);
      params.push(Array.isArray(filters.status) ? filters.status : [filters.status]);
      paramIndex++;
    }
    
    if (filters.assigneeId) {
      conditions.push(`t.assignee_id = $${paramIndex}`);
      params.push(filters.assigneeId);
      paramIndex++;
    }
    
    if (filters.search) {
      conditions.push(`t.search_vector @@ plainto_tsquery($${paramIndex})`);
      params.push(filters.search);
      paramIndex++;
    }
    
    if (filters.dueDateRange) {
      conditions.push(`t.due_date BETWEEN $${paramIndex} AND $${paramIndex + 1}`);
      params.push(filters.dueDateRange.start, filters.dueDateRange.end);
      paramIndex += 2;
    }
    
    const whereClause = conditions.length > 0 ? ` AND ${conditions.join(' AND ')}` : '';
    
    // Optimized sorting
    const sortColumn = this.validateSortColumn(filters.sortBy || 'created_at');
    const sortOrder = filters.sortOrder === 'asc' ? 'ASC' : 'DESC';
    const orderBy = `ORDER BY t.${sortColumn} ${sortOrder}`;
    
    // Efficient pagination
    const limit = Math.min(pagination.limit || 20, 100);
    const offset = pagination.offset || 0;
    const limitClause = `LIMIT $${paramIndex} OFFSET $${paramIndex + 1}`;
    params.push(limit, offset);
    
    const finalQuery = `${baseQuery}${whereClause} ${orderBy} ${limitClause}`;
    
    // Execute with prepared statement
    const result = await this.db.query(finalQuery, params);
    
    return {
      items: result.rows.map(row => this.mapToTask(row)),
      totalCount: result.rows[0]?.total_count || 0,
      hasMore: offset + limit < (result.rows[0]?.total_count || 0)
    };
  }
  
  private validateSortColumn(column: string): string {
    const allowedColumns = ['title', 'status', 'priority', 'due_date', 'created_at', 'updated_at'];
    return allowedColumns.includes(column) ? column : 'created_at';
  }
}
```

---

**Document Version:** 1.0  
**Last Updated:** 2024-01-01  
**Architecture Owner:** Senior Engineering Team  
**Next Review:** 2024-02-01