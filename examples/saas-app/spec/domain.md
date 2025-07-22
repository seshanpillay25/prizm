# Domain Model - ProjectFlow SaaS

## Overview

This document defines the business domain model for ProjectFlow, establishing the core business concepts, relationships, and rules that govern the multi-tenant project management platform.

**Domain:** Project Management and Team Collaboration  
**Context:** Multi-tenant SaaS Platform  
**Bounded Context:** ProjectFlow Workspace Management

## Domain Entities

### Core Business Entities

#### Tenant (Workspace)
**Purpose:** Represents an organizational boundary within the multi-tenant system

```typescript
interface Tenant {
  id: TenantId;
  subdomain: string;
  name: string;
  plan: SubscriptionPlan;
  settings: TenantSettings;
  createdAt: Date;
  updatedAt: Date;
}

interface TenantSettings {
  timezone: string;
  dateFormat: string;
  workingHours: WorkingHours;
  notifications: NotificationSettings;
  security: SecuritySettings;
}
```

**Business Rules:**
- Each tenant must have a unique subdomain
- Subdomain cannot be changed after creation
- Tenant data is completely isolated from other tenants
- Plan determines feature availability and user limits
- Settings apply to all users within the tenant

#### User
**Purpose:** Represents a person who can access the system within a tenant context

```typescript
interface User {
  id: UserId;
  tenantId: TenantId;
  email: string;
  profile: UserProfile;
  role: UserRole;
  permissions: Permission[];
  preferences: UserPreferences;
  status: UserStatus;
  lastActiveAt: Date;
  createdAt: Date;
  updatedAt: Date;
}

interface UserProfile {
  firstName: string;
  lastName: string;
  avatar?: string;
  jobTitle?: string;
  department?: string;
  phoneNumber?: string;
  timezone: string;
}

enum UserRole {
  OWNER = 'owner',
  ADMIN = 'admin',
  MANAGER = 'manager',
  MEMBER = 'member',
  VIEWER = 'viewer'
}
```

**Business Rules:**
- Users belong to exactly one tenant
- Email addresses must be unique within a tenant
- Users can have multiple roles across different projects
- User permissions are inherited from roles plus explicit grants
- Inactive users retain data but cannot access the system

#### Project
**Purpose:** Represents a collection of related work with defined scope, timeline, and objectives

```typescript
interface Project {
  id: ProjectId;
  tenantId: TenantId;
  name: string;
  description: string;
  status: ProjectStatus;
  timeline: ProjectTimeline;
  budget: ProjectBudget;
  team: ProjectTeam;
  settings: ProjectSettings;
  metrics: ProjectMetrics;
  createdAt: Date;
  updatedAt: Date;
}

interface ProjectTimeline {
  startDate: Date;
  endDate: Date;
  phases: ProjectPhase[];
  milestones: Milestone[];
}

interface ProjectBudget {
  amount: number;
  currency: string;
  spent: number;
  remaining: number;
  categories: BudgetCategory[];
}

enum ProjectStatus {
  PLANNING = 'planning',
  ACTIVE = 'active',
  ON_HOLD = 'on_hold',
  COMPLETED = 'completed',
  CANCELLED = 'cancelled'
}
```

**Business Rules:**
- Projects must have a defined start and end date
- Project status changes trigger workflow validations
- Budget tracking is optional but recommended
- Team members must be assigned specific roles
- Completed projects become read-only except for reporting

#### Task
**Purpose:** Represents a discrete unit of work within a project

```typescript
interface Task {
  id: TaskId;
  tenantId: TenantId;
  projectId: ProjectId;
  title: string;
  description: string;
  status: TaskStatus;
  priority: TaskPriority;
  assignment: TaskAssignment;
  timeline: TaskTimeline;
  effort: TaskEffort;
  dependencies: TaskDependency[];
  attachments: Attachment[];
  comments: Comment[];
  history: TaskHistory[];
  createdAt: Date;
  updatedAt: Date;
}

interface TaskAssignment {
  assigneeId?: UserId;
  reporterId: UserId;
  reviewerId?: UserId;
  assignedAt?: Date;
}

interface TaskTimeline {
  dueDate?: Date;
  startDate?: Date;
  completedAt?: Date;
  estimatedDuration?: number;
}

enum TaskStatus {
  BACKLOG = 'backlog',
  TODO = 'todo',
  IN_PROGRESS = 'in_progress',
  IN_REVIEW = 'in_review',
  DONE = 'done',
  CANCELLED = 'cancelled'
}

enum TaskPriority {
  CRITICAL = 'critical',
  HIGH = 'high',
  MEDIUM = 'medium',
  LOW = 'low'
}
```

