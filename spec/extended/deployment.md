# Deployment Strategy

## Overview

This document outlines the deployment strategy, infrastructure requirements, and operational procedures for [PROJECT NAME]. It covers environments, CI/CD pipelines, and deployment best practices.

**Project:** [PROJECT NAME]  
**Deployment Model:** [Blue-Green, Rolling, Canary]  
**Infrastructure:** [Cloud provider, on-premise, hybrid]  
**Orchestration:** [Kubernetes, Docker Swarm, serverless]

## Deployment Environments

### Environment Overview

| Environment | Purpose | URL | Auto-Deploy | Manual Approval |
|-------------|---------|-----|-------------|------------------|
| Development | Feature development | dev.example.com | Yes | No |
| Staging | Pre-production testing | staging.example.com | Yes | No |
| Production | Live application | app.example.com | No | Yes |

### Development Environment

**Purpose:** Continuous integration and feature development  
**Infrastructure:** Shared development cluster  
**Data:** Synthetic test data  
**Deployment Trigger:** Every commit to develop branch

**Configuration:**
```yaml
# dev-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: development
data:
  NODE_ENV: "development"
  API_BASE_URL: "https://dev-api.example.com"
  DATABASE_URL: "postgresql://dev-db:5432/devdb"
  REDIS_URL: "redis://dev-redis:6379"
  LOG_LEVEL: "debug"
  ENABLE_METRICS: "true"
  RATE_LIMIT_ENABLED: "false"
```

### Staging Environment

**Purpose:** Production-like testing and validation  
**Infrastructure:** Production-mirror with reduced capacity  
**Data:** Production-like data (anonymized)  
**Deployment Trigger:** Successful builds from main branch

**Configuration:**
```yaml
# staging-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: staging
data:
  NODE_ENV: "staging"
  API_BASE_URL: "https://staging-api.example.com"
  DATABASE_URL: "postgresql://staging-db:5432/stagingdb"
  REDIS_URL: "redis://staging-redis:6379"
  LOG_LEVEL: "info"
  ENABLE_METRICS: "true"
  RATE_LIMIT_ENABLED: "true"
```

### Production Environment

**Purpose:** Live application serving real users  
**Infrastructure:** High-availability cluster  
**Data:** Production data  
**Deployment Trigger:** Manual approval after staging validation

**Configuration:**
```yaml
# production-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  NODE_ENV: "production"
  API_BASE_URL: "https://api.example.com"
  DATABASE_URL: "postgresql://prod-db:5432/proddb"
  REDIS_URL: "redis://prod-redis:6379"
  LOG_LEVEL: "warn"
  ENABLE_METRICS: "true"
  RATE_LIMIT_ENABLED: "true"
```

## Infrastructure Architecture

### Kubernetes Cluster Setup

**Cluster Configuration:**
```yaml
# cluster-config.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: myregistry/web-app:latest
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
---
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
  namespace: production
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 3000
  type: LoadBalancer
```

### Database Setup

**PostgreSQL Configuration:**
```yaml
# database-config.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  namespace: production
spec:
  serviceName: postgres
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        env:
        - name: POSTGRES_DB
          value: "proddb"
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: postgres-data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 100Gi
```

### Redis Configuration

```yaml
# redis-config.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: production
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:6-alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
```

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
# .github/workflows/deploy.yml
name: Deploy Application

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Run linting
        run: npm run lint
      
      - name: Check code coverage
        run: npm run test:coverage
  
  build:
    needs: test
    runs-on: ubuntu-latest
    outputs:
      image: ${{ steps.image.outputs.image }}
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Log in to Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=ref,event=pr
            type=sha,prefix=sha-
      
      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
      
      - name: Output image
        id: image
        run: echo "image=${{ steps.meta.outputs.tags }}" >> $GITHUB_OUTPUT
  
  deploy-dev:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    environment: development
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Development
        run: |
          echo "Deploying ${{ needs.build.outputs.image }} to development"
          kubectl set image deployment/web-app web-app=${{ needs.build.outputs.image }} -n development
          kubectl rollout status deployment/web-app -n development
  
  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: staging
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Staging
        run: |
          echo "Deploying ${{ needs.build.outputs.image }} to staging"
          kubectl set image deployment/web-app web-app=${{ needs.build.outputs.image }} -n staging
          kubectl rollout status deployment/web-app -n staging
      
      - name: Run smoke tests
        run: |
          npm run test:smoke -- --baseUrl=https://staging.example.com
  
  deploy-production:
    needs: [build, deploy-staging]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Production
        run: |
          echo "Deploying ${{ needs.build.outputs.image }} to production"
          kubectl set image deployment/web-app web-app=${{ needs.build.outputs.image }} -n production
          kubectl rollout status deployment/web-app -n production
      
      - name: Run production health checks
        run: |
          npm run test:health -- --baseUrl=https://app.example.com
