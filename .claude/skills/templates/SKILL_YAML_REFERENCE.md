# YAML Frontmatter Reference for Skills

**Purpose:** Complete reference for all YAML frontmatter fields in SKILL.md files
**Last Updated:** October 19, 2025

---

## Complete YAML Specification

```yaml
---
name: string (REQUIRED)
description: string (REQUIRED)
allowed-tools: comma-separated list (OPTIONAL)
extends: string (OPTIONAL)
version: semver string (OPTIONAL)
author: string (OPTIONAL)
---
```

---

## Field Descriptions

### `name` (REQUIRED)

**Type:** String
**Purpose:** Human-readable display name for the skill
**Format:** Title Case
**Length:** 3-50 characters recommended

**Examples:**
```yaml
name: Phase Planning
name: Completion Report Generator
name: Database Integration Patterns
name: Pre-Deployment Checklist
```

**Best Practices:**
- ✅ Be specific and descriptive
- ✅ Use Title Case
- ✅ Avoid abbreviations
- ✅ Make it self-explanatory
- ❌ Don't be vague ("Helper", "Utility")
- ❌ Don't use jargon without context

**Generic vs Project-Specific Naming:**

Generic:
```yaml
name: Generic Phase Planning
name: Database Integration
name: Cloud Deployment
```

Project-Specific:
```yaml
name: CA Lobby Phase Planning
name: CA Lobby BigQuery Integration
name: CA Lobby Vercel Deployment
```

---

### `description` (REQUIRED)

**Type:** String
**Purpose:** CRITICAL for skill discovery - describes what skill does and when to activate
**Format:** 2-3 sentences
**Length:** 100-300 characters recommended

**Formula:**
```
[Action] for [Context]. Use when [Triggers]. [Ensures/Provides].
```

**Structure:**
1. **Sentence 1:** Primary action + context
2. **Sentence 2:** Trigger conditions (when to activate)
3. **Sentence 3:** What it ensures or provides

**Examples:**

**✅ GOOD Descriptions:**

```yaml
description: Enforces phase planning protocol for CA Lobby project. Use when user requests to start a new phase, create implementation plan, or begin major feature development. Ensures master plan consultation and proper documentation structure.
```
- ✅ Primary action: "Enforces phase planning protocol"
- ✅ Context: "for CA Lobby project"
- ✅ Triggers: "start a new phase, create implementation plan, begin major feature"
- ✅ Ensures: "master plan consultation and proper documentation"

```yaml
description: Database integration patterns for SQL and NoSQL databases. Use when working with database schemas, queries, migrations, or ORM configurations. Provides patterns for PostgreSQL, BigQuery, MongoDB, and MySQL.
```
- ✅ Primary action: "Database integration patterns"
- ✅ Context: "SQL and NoSQL databases"
- ✅ Triggers: "schemas, queries, migrations, ORM configurations"
- ✅ Provides: "patterns for PostgreSQL, BigQuery, MongoDB, MySQL"

```yaml
description: Pre-deployment verification checklist for cloud platforms. Use before deploying to production or when user says "deploy" or "ready to deploy". Ensures tests pass, build succeeds, and environment correctly configured.
```
- ✅ Primary action: "Pre-deployment verification checklist"
- ✅ Context: "cloud platforms"
- ✅ Triggers: "before deploying", "says deploy", "ready to deploy"
- ✅ Ensures: "tests pass, build succeeds, environment configured"

---

**❌ BAD Descriptions:**

```yaml
description: Helps with planning
```
- ❌ Too vague
- ❌ No context
- ❌ No triggers
- ❌ Unclear what it ensures

```yaml
description: Database stuff
```
- ❌ Unprofessional
- ❌ No specifics
- ❌ Won't match user queries

```yaml
description: Deployment
```
- ❌ Too short
- ❌ No context or triggers
- ❌ Won't activate properly

---

**Description Optimization Checklist:**

- [ ] Starts with primary action (first 5 words)
- [ ] Specifies context (project, technology, domain)
- [ ] Lists 3+ trigger phrases or keywords
- [ ] Explains what skill ensures or provides
- [ ] Uses keywords users will naturally say
- [ ] Is 2-3 sentences (not too short or long)
- [ ] Avoids jargon or unexplained abbreviations
- [ ] Differentiates from similar skills
- [ ] Front-loads important terms