**Business Rules:**
- Tasks must belong to a project within the same tenant
- Task status changes must follow defined workflows
- Dependencies cannot create circular references
- Assigned users must be project team members
- Time tracking is mandatory for billable projects

### Supporting Entities

#### Team
**Purpose:** Represents a group of users working together within a project or organization

```typescript
interface Team {
  id: TeamId;
  tenantId: TenantId;
  name: string;
  description: string;
  members: TeamMember[];
  lead: UserId;
  projects: ProjectId[];
  skills: Skill[];
  capacity: TeamCapacity;
  createdAt: Date;
  updatedAt: Date;
}

interface TeamMember {
  userId: UserId;
  role: TeamRole;
  joinedAt: Date;
  capacity: number; // Percentage of full-time
  skills: Skill[];
}

enum TeamRole {
  LEAD = 'lead',
  SENIOR = 'senior',
  MEMBER = 'member',
  CONTRIBUTOR = 'contributor'
}
```

**Business Rules:**
- Teams can work on multiple projects simultaneously
- Team leads have administrative privileges within their team
- Member capacity cannot exceed 100% across all projects
- Skills are used for task assignment optimization
- Team structure affects project access permissions

#### Milestone
**Purpose:** Represents significant project checkpoints or deliverables

```typescript
interface Milestone {
  id: MilestoneId;
  tenantId: TenantId;
  projectId: ProjectId;
  name: string;
  description: string;
  dueDate: Date;
  status: MilestoneStatus;
  deliverables: Deliverable[];
  tasks: TaskId[];
  dependencies: MilestoneId[];
  completedAt?: Date;
  createdAt: Date;
  updatedAt: Date;
}

enum MilestoneStatus {
  PLANNED = 'planned',
  IN_PROGRESS = 'in_progress',
  COMPLETED = 'completed',
  DELAYED = 'delayed'
}
```

**Business Rules:**
- Milestones mark important project phases
- Milestone completion requires all linked tasks to be done
- Milestone delays trigger project risk assessments
- Deliverables must be approved before milestone completion
- Milestone dependencies affect project timeline calculations

#### Workflow
**Purpose:** Defines business processes and approval flows

```typescript
interface Workflow {
  id: WorkflowId;
  tenantId: TenantId;
  name: string;
  description: string;
  entityType: EntityType;
  steps: WorkflowStep[];
  triggers: WorkflowTrigger[];
  conditions: WorkflowCondition[];
  isActive: boolean;
  createdAt: Date;
  updatedAt: Date;
}

interface WorkflowStep {
  id: StepId;
  name: string;
  type: StepType;
  assignee: AssigneeRule;
  actions: WorkflowAction[];
  timeLimit?: number;
  required: boolean;
}

enum EntityType {
  TASK = 'task',
  PROJECT = 'project',
  USER = 'user',
  BUDGET = 'budget'
}
```

**Business Rules:**
- Workflows define approval processes for different entities
- Workflow steps must be completed in order
- Step assignments can be role-based or user-specific
- Time limits trigger escalation procedures
- Workflow changes don't affect in-progress instances

## Domain Relationships

### Tenant Relationships
```
Tenant (1) ←→ (n) User
Tenant (1) ←→ (n) Project
Tenant (1) ←→ (n) Team
Tenant (1) ←→ (n) Workflow
Tenant (1) ←→ (1) Subscription
```

### Project Relationships
```
Project (1) ←→ (n) Task
Project (1) ←→ (n) Milestone
Project (1) ←→ (n) Team (many-to-many)
Project (1) ←→ (n) Budget
Project (1) ←→ (n) ProjectMember
```

### Task Relationships
```
Task (1) ←→ (n) Comment
Task (1) ←→ (n) Attachment
Task (1) ←→ (n) TimeEntry
Task (1) ←→ (n) TaskDependency
Task (n) ←→ (1) User (assignee)
Task (n) ←→ (1) User (reporter)
```

### User Relationships
```
User (1) ←→ (n) Task (as assignee)
User (1) ←→ (n) Task (as reporter)
User (n) ←→ (n) Team (many-to-many)
User (n) ←→ (n) Project (many-to-many)
User (1) ←→ (n) Comment
User (1) ←→ (n) TimeEntry
```

## Value Objects

### Time Period
```typescript
interface TimePeriod {
  startDate: Date;
  endDate: Date;
  
  duration(): number;
  overlaps(other: TimePeriod): boolean;
  contains(date: Date): boolean;
  isValid(): boolean;
}
```