```

### Docker Configuration

**Dockerfile:**
```dockerfile
# Multi-stage build for optimization
FROM node:18-alpine AS build

WORKDIR /app

# Copy package files
COPY package*.json ./
RUN npm ci --only=production

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM node:18-alpine AS production

# Create app user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

WORKDIR /app

# Copy built application
COPY --from=build --chown=nextjs:nodejs /app/dist ./dist
COPY --from=build --chown=nextjs:nodejs /app/node_modules ./node_modules
COPY --from=build --chown=nextjs:nodejs /app/package.json ./package.json

# Switch to non-root user
USER nextjs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Start application
CMD ["npm", "start"]
```

**Docker Compose for Development:**
```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://postgres:password@db:5432/devdb
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
    volumes:
      - .:/app
      - /app/node_modules
    command: npm run dev
  
  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=devdb
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:6-alpine
    ports:
      - "6379:6379"
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

## Deployment Strategies

### Blue-Green Deployment

**Implementation:**
```yaml
# blue-green-deployment.yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: web-app-rollout
  namespace: production
spec:
  replicas: 3
  strategy:
    blueGreen:
      activeService: web-app-active
      previewService: web-app-preview
      autoPromotionEnabled: false
      scaleDownDelaySeconds: 30
      prePromotionAnalysis:
        templates:
        - templateName: success-rate
        args:
        - name: service-name
          value: web-app-preview
      postPromotionAnalysis:
        templates:
        - templateName: success-rate
        args:
        - name: service-name
          value: web-app-active
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: myregistry/web-app:latest
        ports:
        - containerPort: 3000
```

### Canary Deployment

**Implementation:**
```yaml
# canary-deployment.yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: web-app-canary
  namespace: production
spec:
  replicas: 5
  strategy:
    canary:
      steps:
      - setWeight: 20
      - pause: {duration: 1m}
      - setWeight: 40
      - pause: {duration: 1m}
      - setWeight: 60
      - pause: {duration: 1m}
      - setWeight: 80
      - pause: {duration: 1m}
      analysis:
        templates:
        - templateName: success-rate
        startingStep: 2
        args:
        - name: service-name
          value: web-app-canary
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: myregistry/web-app:latest
        ports:
        - containerPort: 3000
```

## Monitoring and Observability

### Health Checks

**Application Health Endpoint:**
```javascript
// health.js
const express = require('express');
const router = express.Router();

router.get('/health', async (req, res) => {
  const health = {
    status: 'healthy',
    timestamp: new Date().toISOString(),
    service: 'web-app',
    version: process.env.APP_VERSION || 'unknown',
    checks: {}
  };
  
  try {
    // Database health check
    await checkDatabase();
    health.checks.database = { status: 'healthy' };
  } catch (error) {
    health.checks.database = { status: 'unhealthy', error: error.message };
    health.status = 'unhealthy';
  }
  
  try {
    // Redis health check
    await checkRedis();
    health.checks.redis = { status: 'healthy' };
  } catch (error) {
    health.checks.redis = { status: 'unhealthy', error: error.message };
    health.status = 'unhealthy';
  }
  
  const statusCode = health.status === 'healthy' ? 200 : 503;
  res.status(statusCode).json(health);
});

async function checkDatabase() {
  // Database connection check
  const result = await db.query('SELECT 1');
  if (!result) throw new Error('Database query failed');
}

async function checkRedis() {
  // Redis connection check
  const result = await redis.ping();
  if (result !== 'PONG') throw new Error('Redis ping failed');
}

module.exports = router;
```

### Prometheus Metrics