---

**Keyword Strategy:**

Include keywords for:
1. **Action verbs:** "enforce", "generate", "verify", "deploy"
2. **Domain terms:** "database", "phase", "deployment", "testing"
3. **User phrases:** "start phase", "create report", "run tests"
4. **Technology names:** "BigQuery", "Vercel", "PostgreSQL"

---

### `allowed-tools` (OPTIONAL)

**Type:** Comma-separated list of tool names
**Purpose:** Restrict which tools Claude can use when this skill is active
**Default:** If omitted, all tools available

**Available Tools:**
- `Read` - Read files
- `Write` - Write new files
- `Edit` - Edit existing files
- `Bash` - Execute bash commands
- `Glob` - Find files by pattern
- `Grep` - Search file contents
- `WebFetch` - Fetch web content
- `Task` - Launch agents

**Format:**
```yaml
allowed-tools: Tool1, Tool2, Tool3
```

**Common Patterns:**

**Read-Only (Research, Planning, Review):**
```yaml
allowed-tools: Read, Glob, Grep
```
Use for:
- Code review
- Research
- Planning (before implementation)
- Analysis

**Read + Write (Report Generation, New Files):**
```yaml
allowed-tools: Read, Write
```
Use for:
- Report generation
- Documentation creation
- New file creation
- Template population

**Read + Edit (Code Modification):**
```yaml
allowed-tools: Read, Edit
```
Use for:
- Refactoring
- Code updates
- Configuration changes
- Existing file modifications

**Read + Bash (Verification, Testing):**
```yaml
allowed-tools: Read, Bash, Glob
```
Use for:
- Running tests
- Deployment verification
- Build processes
- Command execution

**Full Access (Default):**
```yaml
# No allowed-tools field = all tools available
```
Use for:
- Complex implementation
- Full workflows
- Deployment execution

---

**Examples:**

```yaml
---
name: Code Review Checklist
description: Review code against project standards
allowed-tools: Read, Grep, Glob
---
# Can only read and search, cannot modify
```

```yaml
---
name: Phase Planning
description: Create phase plans from templates
allowed-tools: Read, Glob, WebFetch
---
# Read-only during planning, can research but not modify code
```

```yaml
---
name: Completion Report Generator
description: Generate completion reports
allowed-tools: Read, Write
---
# Can read templates and write reports, but not edit existing files
```

```yaml
---
name: Deployment Workflow
description: Deploy application to production
# No allowed-tools = full access
---
# Needs all tools for complex deployment
```

---

### `extends` (OPTIONAL)

**Type:** String (path to generic skill)
**Purpose:** Inherit from a generic skill
**Format:** `generic-skills/skill-name`
**When to Use:** Project-specific skills extending generic base

**Examples:**

```yaml
---
name: CA Lobby Phase Planning
description: CA Lobby phase planning protocol
extends: generic-skills/phase-planning
---
```

```yaml
---
name: CA Lobby Database Integration
description: BigQuery integration for CA Lobby
extends: generic-skills/database-integration
---
```

**How It Works:**

1. Generic skill provides base structure
2. Project skill adds/overrides specifics
3. Combined behavior = generic + project

**Generic Skill:**
```yaml
---
name: Generic Phase Planning
description: Template-based phase planning for any project
---

# Generic Phase Planning

Configuration Required:
- ${PROJECT_MASTER_PLAN_PATH}
- ${PROJECT_DOCS_PATH}

Steps:
1. Read master plan from ${PROJECT_MASTER_PLAN_PATH}
2. Create plan in ${PROJECT_DOCS_PATH}
...
```

**Project Skill (Extends Generic):**
```yaml
---
name: CA Lobby Phase Planning
description: CA Lobby phase planning
extends: generic-skills/phase-planning
---

# CA Lobby Phase Planning

Configuration:
- PROJECT_MASTER_PLAN_PATH: Documentation/General/MASTER_PROJECT_PLAN.md
- PROJECT_DOCS_PATH: Documentation/PhaseX/Plans/

Additional Steps:
4. Verify demo data considerations
5. Check Vercel deployment impact
...
```

**Result:** Generic steps 1-3 + CA Lobby steps 4-5 = Complete workflow

---

### `version` (OPTIONAL)

**Type:** Semantic version string
**Purpose:** Track skill versions for changes and compatibility
**Format:** `MAJOR.MINOR.PATCH`
**Default:** If omitted, assumed to be 1.0.0

