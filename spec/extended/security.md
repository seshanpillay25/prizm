# Security Requirements

## Overview

This document outlines the security requirements, policies, and implementation strategies for [PROJECT NAME]. It covers authentication, authorization, data protection, and security best practices.

**Project:** [PROJECT NAME]  
**Security Framework:** [OWASP, NIST, ISO 27001]  
**Compliance Requirements:** [GDPR, HIPAA, SOC 2]  
**Security Review Schedule:** [Monthly, quarterly]

## Security Architecture

### Security Principles

1. **Defense in Depth:** Multiple layers of security controls
2. **Least Privilege:** Users and systems have minimum required access
3. **Zero Trust:** Never trust, always verify
4. **Fail Secure:** System fails to a secure state
5. **Security by Design:** Security built into the system from the start

### Threat Model

**Assets:**
- User data (PII, credentials, preferences)
- Application source code
- Infrastructure and configuration
- Business logic and algorithms
- Third-party integrations

**Threats:**
- Unauthorized access to user data
- SQL injection attacks
- Cross-site scripting (XSS)
- Cross-site request forgery (CSRF)
- Man-in-the-middle attacks
- Distributed denial of service (DDoS)
- Insider threats

**Vulnerabilities:**
- Weak authentication mechanisms
- Insufficient input validation
- Insecure direct object references
- Security misconfigurations
- Vulnerable dependencies

## Authentication and Authorization

### Authentication Strategy

**Multi-Factor Authentication (MFA):**
```javascript
// MFA implementation
class MFAService {
  constructor(totpService, smsService) {
    this.totpService = totpService;
    this.smsService = smsService;
  }
  
  async setupMFA(userId, method) {
    const user = await User.findById(userId);
    
    switch (method) {
      case 'totp':
        const secret = this.totpService.generateSecret();
        await user.updateMFASecret(secret);
        return {
          secret,
          qrCode: this.totpService.generateQRCode(user.email, secret)
        };
      
      case 'sms':
        await user.updateMFAMethod('sms');
        return { message: 'SMS MFA enabled' };
      
      default:
        throw new Error('Invalid MFA method');
    }
  }
  
  async verifyMFA(userId, token, method) {
    const user = await User.findById(userId);
    
    switch (method) {
      case 'totp':
        return this.totpService.verify(token, user.mfaSecret);
      
      case 'sms':
        return this.smsService.verify(user.phone, token);
      
      default:
        throw new Error('Invalid MFA method');
    }
  }
}
```

**JWT Token Management:**
```javascript
// JWT service implementation
class JWTService {
  constructor(secret, refreshSecret) {
    this.secret = secret;
    this.refreshSecret = refreshSecret;
  }
  
  generateTokens(userId, roles) {
    const accessToken = jwt.sign(
      { 
        userId, 
        roles,
        type: 'access',
        iat: Math.floor(Date.now() / 1000),
        exp: Math.floor(Date.now() / 1000) + (15 * 60) // 15 minutes
      },
      this.secret,
      { algorithm: 'HS256' }
    );
    
    const refreshToken = jwt.sign(
      { 
        userId,
        type: 'refresh',
        iat: Math.floor(Date.now() / 1000),
        exp: Math.floor(Date.now() / 1000) + (7 * 24 * 60 * 60) // 7 days
      },
      this.refreshSecret,
      { algorithm: 'HS256' }
    );
    
    return { accessToken, refreshToken };
  }
  
  verifyToken(token, type = 'access') {
    const secret = type === 'access' ? this.secret : this.refreshSecret;
    
    try {
      const decoded = jwt.verify(token, secret);
      
      if (decoded.type !== type) {
        throw new Error('Invalid token type');
      }
      
      return decoded;
    } catch (error) {
      throw new Error('Invalid token');
    }
  }
  
  async refreshToken(refreshToken) {
    const decoded = this.verifyToken(refreshToken, 'refresh');
    const user = await User.findById(decoded.userId);
    
    if (!user || !user.isActive) {
      throw new Error('User not found or inactive');
    }
    
    return this.generateTokens(user.id, user.roles);
  }
}
```

### Authorization Model

