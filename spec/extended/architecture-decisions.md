# Architecture Decision Records (ADRs)

## Overview

This document contains Architecture Decision Records (ADRs) for [PROJECT NAME]. ADRs capture important architectural decisions, the context in which they were made, and their consequences.

**Project:** [PROJECT NAME]  
**ADR Format:** [Michael Nygard format / MADR / Custom]  
**Decision Authority:** [Who can make architectural decisions]

## ADR Index

| ADR | Date | Status | Title |
|-----|------|--------|-------|
| [ADR-001](#adr-001) | [YYYY-MM-DD] | Accepted | [Decision Title] |
| [ADR-002](#adr-002) | [YYYY-MM-DD] | Accepted | [Decision Title] |
| [ADR-003](#adr-003) | [YYYY-MM-DD] | Superseded | [Decision Title] |

---

## ADR-001: [Decision Title]

**Date:** [YYYY-MM-DD]  
**Status:** Proposed | Accepted | Superseded | Deprecated  
**Supersedes:** [Previous ADR if applicable]  
**Superseded by:** [Later ADR if applicable]

### Context

[Describe the forces at play, including technological, political, social, and project forces. This section should be value-neutral and simply describe what is happening.]

**Problem Statement:**
[What problem are we trying to solve?]

**Key Stakeholders:**
- [Stakeholder 1 and their concerns]
- [Stakeholder 2 and their concerns]

**Constraints:**
- [Constraint 1: Description]
- [Constraint 2: Description]

**Assumptions:**
- [Assumption 1: Description]
- [Assumption 2: Description]

### Decision

[Describe the decision and the rationale behind it. This is the "we will" section.]

**We will:** [Clear statement of the decision]

**Because:** [Primary rationale for the decision]

### Alternatives Considered

#### Option 1: [Alternative Name]

**Description:** [What this option would involve]

**Pros:**
- [Pro 1]
- [Pro 2]

**Cons:**
- [Con 1]
- [Con 2]

**Why Rejected:** [Reason this option was not chosen]

#### Option 2: [Alternative Name]

**Description:** [What this option would involve]

**Pros:**
- [Pro 1]
- [Pro 2]

**Cons:**
- [Con 1]
- [Con 2]

**Why Rejected:** [Reason this option was not chosen]

#### Option 3: [Alternative Name]

**Description:** [What this option would involve]

**Pros:**
- [Pro 1]
- [Pro 2]

**Cons:**
- [Con 1]
- [Con 2]

**Why Rejected:** [Reason this option was not chosen]

### Consequences

**Positive Consequences:**
- [Positive consequence 1]
- [Positive consequence 2]

**Negative Consequences:**
- [Negative consequence 1]
- [Negative consequence 2]

**Trade-offs:**
- [Trade-off 1: What we gain vs. what we lose]
- [Trade-off 2: What we gain vs. what we lose]

**Risks:**
- [Risk 1 and mitigation strategy]
- [Risk 2 and mitigation strategy]

**Impact on:**
- **Development Team:** [How this affects the team]
- **Operations:** [How this affects operations]
- **Users:** [How this affects end users]
- **Performance:** [Performance implications]
- **Security:** [Security implications]
- **Maintenance:** [Maintenance implications]

### Implementation

**Action Items:**
- [ ] [Action item 1]
- [ ] [Action item 2]
- [ ] [Action item 3]

**Timeline:**
- [Phase 1: Description and timeline]
- [Phase 2: Description and timeline]

**Success Metrics:**
- [Metric 1: How we'll measure success]
- [Metric 2: How we'll measure success]

**Review Date:** [When this decision should be reviewed]

---

## ADR-002: [Decision Title]

**Date:** [YYYY-MM-DD]  
**Status:** Accepted  
**Supersedes:** [Previous ADR if applicable]

### Context

[Describe the situation and context for this decision]

**Problem Statement:**
[What problem are we trying to solve?]

**Key Stakeholders:**
- [Stakeholder 1 and their concerns]
- [Stakeholder 2 and their concerns]

**Constraints:**
- [Constraint 1: Description]
- [Constraint 2: Description]

### Decision

**We will:** [Clear statement of the decision]

**Because:** [Primary rationale for the decision]

### Alternatives Considered

#### Option 1: [Alternative Name]

**Description:** [What this option would involve]

**Pros:**
- [Pro 1]
- [Pro 2]

**Cons:**
- [Con 1]
- [Con 2]

**Why Rejected:** [Reason this option was not chosen]

### Consequences

**Positive Consequences:**
- [Positive consequence 1]
- [Positive consequence 2]

**Negative Consequences:**
- [Negative consequence 1]
- [Negative consequence 2]

**Trade-offs:**
- [Trade-off 1: What we gain vs. what we lose]
- [Trade-off 2: What we gain vs. what we lose]

### Implementation

**Action Items:**
- [ ] [Action item 1]
- [ ] [Action item 2]

**Timeline:**
- [Phase 1: Description and timeline]

**Success Metrics:**
- [Metric 1: How we'll measure success]

**Review Date:** [When this decision should be reviewed]

---

## ADR-003: [Decision Title] (Superseded)

**Date:** [YYYY-MM-DD]  
**Status:** Superseded  
**Superseded by:** [ADR-XXX: Title]

### Context

[Original context for this decision]

### Decision

**We will:** [Original decision]

### Why This Decision Was Superseded

**Date Superseded:** [YYYY-MM-DD]  
**Reason:** [Why this decision was changed]  
**New Decision:** [Reference to superseding ADR]

**What Changed:**
- [Change 1: What aspect of the context changed]
- [Change 2: What aspect of the context changed]

**Migration Strategy:**
- [Step 1: How to migrate from old to new decision]
- [Step 2: How to migrate from old to new decision]

---

## ADR Template

Use this template when creating new ADRs:

```markdown
## ADR-XXX: [Decision Title]

**Date:** [YYYY-MM-DD]  
**Status:** Proposed  
**Supersedes:** [Previous ADR if applicable]

### Context

[Describe the forces at play and the problem to be solved]

**Problem Statement:**
[What problem are we trying to solve?]

**Key Stakeholders:**
- [Stakeholder 1 and their concerns]

**Constraints:**
- [Constraint 1: Description]

**Assumptions:**
- [Assumption 1: Description]

### Decision

**We will:** [Clear statement of the decision]

**Because:** [Primary rationale for the decision]

### Alternatives Considered

#### Option 1: [Alternative Name]

**Description:** [What this option would involve]

**Pros:**
- [Pro 1]

**Cons:**
- [Con 1]

**Why Rejected:** [Reason this option was not chosen]

### Consequences

**Positive Consequences:**
- [Positive consequence 1]

**Negative Consequences:**
- [Negative consequence 1]

**Trade-offs:**
- [Trade-off 1: What we gain vs. what we lose]

**Risks:**
- [Risk 1 and mitigation strategy]

### Implementation

**Action Items:**
- [ ] [Action item 1]

**Timeline:**
- [Phase 1: Description and timeline]

**Success Metrics:**
- [Metric 1: How we'll measure success]

**Review Date:** [When this decision should be reviewed]
```

## ADR Guidelines

### When to Create an ADR

Create an ADR when making decisions about:
- **Architecture patterns** (microservices vs. monolith)
- **Technology choices** (database, framework, language)
- **Integration approaches** (REST vs. GraphQL)
- **Security models** (authentication, authorization)
- **Deployment strategies** (cloud provider, CI/CD)
- **Data management** (storage, caching, synchronization)

### What NOT to Put in ADRs

- **Implementation details** (belongs in design documents)
- **Temporary decisions** (use for significant, lasting decisions)
- **Obvious choices** (don't document decisions with no alternatives)
- **Process decisions** (use for technical architecture decisions)

### ADR Lifecycle

1. **Proposed** - Initial draft, under discussion
2. **Accepted** - Decision has been made and approved
3. **Superseded** - Decision has been replaced by a newer ADR
4. **Deprecated** - Decision is no longer relevant

### Best Practices

#### Writing ADRs

1. **Be specific** - Clearly state the decision and rationale
2. **Include context** - Explain the situation that led to the decision
3. **Consider alternatives** - Show that other options were evaluated
4. **Document consequences** - Be honest about trade-offs and risks
5. **Keep it concise** - Focus on the essential information
6. **Use plain language** - Avoid jargon and technical acronyms

#### Reviewing ADRs

1. **Review context** - Ensure the problem is clearly stated
2. **Validate alternatives** - Check that reasonable options were considered
3. **Assess consequences** - Evaluate the trade-offs and risks
4. **Check implementation** - Ensure the decision is actionable
5. **Consider stakeholders** - Verify all affected parties are considered

### ADR Governance

**Decision Authority:**
- **Technical Lead:** Can approve technical ADRs
- **Architecture Committee:** Required for major architectural decisions
- **Product Owner:** Must approve ADRs affecting user experience

**Review Process:**
1. Create ADR in "Proposed" status
2. Share with relevant stakeholders
3. Incorporate feedback and discussion
4. Get approval from decision authority
5. Update status to "Accepted"
6. Implement the decision

**Maintenance:**
- Review ADRs quarterly for relevance
- Update status as decisions evolve
- Create new ADRs when superseding old ones
- Archive deprecated ADRs with clear reasoning

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**ADR Owner:** [Name and Role]  
**Next Review:** [Date]