### Money
```typescript
interface Money {
  amount: number;
  currency: string;
  
  add(other: Money): Money;
  subtract(other: Money): Money;
  multiply(factor: number): Money;
  equals(other: Money): boolean;
  isValid(): boolean;
}
```

### Progress
```typescript
interface Progress {
  completed: number;
  total: number;
  
  percentage(): number;
  isComplete(): boolean;
  remaining(): number;
}
```

### Notification
```typescript
interface Notification {
  id: NotificationId;
  recipientId: UserId;
  type: NotificationType;
  title: string;
  message: string;
  data: any;
  priority: NotificationPriority;
  channels: NotificationChannel[];
  status: NotificationStatus;
  createdAt: Date;
  readAt?: Date;
  scheduledAt?: Date;
}

enum NotificationType {
  TASK_ASSIGNED = 'task_assigned',
  TASK_COMPLETED = 'task_completed',
  MILESTONE_REACHED = 'milestone_reached',
  PROJECT_DELAYED = 'project_delayed',
  BUDGET_EXCEEDED = 'budget_exceeded',
  TEAM_INVITATION = 'team_invitation'
}
```

## Domain Services

### Project Planning Service
**Purpose:** Handles complex project planning logic

```typescript
interface ProjectPlanningService {
  calculateProjectTimeline(project: Project): ProjectTimeline;
  identifyDependencyConflicts(tasks: Task[]): ConflictReport;
  optimizeResourceAllocation(project: Project): AllocationPlan;
  predictProjectCompletion(project: Project): CompletionPrediction;
  generateProjectSchedule(project: Project): Schedule;
}
```

**Business Rules:**
- Timeline calculations consider task dependencies
- Resource allocation respects team capacity constraints
- Predictions use historical data and current velocity
- Schedule generation handles resource conflicts
- Critical path analysis identifies bottlenecks

### Task Assignment Service
**Purpose:** Manages intelligent task assignment

```typescript
interface TaskAssignmentService {
  suggestAssignee(task: Task, team: Team): AssignmentSuggestion[];
  validateAssignment(task: Task, assignee: User): ValidationResult;
  balanceWorkload(team: Team, timeframe: TimePeriod): WorkloadReport;
  escalateOverdueTask(task: Task): EscalationAction;
  reassignTask(task: Task, newAssignee: User): void;
}
```

**Business Rules:**
- Assignment suggestions consider skills and availability
- Workload balancing prevents team member overload
- Escalation follows defined organizational hierarchy
- Reassignment preserves task history and comments
- Assignment validation checks permissions and capacity

### Notification Service
**Purpose:** Manages user notifications and communication

```typescript
interface NotificationService {
  sendNotification(notification: Notification): void;
  scheduleNotification(notification: Notification, when: Date): void;
  getUserPreferences(userId: UserId): NotificationPreferences;
  markAsRead(notificationId: NotificationId): void;
  generateDigest(userId: UserId, period: TimePeriod): NotificationDigest;
}
```

**Business Rules:**
- Notifications respect user preferences
- Scheduled notifications can be cancelled
- Digest generation aggregates related notifications
- Real-time notifications use WebSocket connections
- Email fallback for critical notifications

## Business Rules

### Tenant Management Rules
1. **Subdomain Uniqueness:** Each tenant must have a unique subdomain
2. **Data Isolation:** Tenant data must be completely isolated
3. **Plan Enforcement:** Feature access is controlled by subscription plan
4. **Billing Compliance:** Usage must be tracked for billing purposes
5. **Audit Trail:** All tenant changes must be logged

### User Management Rules
1. **Single Tenant Membership:** Users belong to exactly one tenant
2. **Email Uniqueness:** Email addresses are unique within a tenant
3. **Role Inheritance:** Users inherit permissions from their roles
4. **Activity Tracking:** User activity is logged for security
5. **Deactivation Handling:** Deactivated users retain data but lose access

### Project Management Rules
1. **Timeline Validation:** Project end date must be after start date
2. **Team Membership:** Project team members must be tenant users
3. **Status Workflows:** Project status changes follow defined workflows
4. **Budget Constraints:** Project spending cannot exceed allocated budget
5. **Archive Restrictions:** Completed projects become read-only

### Task Management Rules
1. **Project Association:** Tasks must belong to a project
2. **Dependency Validation:** Task dependencies cannot create cycles
3. **Assignment Rules:** Assigned users must be project team members
4. **Status Transitions:** Task status changes must follow workflow
5. **Time Tracking:** Time entries must be within task timeline