**Role-Based Access Control (RBAC):**
```javascript
// RBAC implementation
class RBACService {
  constructor() {
    this.roles = new Map();
    this.permissions = new Map();
    this.initializeRoles();
  }
  
  initializeRoles() {
    // Define permissions
    this.permissions.set('user:read', 'Read user data');
    this.permissions.set('user:write', 'Write user data');
    this.permissions.set('user:delete', 'Delete user data');
    this.permissions.set('admin:users', 'Manage users');
    this.permissions.set('admin:system', 'System administration');
    
    // Define roles
    this.roles.set('user', {
      name: 'User',
      permissions: ['user:read', 'user:write']
    });
    
    this.roles.set('moderator', {
      name: 'Moderator',
      permissions: ['user:read', 'user:write', 'user:delete']
    });
    
    this.roles.set('admin', {
      name: 'Administrator',
      permissions: ['user:read', 'user:write', 'user:delete', 'admin:users', 'admin:system']
    });
  }
  
  hasPermission(userRoles, requiredPermission) {
    for (const roleName of userRoles) {
      const role = this.roles.get(roleName);
      if (role && role.permissions.includes(requiredPermission)) {
        return true;
      }
    }
    return false;
  }
  
  authorize(requiredPermission) {
    return (req, res, next) => {
      const userRoles = req.user?.roles || [];
      
      if (!this.hasPermission(userRoles, requiredPermission)) {
        return res.status(403).json({ error: 'Insufficient permissions' });
      }
      
      next();
    };
  }
}
```

**Resource-Level Authorization:**
```javascript
// Resource authorization middleware
class ResourceAuthorizationService {
  static async authorizeResource(resourceType, resourceId, userId, action) {
    switch (resourceType) {
      case 'user':
        return await this.authorizeUserResource(resourceId, userId, action);
      
      case 'order':
        return await this.authorizeOrderResource(resourceId, userId, action);
      
      default:
        throw new Error('Unknown resource type');
    }
  }
  
  static async authorizeUserResource(resourceId, userId, action) {
    // Users can only access their own data
    if (action === 'read' || action === 'write') {
      return resourceId === userId;
    }
    
    // Only admins can delete users
    if (action === 'delete') {
      const user = await User.findById(userId);
      return user.roles.includes('admin');
    }
    
    return false;
  }
  
  static async authorizeOrderResource(resourceId, userId, action) {
    const order = await Order.findById(resourceId);
    
    if (!order) {
      return false;
    }
    
    // Users can only access their own orders
    if (action === 'read' || action === 'write') {
      return order.userId === userId;
    }
    
    return false;
  }
}
```

## Data Protection

### Encryption

**Data at Rest:**
```javascript
// Encryption service for sensitive data
const crypto = require('crypto');

class EncryptionService {
  constructor(encryptionKey) {
    this.algorithm = 'aes-256-gcm';
    this.key = Buffer.from(encryptionKey, 'hex');
  }
  
  encrypt(text) {
    const iv = crypto.randomBytes(16);
    const cipher = crypto.createCipher(this.algorithm, this.key);
    cipher.setAAD(Buffer.from('additional data'));
    
    let encrypted = cipher.update(text, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    
    const authTag = cipher.getAuthTag();
    
    return {
      encrypted,
      iv: iv.toString('hex'),
      authTag: authTag.toString('hex')
    };
  }
  
  decrypt(encryptedData) {
    const { encrypted, iv, authTag } = encryptedData;
    
    const decipher = crypto.createDecipher(this.algorithm, this.key);
    decipher.setAAD(Buffer.from('additional data'));
    decipher.setAuthTag(Buffer.from(authTag, 'hex'));
    
    let decrypted = decipher.update(encrypted, 'hex', 'utf8');
    decrypted += decipher.final('utf8');
    
    return decrypted;
  }
}
```

**Password Security:**
```javascript
// Password hashing service
const bcrypt = require('bcrypt');

class PasswordService {
  static async hashPassword(password) {
    const saltRounds = 12;
    return await bcrypt.hash(password, saltRounds);
  }
  
  static async verifyPassword(password, hash) {
    return await bcrypt.compare(password, hash);
  }
  
  static validatePasswordStrength(password) {
    const minLength = 8;
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumbers = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
    
    const errors = [];
    
    if (password.length < minLength) {
      errors.push('Password must be at least 8 characters long');
    }
    
    if (!hasUpperCase) {
      errors.push('Password must contain at least one uppercase letter');
    }
    
    if (!hasLowerCase) {
      errors.push('Password must contain at least one lowercase letter');
    }
    
    if (!hasNumbers) {
      errors.push('Password must contain at least one number');
    }
    
    if (!hasSpecialChar) {
      errors.push('Password must contain at least one special character');
    }
    
    return {
      isValid: errors.length === 0,
      errors
    };
  }
}
```