**Metrics Collection:**
```javascript
// metrics.js
const prometheus = require('prom-client');

// Create metrics registry
const register = new prometheus.Registry();

// Add default metrics
prometheus.collectDefaultMetrics({ register });

// Custom metrics
const httpRequestDuration = new prometheus.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status_code'],
  buckets: [0.1, 0.3, 0.5, 0.7, 1, 3, 5, 7, 10]
});

const httpRequestsTotal = new prometheus.Counter({
  name: 'http_requests_total',
  help: 'Total number of HTTP requests',
  labelNames: ['method', 'route', 'status_code']
});

const activeConnections = new prometheus.Gauge({
  name: 'active_connections',
  help: 'Number of active connections'
});

register.registerMetric(httpRequestDuration);
register.registerMetric(httpRequestsTotal);
register.registerMetric(activeConnections);

// Middleware for metrics collection
const metricsMiddleware = (req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    
    httpRequestDuration
      .labels(req.method, req.route?.path || req.path, res.statusCode)
      .observe(duration);
    
    httpRequestsTotal
      .labels(req.method, req.route?.path || req.path, res.statusCode)
      .inc();
  });
  
  next();
};

module.exports = {
  register,
  metricsMiddleware,
  httpRequestDuration,
  httpRequestsTotal,
  activeConnections
};
```

### Logging Configuration

**Structured Logging:**
```javascript
// logger.js
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    service: 'web-app',
    version: process.env.APP_VERSION || 'unknown'
  },
  transports: [
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      )
    })
  ]
});

// Add file transport for production
if (process.env.NODE_ENV === 'production') {
  logger.add(new winston.transports.File({
    filename: 'logs/error.log',
    level: 'error'
  }));
  
  logger.add(new winston.transports.File({
    filename: 'logs/combined.log'
  }));
}

module.exports = logger;
```

## Security Considerations

### Image Security

**Vulnerability Scanning:**
```yaml
# .github/workflows/security.yml
name: Security Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest
          format: 'sarif'
          output: 'trivy-results.sarif'
      
      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```

### Secret Management

**Kubernetes Secrets:**
```yaml
# secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
  namespace: production
type: Opaque
data:
  database-password: <base64-encoded-password>
  redis-password: <base64-encoded-password>
  jwt-secret: <base64-encoded-jwt-secret>
  api-key: <base64-encoded-api-key>
```

**External Secrets Operator:**
```yaml
# external-secret.yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: app-secrets
  namespace: production
spec:
  refreshInterval: 15s
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  target:
    name: app-secrets
    creationPolicy: Owner
  data:
  - secretKey: database-password
    remoteRef:
      key: secret/database
      property: password
  - secretKey: jwt-secret
    remoteRef:
      key: secret/jwt
      property: secret
```

## Disaster Recovery

### Backup Strategy

**Database Backup:**
```yaml
# backup-cronjob.yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: postgres-backup
  namespace: production
spec:
  schedule: "0 2 * * *" # Daily at 2 AM
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: postgres-backup
            image: postgres:13
            command:
            - /bin/bash
            - -c
            - |
              pg_dump -h postgres -U $POSTGRES_USER -d $POSTGRES_DB | \
              gzip > /backup/backup-$(date +%Y%m%d-%H%M%S).sql.gz
              
              # Upload to S3
              aws s3 cp /backup/backup-$(date +%Y%m%d-%H%M%S).sql.gz \
                s3://my-backup-bucket/postgres/
              
              # Clean old local backups
              find /backup -name "*.sql.gz" -mtime +7 -delete
            env:
            - name: POSTGRES_USER
              valueFrom:
                secretKeyRef:
                  name: postgres-secret
                  key: username
            - name: POSTGRES_DB
              value: "proddb"
            - name: PGPASSWORD
              valueFrom:
                secretKeyRef:
                  name: postgres-secret
                  key: password
            volumeMounts:
            - name: backup-storage
              mountPath: /backup
          volumes:
          - name: backup-storage
            persistentVolumeClaim:
              claimName: backup-pvc
          restartPolicy: OnFailure
```

### Recovery Procedures

**Database Recovery:**
```bash
#!/bin/bash
# recovery.sh

# Download backup from S3
aws s3 cp s3://my-backup-bucket/postgres/backup-20240101-020000.sql.gz .

# Restore database
gunzip -c backup-20240101-020000.sql.gz | \
  psql -h postgres -U postgres -d proddb

# Verify restoration
psql -h postgres -U postgres -d proddb -c "SELECT COUNT(*) FROM users;"
```

## Performance Optimization

### Resource Management

**HPA Configuration:**
```yaml
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-app-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-app
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 60
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 10
        periodSeconds: 60
```

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**DevOps Lead:** [Name and Role]  
**Next Review:** [Date]