### Security Rules
1. **Tenant Isolation:** Users can only access their tenant's data
2. **Permission Checks:** All operations require permission validation
3. **Audit Logging:** Security events are logged and monitored
4. **Session Management:** User sessions have defined timeouts
5. **Data Encryption:** Sensitive data is encrypted at rest

## Domain Events

### Project Events
```typescript
interface ProjectCreated {
  projectId: ProjectId;
  tenantId: TenantId;
  createdBy: UserId;
  createdAt: Date;
}

interface ProjectStatusChanged {
  projectId: ProjectId;
  oldStatus: ProjectStatus;
  newStatus: ProjectStatus;
  changedBy: UserId;
  changedAt: Date;
}

interface ProjectCompleted {
  projectId: ProjectId;
  completedAt: Date;
  completedBy: UserId;
  finalMetrics: ProjectMetrics;
}
```

### Task Events
```typescript
interface TaskAssigned {
  taskId: TaskId;
  assigneeId: UserId;
  assignedBy: UserId;
  assignedAt: Date;
}

interface TaskCompleted {
  taskId: TaskId;
  completedBy: UserId;
  completedAt: Date;
  actualEffort: number;
}

interface TaskOverdue {
  taskId: TaskId;
  dueDate: Date;
  overdueBy: number;
  assigneeId: UserId;
}
```

### User Events
```typescript
interface UserJoinedTenant {
  userId: UserId;
  tenantId: TenantId;
  invitedBy: UserId;
  joinedAt: Date;
}

interface UserRoleChanged {
  userId: UserId;
  oldRole: UserRole;
  newRole: UserRole;
  changedBy: UserId;
  changedAt: Date;
}
```

## Aggregates

### Project Aggregate
**Root Entity:** Project  
**Child Entities:** Task, Milestone, Budget, ProjectMember  
**Invariants:**
- Project timeline must be consistent with task timelines
- Budget spending cannot exceed allocated amount
- Team members must have valid roles
- Milestone completion requires all tasks to be done

### User Aggregate
**Root Entity:** User  
**Child Entities:** UserPreferences, UserSession, UserNotification  
**Invariants:**
- User email must be unique within tenant
- User preferences must be valid
- Active sessions must not exceed limit
- Notification preferences must be consistent

### Team Aggregate
**Root Entity:** Team  
**Child Entities:** TeamMember, TeamSkill, TeamCapacity  
**Invariants:**
- Team must have at least one member
- Team lead must be a team member
- Member capacity cannot exceed 100%
- Skills must be from approved list

## Bounded Contexts

### Project Management Context
**Entities:** Project, Task, Milestone, Timeline  
**Services:** ProjectPlanningService, TaskAssignmentService  
**Repository:** ProjectRepository, TaskRepository

### User Management Context
**Entities:** User, Team, Role, Permission  
**Services:** UserService, AuthenticationService  
**Repository:** UserRepository, TeamRepository

### Tenant Management Context
**Entities:** Tenant, Subscription, Usage  
**Services:** TenantService, BillingService  
**Repository:** TenantRepository, SubscriptionRepository

### Notification Context
**Entities:** Notification, NotificationTemplate  
**Services:** NotificationService, EmailService  
**Repository:** NotificationRepository

## Domain Specifications

### Task Assignment Specification
```typescript
class TaskAssignmentSpecification {
  isSatisfiedBy(task: Task, assignee: User): boolean {
    return this.isTeamMember(task.projectId, assignee.id) &&
           this.hasRequiredSkills(task.requiredSkills, assignee.skills) &&
           this.hasCapacity(assignee.id, task.estimatedEffort);
  }
}
```

### Project Completion Specification
```typescript
class ProjectCompletionSpecification {
  isSatisfiedBy(project: Project): boolean {
    return this.allTasksCompleted(project.id) &&
           this.allMilestonesReached(project.id) &&
           this.budgetReconciled(project.id);
  }
}
```

### Budget Validation Specification
```typescript
class BudgetValidationSpecification {
  isSatisfiedBy(project: Project, expense: Expense): boolean {
    return this.withinBudgetLimit(project.budget, expense) &&
           this.approvedByAuthorizer(expense) &&
           this.validExpenseCategory(expense.category);
  }
}
```

---

**Document Version:** 1.0  
**Last Updated:** 2024-01-01  
**Domain Expert:** Product Team Lead  
**Next Review:** 2024-02-01