### Data Privacy

**PII Protection:**
```javascript
// PII handling service
class PIIService {
  static readonly PII_FIELDS = ['email', 'phone', 'address', 'ssn'];
  
  static sanitizeForLogging(data) {
    const sanitized = { ...data };
    
    this.PII_FIELDS.forEach(field => {
      if (sanitized[field]) {
        sanitized[field] = this.maskField(sanitized[field]);
      }
    });
    
    return sanitized;
  }
  
  static maskField(value) {
    if (typeof value === 'string') {
      if (value.includes('@')) {
        // Email masking
        const [local, domain] = value.split('@');
        return `${local.substring(0, 2)}***@${domain}`;
      } else if (value.length > 4) {
        // General string masking
        return `${value.substring(0, 2)}***${value.substring(value.length - 2)}`;
      }
    }
    return '***';
  }
  
  static async anonymizeUser(userId) {
    const user = await User.findById(userId);
    
    if (!user) {
      throw new Error('User not found');
    }
    
    // Anonymize PII
    await user.update({
      email: `deleted-${userId}@example.com`,
      firstName: 'Deleted',
      lastName: 'User',
      phone: null,
      address: null,
      dateOfBirth: null,
      deletedAt: new Date()
    });
    
    return user;
  }
}
```

## Input Validation and Sanitization

### Input Validation

**Validation Middleware:**
```javascript
// Input validation service
const joi = require('joi');

class ValidationService {
  static validate(schema) {
    return (req, res, next) => {
      const { error, value } = schema.validate(req.body, {
        abortEarly: false,
        stripUnknown: true
      });
      
      if (error) {
        const errors = error.details.map(detail => ({
          field: detail.path.join('.'),
          message: detail.message
        }));
        
        return res.status(400).json({
          error: 'Validation failed',
          details: errors
        });
      }
      
      req.validatedData = value;
      next();
    };
  }
  
  static userSchema = joi.object({
    email: joi.string().email().required(),
    firstName: joi.string().trim().min(1).max(50).required(),
    lastName: joi.string().trim().min(1).max(50).required(),
    phone: joi.string().pattern(/^\+?[1-9]\d{1,14}$/).optional(),
    password: joi.string().min(8).required()
  });
  
  static orderSchema = joi.object({
    items: joi.array().items(
      joi.object({
        productId: joi.string().uuid().required(),
        quantity: joi.number().integer().min(1).required()
      })
    ).min(1).required(),
    shippingAddress: joi.object({
      street: joi.string().required(),
      city: joi.string().required(),
      state: joi.string().required(),
      postalCode: joi.string().required(),
      country: joi.string().required()
    }).required()
  });
}
```

### SQL Injection Prevention

**Parameterized Queries:**
```javascript
// Database service with parameterized queries
class DatabaseService {
  constructor(pool) {
    this.pool = pool;
  }
  
  async findUserByEmail(email) {
    // ✅ Safe - uses parameterized query
    const query = 'SELECT * FROM users WHERE email = $1 AND status = $2';
    const values = [email, 'active'];
    
    const result = await this.pool.query(query, values);
    return result.rows[0];
  }
  
  async searchUsers(searchTerm, limit = 10, offset = 0) {
    // ✅ Safe - uses parameterized query with ILIKE
    const query = `
      SELECT id, email, first_name, last_name 
      FROM users 
      WHERE (first_name ILIKE $1 OR last_name ILIKE $1 OR email ILIKE $1)
      AND status = $2
      ORDER BY created_at DESC
      LIMIT $3 OFFSET $4
    `;
    
    const values = [`%${searchTerm}%`, 'active', limit, offset];
    const result = await this.pool.query(query, values);
    return result.rows;
  }
  
  // ❌ NEVER do this - vulnerable to SQL injection
  // async findUserUnsafe(email) {
  //   const query = `SELECT * FROM users WHERE email = '${email}'`;
  //   const result = await this.pool.query(query);
  //   return result.rows[0];
  // }
}
```

### XSS Prevention