**Semantic Versioning:**
```
MAJOR.MINOR.PATCH

1.0.0 → Initial release
1.0.1 → Bug fix (backwards compatible)
1.1.0 → New feature (backwards compatible)
2.0.0 → Breaking change (not backwards compatible)
```

**Examples:**

```yaml
version: 1.0.0  # Initial release
version: 1.0.1  # Bug fix
version: 1.1.0  # New feature added
version: 2.0.0  # Breaking change
```

**When to Increment:**

**MAJOR (X.0.0):**
- Breaking changes
- Incompatible with previous version
- Requires user changes to use

**MINOR (1.X.0):**
- New features
- Backwards compatible
- Optional enhancements

**PATCH (1.0.X):**
- Bug fixes
- No functionality changes
- Backwards compatible

**Examples:**

```yaml
# Version 1.0.0 → 1.0.1 (bug fix)
# Fixed: Template path resolution on Windows
version: 1.0.1
```

```yaml
# Version 1.0.1 → 1.1.0 (new feature)
# Added: Support for PostgreSQL in addition to BigQuery
version: 1.1.0
```

```yaml
# Version 1.1.0 → 2.0.0 (breaking change)
# Changed: Renamed ${MASTER_PLAN} to ${PROJECT_MASTER_PLAN_PATH}
#          Users must update project configuration
version: 2.0.0
```

---

### `author` (OPTIONAL)

**Type:** String
**Purpose:** Attribution and contact
**Format:** Free-form string
**Default:** If omitted, no specific author

**Examples:**

```yaml
author: John Doe
author: CA Lobby Team
author: DevOps Team
author: john.doe@example.com
```

**Use Cases:**
- Team attribution
- Contact for questions
- Skill ownership
- Organizational tracking

---

## Complete Examples

### Example 1: Simple Read-Only Skill

```yaml
---
name: Environment Variables Checker
description: Verify environment variables are configured. Use when setting up project or before deployment. Ensures all required env vars present.
allowed-tools: Read, Bash
version: 1.0.0
author: DevOps Team
---
```

---

### Example 2: Template-Based Generic Skill

```yaml
---
name: Generic Completion Report
description: Generate completion reports from template for any project. Use when phase complete or milestone reached. Provides standardized reporting structure.
allowed-tools: Read, Write
version: 1.2.0
---
```

---

### Example 3: Project-Specific Skill Extending Generic

```yaml
---
name: CA Lobby Phase Planning
description: Enforce CA Lobby phase planning protocol. Use when starting new CA Lobby phases or planning implementations. Ensures master plan consultation and proper documentation.
allowed-tools: Read, Glob, WebFetch
extends: generic-skills/phase-planning
version: 2.0.0
author: CA Lobby Team
---
```

---

### Example 4: Complex Full-Access Skill

```yaml
---
name: Multi-Environment Deployment
description: Deploy application across staging and production environments. Use when user says "deploy" or "push to production". Ensures verification at each stage with rollback capability.
version: 1.5.0
author: Deployment Team
---
# No allowed-tools = full access needed for deployment
```

---

## Quick Reference

| Field | Required | Type | Purpose |
|-------|----------|------|---------|
| `name` | ✅ Yes | String | Display name |
| `description` | ✅ Yes | String | Discovery & activation |
| `allowed-tools` | ❌ No | List | Tool restrictions |
| `extends` | ❌ No | String | Inherit from generic |
| `version` | ❌ No | Semver | Version tracking |
| `author` | ❌ No | String | Attribution |

---

## Validation Checklist

Before deploying a skill, verify YAML:

- [ ] `name` is clear and descriptive
- [ ] `description` is optimized for discovery
  - [ ] Includes primary action
  - [ ] Specifies context
  - [ ] Lists 3+ triggers
  - [ ] Explains what it ensures
- [ ] `allowed-tools` appropriate for skill type
  - [ ] Read-only for planning/review
  - [ ] Limited for specific operations
  - [ ] Full access only when needed
- [ ] `extends` points to valid generic skill (if used)
- [ ] `version` follows semver
- [ ] YAML syntax valid (no syntax errors)

---

**Reference Version:** 1.0
**Last Updated:** October 19, 2025
**Maintained By:** Master-Files-Toolkit Team