**Output Sanitization:**
```javascript
// XSS protection service
const DOMPurify = require('isomorphic-dompurify');

class XSSProtectionService {
  static sanitizeHTML(html) {
    return DOMPurify.sanitize(html, {
      ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p', 'br'],
      ALLOWED_ATTR: []
    });
  }
  
  static escapeHTML(str) {
    return str
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#39;');
  }
  
  static sanitizeUserInput(input) {
    if (typeof input === 'string') {
      return this.escapeHTML(input.trim());
    }
    
    if (typeof input === 'object' && input !== null) {
      const sanitized = {};
      for (const [key, value] of Object.entries(input)) {
        sanitized[key] = this.sanitizeUserInput(value);
      }
      return sanitized;
    }
    
    return input;
  }
}
```

## Security Headers

**Security Middleware:**
```javascript
// Security headers middleware
const helmet = require('helmet');

const securityMiddleware = [
  helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'", "https://fonts.googleapis.com"],
        scriptSrc: ["'self'", "https://cdn.jsdelivr.net"],
        imgSrc: ["'self'", "data:", "https:"],
        connectSrc: ["'self'", "https://api.example.com"],
        fontSrc: ["'self'", "https://fonts.gstatic.com"],
        objectSrc: ["'none'"],
        mediaSrc: ["'self'"],
        frameSrc: ["'none'"]
      }
    },
    crossOriginEmbedderPolicy: false,
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true
    }
  }),
  
  // CSRF protection
  (req, res, next) => {
    res.header('X-CSRF-Token', req.csrfToken());
    next();
  },
  
  // Custom security headers
  (req, res, next) => {
    res.header('X-Content-Type-Options', 'nosniff');
    res.header('X-Frame-Options', 'DENY');
    res.header('X-XSS-Protection', '1; mode=block');
    res.header('Referrer-Policy', 'strict-origin-when-cross-origin');
    next();
  }
];
```

## Rate Limiting and DoS Protection

**Rate Limiting Implementation:**
```javascript
// Rate limiting service
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');

class RateLimitService {
  constructor(redisClient) {
    this.redisClient = redisClient;
  }
  
  createLimiter(options) {
    return rateLimit({
      store: new RedisStore({
        client: this.redisClient,
        prefix: 'rate_limit:'
      }),
      windowMs: options.windowMs,
      max: options.max,
      message: {
        error: 'Too many requests, please try again later.',
        retryAfter: options.windowMs / 1000
      },
      standardHeaders: true,
      legacyHeaders: false,
      skip: (req) => {
        // Skip rate limiting for health checks
        return req.path === '/health';
      },
      keyGenerator: (req) => {
        // Rate limit by IP and user ID if authenticated
        const ip = req.ip;
        const userId = req.user?.id;
        return userId ? `${ip}:${userId}` : ip;
      }
    });
  }
  
  // Different rate limits for different endpoints
  authLimiter = this.createLimiter({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 5 // 5 login attempts per 15 minutes
  });
  
  apiLimiter = this.createLimiter({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100 // 100 requests per 15 minutes
  });
  
  uploadLimiter = this.createLimiter({
    windowMs: 60 * 60 * 1000, // 1 hour
    max: 10 // 10 uploads per hour
  });
}
```

## Security Monitoring

### Audit Logging

**Audit Service:**
```javascript
// Audit logging service
class AuditService {
  constructor(logger) {
    this.logger = logger;
  }
  
  async logSecurityEvent(event) {
    const auditLog = {
      timestamp: new Date().toISOString(),
      eventType: event.type,
      userId: event.userId,
      userEmail: event.userEmail,
      ipAddress: event.ipAddress,
      userAgent: event.userAgent,
      resource: event.resource,
      action: event.action,
      result: event.result,
      details: event.details,
      severity: event.severity || 'info'
    };
    
    // Log to structured logging system
    this.logger.info('Security Event', auditLog);
    
    // Store in audit database
    await this.storeAuditLog(auditLog);
    
    // Send alert for high-severity events
    if (event.severity === 'high' || event.severity === 'critical') {
      await this.sendSecurityAlert(auditLog);
    }
  }
  
  async storeAuditLog(auditLog) {
    // Store audit log in database
    await AuditLog.create(auditLog);
  }
  
  async sendSecurityAlert(auditLog) {
    // Send alert to security team
    // Implementation depends on alerting system
  }
  
  // Convenience methods for common events
  async logLoginAttempt(userId, email, ipAddress, userAgent, success) {
    await this.logSecurityEvent({
      type: 'login_attempt',
      userId,
      userEmail: email,
      ipAddress,
      userAgent,
      action: 'login',
      result: success ? 'success' : 'failure',
      severity: success ? 'info' : 'warning'
    });
  }
  
  async logPasswordChange(userId, email, ipAddress, userAgent) {
    await this.logSecurityEvent({
      type: 'password_change',
      userId,
      userEmail: email,
      ipAddress,
      userAgent,
      action: 'password_change',
      result: 'success',
      severity: 'info'
    });
  }
  
  async logUnauthorizedAccess(userId, resource, action, ipAddress, userAgent) {
    await this.logSecurityEvent({
      type: 'unauthorized_access',
      userId,
      ipAddress,
      userAgent,
      resource,
      action,
      result: 'blocked',
      severity: 'high'
    });
  }
}
```

### Intrusion Detection

**Anomaly Detection:**
```javascript
// Anomaly detection service
class AnomalyDetectionService {
  constructor(redisClient, auditService) {
    this.redis = redisClient;
    this.auditService = auditService;
  }
  
  async detectLoginAnomalies(userId, ipAddress, userAgent) {
    const key = `login_history:${userId}`;
    const recentLogins = await this.redis.lrange(key, 0, 9);
    
    const anomalies = [];
    
    // Check for new IP address
    const recentIPs = recentLogins.map(login => JSON.parse(login).ipAddress);
    if (!recentIPs.includes(ipAddress)) {
      anomalies.push({
        type: 'new_ip_address',
        details: { newIP: ipAddress, recentIPs }
      });
    }
    
    // Check for new user agent
    const recentUserAgents = recentLogins.map(login => JSON.parse(login).userAgent);
    if (!recentUserAgents.includes(userAgent)) {
      anomalies.push({
        type: 'new_user_agent',
        details: { newUserAgent: userAgent }
      });
    }
    
    // Check for rapid successive logins
    if (recentLogins.length >= 3) {
      const lastThreeLogins = recentLogins.slice(0, 3).map(login => JSON.parse(login));
      const timeDiffs = [];
      
      for (let i = 0; i < lastThreeLogins.length - 1; i++) {
        const diff = new Date(lastThreeLogins[i].timestamp) - new Date(lastThreeLogins[i + 1].timestamp);
        timeDiffs.push(diff);
      }
      
      const avgTimeDiff = timeDiffs.reduce((a, b) => a + b, 0) / timeDiffs.length;
      if (avgTimeDiff < 5 * 60 * 1000) { // Less than 5 minutes
        anomalies.push({
          type: 'rapid_logins',
          details: { averageTimeBetweenLogins: avgTimeDiff }
        });
      }
    }
    
    // Log anomalies
    for (const anomaly of anomalies) {
      await this.auditService.logSecurityEvent({
        type: 'login_anomaly',
        userId,
        ipAddress,
        userAgent,
        action: 'login',
        result: 'anomaly_detected',
        severity: 'warning',
        details: anomaly
      });
    }
    
    return anomalies;
  }
  
  async recordLogin(userId, ipAddress, userAgent) {
    const key = `login_history:${userId}`;
    const loginRecord = JSON.stringify({
      timestamp: new Date().toISOString(),
      ipAddress,
      userAgent
    });
    
    await this.redis.lpush(key, loginRecord);
    await this.redis.ltrim(key, 0, 9); // Keep only last 10 logins
    await this.redis.expire(key, 30 * 24 * 60 * 60); // 30 days
  }
}
```

## Compliance and Reporting

### GDPR Compliance

**Data Subject Rights:**
```javascript
// GDPR compliance service
class GDPRComplianceService {
  constructor(encryptionService, auditService) {
    this.encryptionService = encryptionService;
    this.auditService = auditService;
  }
  
  async exportUserData(userId, requesterId) {
    await this.auditService.logSecurityEvent({
      type: 'data_export_request',
      userId: requesterId,
      resource: `user:${userId}`,
      action: 'export',
      result: 'initiated',
      severity: 'info'
    });
    
    // Collect user data from all sources
    const userData = await this.collectUserData(userId);
    
    // Create export file
    const exportData = {
      exportedAt: new Date().toISOString(),
      user: userData.user,
      orders: userData.orders,
      preferences: userData.preferences,
      auditLogs: userData.auditLogs
    };
    
    return exportData;
  }
  
  async deleteUserData(userId, requesterId) {
    await this.auditService.logSecurityEvent({
      type: 'data_deletion_request',
      userId: requesterId,
      resource: `user:${userId}`,
      action: 'delete',
      result: 'initiated',
      severity: 'high'
    });
    
    // Anonymize user data instead of hard delete
    await PIIService.anonymizeUser(userId);
    
    // Mark related data as deleted
    await this.markRelatedDataAsDeleted(userId);
    
    await this.auditService.logSecurityEvent({
      type: 'data_deletion_completed',
      userId: requesterId,
      resource: `user:${userId}`,
      action: 'delete',
      result: 'completed',
      severity: 'info'
    });
  }
  
  async collectUserData(userId) {
    const user = await User.findById(userId);
    const orders = await Order.findByUserId(userId);
    const preferences = await UserPreference.findByUserId(userId);
    const auditLogs = await AuditLog.findByUserId(userId);
    
    return {
      user: user.toJSON(),
      orders: orders.map(order => order.toJSON()),
      preferences: preferences.map(pref => pref.toJSON()),
      auditLogs: auditLogs.map(log => log.toJSON())
    };
  }
  
  async markRelatedDataAsDeleted(userId) {
    // Mark orders as belonging to deleted user
    await Order.update(
      { userId },
      { userDeleted: true, updatedAt: new Date() }
    );
    
    // Remove personal data from audit logs
    await AuditLog.update(
      { userId },
      { userEmail: null, updatedAt: new Date() }
    );
  }
}
```

## Security Testing

### Automated Security Testing

**Security Test Suite:**
```javascript
// Security tests
describe('Security Tests', () => {
  describe('Authentication', () => {
    it('should prevent brute force attacks', async () => {
      const loginAttempts = Array(6).fill().map(() => ({
        email: 'test@example.com',
        password: 'wrongpassword'
      }));
      
      // Make 5 failed attempts
      for (let i = 0; i < 5; i++) {
        const response = await request(app)
          .post('/api/auth/login')
          .send(loginAttempts[i])
          .expect(401);
      }
      
      // 6th attempt should be rate limited
      const response = await request(app)
        .post('/api/auth/login')
        .send(loginAttempts[5])
        .expect(429);
      
      expect(response.body.error).toContain('Too many requests');
    });
    
    it('should require strong passwords', async () => {
      const weakPasswords = [
        'password',
        '123456',
        'password123',
        'Password'
      ];
      
      for (const password of weakPasswords) {
        const response = await request(app)
          .post('/api/auth/register')
          .send({
            email: 'test@example.com',
            password,
            name: 'Test User'
          })
          .expect(400);
        
        expect(response.body.error).toContain('password');
      }
    });
  });
  
  describe('Authorization', () => {
    it('should prevent unauthorized access to user data', async () => {
      const user1Token = await createUserAndGetToken('user1@example.com');
      const user2Token = await createUserAndGetToken('user2@example.com');
      
      // User 1 tries to access User 2's data
      const response = await request(app)
        .get('/api/users/user2-id')
        .set('Authorization', `Bearer ${user1Token}`)
        .expect(403);
      
      expect(response.body.error).toContain('Insufficient permissions');
    });
  });
  
  describe('Input Validation', () => {
    it('should prevent XSS attacks', async () => {
      const xssPayloads = [
        '<script>alert("XSS")</script>',
        'javascript:alert("XSS")',
        '<img src=x onerror=alert("XSS")>'
      ];
      
      for (const payload of xssPayloads) {
        const response = await request(app)
          .post('/api/users')
          .send({
            name: payload,
            email: 'test@example.com'
          })
          .expect(400);
        
        expect(response.body.error).toContain('Invalid input');
      }
    });
    
    it('should prevent SQL injection', async () => {
      const sqlPayloads = [
        "'; DROP TABLE users; --",
        "' OR '1'='1",
        "'; SELECT * FROM users; --"
      ];
      
      for (const payload of sqlPayloads) {
        const response = await request(app)
          .get(`/api/users/search?q=${encodeURIComponent(payload)}`)
          .expect(200);
        
        // Should return empty results, not error
        expect(response.body.results).toEqual([]);
      }
    });
  });
});
```

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**Security Officer:** [Name and Role]  
**Next Review:** [Date]