# Skills Implementation Guide

**Document Version:** 1.0
**Last Updated:** October 19, 2025
**Purpose:** Step-by-step guide for creating and implementing Claude Skills
**Audience:** Developers implementing skills for their projects

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Design Phase](#design-phase)
3. [Implementation Phase](#implementation-phase)
4. [Testing Phase](#testing-phase)
5. [Deployment Phase](#deployment-phase)
6. [Maintenance Phase](#maintenance-phase)
7. [Generic vs Project-Specific Decision Tree](#generic-vs-project-specific-decision-tree)
8. [Skill Composition Patterns](#skill-composition-patterns)
9. [Advanced Techniques](#advanced-techniques)
10. [Common Pitfalls](#common-pitfalls)
11. [Examples & Templates](#examples--templates)

---

## Quick Start

### 5-Minute Skill Creation

**Goal:** Create a functional skill in 5 minutes

**Step 1:** Create directory (30 seconds)
```bash
# For generic skill
mkdir -p ~/.claude/master-files/skills/generic-skills/my-skill

# For project-specific skill
mkdir -p .claude/skills/my-skill
```

**Step 2:** Create SKILL.md (3 minutes)
```bash
cd my-skill
cat > SKILL.md << 'EOF'
---
name: My Skill Name
description: What this skill does. Use when user requests [trigger]. Ensures [outcome].
---

# My Skill Name

## Purpose
Brief description of what this skill accomplishes

## When This Activates
- User says [trigger phrase 1]
- User requests [trigger phrase 2]
- Context matches [condition]

## Steps
1. First step
2. Second step
3. Third step

## Output
What this skill produces
EOF
```

**Step 3:** Test (1.5 minutes)
```
Open Claude Code session
Say: [trigger phrase from description]
Verify: Skill activates and follows steps
```

**Done!** You now have a working skill.

---

## Design Phase

### Step 1: Define Purpose

**Questions to Answer:**

1. **What problem does this skill solve?**
   - Example: "Ensures completion reports are never skipped"

2. **What makes this a skill vs an agent?**
   - Skill: Protocol enforcement, templates, checklists
   - Agent: Complex implementation, research, autonomous work

3. **Is this generic or project-specific?**
   - Generic: Reusable across projects
   - Project: Specific to one project's needs

4. **What should trigger this skill?**
   - Specific phrases users will say
   - Contextual conditions

5. **What tools does it need?**
   - Read-only: `Read, Grep, Glob`
   - Write-only: `Write`
   - Edit: `Read, Edit`
   - Full: No restriction

---

### Step 2: Map the Workflow

**Template:**

```
INPUT:
- User says: [trigger phrase]
- Context: [condition]

PROCESS:
1. [Step 1]
2. [Step 2]
3. [Step 3]
...

OUTPUT:
- [What gets produced]
- [What gets updated]
- [Next step/skill triggered]

VALIDATION:
- [How to verify success]
```

**Example: Completion Report Skill**

```
INPUT:
- User says: "Phase complete", "create completion report"
- Context: Working in project with phase documentation

PROCESS:
1. Read templates/completion-report-template.md
2. Gather phase information:
   - Phase name
   - Deliverables achieved
   - Files modified
   - Metrics
3. Populate template with data
4. Write report to Documentation/PhaseX/Reports/
5. Trigger master-plan-update skill

OUTPUT:
- Completion report file created
- Master plan updated
- User notified of next steps

VALIDATION:
- Report file exists at correct path
- All 12 sections present
- Master plan references new report
```

---

### Step 3: Design File Structure

**Minimal Structure:**
```
skill-name/
└── SKILL.md
```

**Recommended Structure:**
```
skill-name/
├── SKILL.md                    # Main skill
├── README.md                   # Documentation
├── templates/                  # Template files
│   ├── template-1.md
│   └── template-2.md
├── checklists/                 # Checklists
│   ├── pre-checklist.md
│   └── post-checklist.md
├── reference/                  # Reference docs
│   ├── patterns.md
│   └── examples.md
└── config/                     # Configuration
    └── defaults.md
```

**Decision Guide:**

| Need | Add This |
|------|----------|
| Template-based generation | `templates/` directory |
| Step-by-step checklist | `checklists/` directory |
| Large reference material | `reference/` directory |
| Configurable defaults | `config/` directory |
| Multiple examples | `examples/` directory |

---

### Step 4: Write Description (CRITICAL)

**Description Formula:**

```yaml
description: [Primary Action] for [Specific Context]. Use when [Trigger Conditions]. [What it ensures/provides].
```

**Examples:**

**❌ Bad Descriptions:**
```yaml
description: Helps with planning
description: Database stuff
description: Deployment
```

**✅ Good Descriptions:**
```yaml
description: Enforces phase planning protocol for CA Lobby project. Use when user requests to start a new phase, create implementation plan, or begin major feature development. Ensures master plan consultation and proper documentation structure.

description: Database integration patterns for SQL and NoSQL databases. Use when working with database schemas, queries, or ORM configurations. Provides patterns for PostgreSQL, BigQuery, MongoDB, and migration strategies.

description: Pre-deployment verification checklist for cloud platforms. Use before deploying to production or when user says "deploy" or "ready to deploy". Ensures tests pass, build succeeds, and environment configured correctly.
```

**Description Optimization Checklist:**

- [ ] Front-loads primary action (first 5 words)
- [ ] Specifies context (project, technology, domain)
- [ ] Lists 3+ trigger phrases
- [ ] Explains what it ensures/prevents
- [ ] Contains keywords user will naturally say
- [ ] Is 2-3 sentences (not too short, not too long)
- [ ] Avoids jargon or abbreviations
- [ ] Clearly differentiates from similar skills

---

## Implementation Phase

### Step 1: Create SKILL.md

**Template:**

```yaml
---
name: Skill Display Name
description: [Optimized description from design phase]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, WebFetch, Task
extends: generic-skills/base-skill  # If extending
version: 1.0.0
---

# Skill Display Name

## Purpose
[What this skill accomplishes]

## When This Activates
[Trigger conditions]
- User says "[phrase 1]"
- User requests "[phrase 2]"
- Context matches [condition]

## Prerequisites
[What must exist before this skill runs]
- [ ] Prerequisite 1
- [ ] Prerequisite 2

## Steps
1. [Step 1 description]
   - Substep if needed
   - Substep if needed

2. [Step 2 description]

3. [Step 3 description]

## Output
[What this skill produces]

## Error Handling
[What happens if something fails]

## Integration Points
[How this integrates with other skills/agents]
- May invoke: [other-skill-name]
- Triggered by: [upstream-skill-name]
- Triggers: [downstream-skill-name]

## Examples
[Usage examples if helpful]

## Changelog
### Version 1.0.0 (YYYY-MM-DD)
- Initial release
```

---

### Step 2: Create Supporting Files

#### Templates

**When to create:**
- Skill generates files from templates
- Need consistent output format

**Template Pattern:**
```markdown
# {{PLACEHOLDER_NAME}}

## Section 1
{{PLACEHOLDER_DATA_1}}

## Section 2
{{PLACEHOLDER_DATA_2}}

...
```

**Example: Phase Plan Template**

**File:** `templates/phase-plan-template.md`
```markdown
# {{PHASE_NAME}} - Implementation Plan

**Phase:** {{PHASE_NUMBER}}
**Duration:** {{DURATION}}
**Status:** 🔄 IN PROGRESS

## Executive Summary
{{EXECUTIVE_SUMMARY}}

## Objectives
{{OBJECTIVES}}

## Prerequisites
{{PREREQUISITES}}

## Deliverables
{{DELIVERABLES}}

## Success Criteria
{{SUCCESS_CRITERIA}}

## Micro Save Points
{{SAVE_POINTS}}

## Risks
{{RISKS}}

## Timeline
{{TIMELINE}}
```

---

#### Checklists

**When to create:**
- Skill enforces step-by-step verification
- Need consistent process compliance

**Checklist Pattern:**
```markdown
# Checklist Name

## Pre-Checklist (Before Main Process)
- [ ] Item 1
- [ ] Item 2
- [ ] Item 3

## Main Checklist (During Process)
- [ ] Item 1
- [ ] Item 2
- [ ] Item 3

## Post-Checklist (After Process)
- [ ] Item 1
- [ ] Item 2
- [ ] Item 3

## Validation
- [ ] All items completed
- [ ] No errors encountered
- [ ] Output verified
```

**Example: Deployment Checklist**

**File:** `checklists/pre-deployment-checklist.md`
```markdown
# Pre-Deployment Checklist

## Code Quality
- [ ] All tests passing (`npm test`)
- [ ] Build succeeds (`npm run build`)
- [ ] No console.log in production code
- [ ] No commented-out code
- [ ] ESLint/Prettier checks pass

## Configuration
- [ ] Environment variables configured
- [ ] API keys valid
- [ ] Database connection strings correct
- [ ] Feature flags set appropriately

## Dependencies
- [ ] Dependencies up to date
- [ ] No critical security vulnerabilities
- [ ] Audit clean (`npm audit`)

## Documentation
- [ ] CHANGELOG updated
- [ ] README current
- [ ] API docs generated

## Verification
- [ ] All checklist items completed
- [ ] Ready to deploy: GO / NO-GO
```

---

#### Reference Files

**When to create:**
- Large pattern libraries
- Extensive examples
- Detailed technical references
- Context would be too large for SKILL.md

**Reference Pattern:**
```markdown
# Reference Title

## Overview
Brief description

## Pattern 1
Detailed information about pattern 1

## Pattern 2
Detailed information about pattern 2

...

## Usage
How to use these patterns
```

**Example: Database Patterns Reference**

**File:** `reference/database-patterns.md`
```markdown
# Database Integration Patterns

## Overview
Common patterns for database integration across different database types

## PostgreSQL Patterns

### Connection Pattern
\`\`\`javascript
const { Pool } = require('pg');
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 20,
  idleTimeoutMillis: 30000
});
\`\`\`

### Query Pattern
\`\`\`javascript
async function query(text, params) {
  const client = await pool.connect();
  try {
    const result = await client.query(text, params);
    return result.rows;
  } finally {
    client.release();
  }
}
\`\`\`

## BigQuery Patterns

### Connection Pattern
\`\`\`python
from google.cloud import bigquery
client = bigquery.Client()
\`\`\`

### Query Pattern
\`\`\`python
def query_table(query_string):
    query_job = client.query(query_string)
    results = query_job.result()
    return [dict(row) for row in results]
\`\`\`

## MongoDB Patterns
[...]
```

---

### Step 3: Implement Progressive Disclosure

**Principle:** Load supporting files only when needed

**SKILL.md Pattern:**
```markdown
# Database Integration Skill

## Step 1: Determine Database Type
Ask user or detect from config:
- PostgreSQL
- BigQuery
- MongoDB
- MySQL

## Step 2: Load Appropriate Pattern
Based on database type:
- PostgreSQL → [reference/postgres-patterns.md](reference/postgres-patterns.md)
- BigQuery → [reference/bigquery-patterns.md](reference/bigquery-patterns.md)
- MongoDB → [reference/mongo-patterns.md](reference/mongo-patterns.md)

## Step 3: Apply Pattern
Use loaded pattern file for implementation guidance
```

**Benefits:**
- Doesn't load all patterns upfront
- Only loads what's needed
- Saves context tokens
- Faster activation

---

### Step 4: Configure Tool Restrictions

**Decision Matrix:**

| Skill Type | allowed-tools | Reason |
|------------|---------------|--------|
| Planning | `Read, Glob, WebFetch` | Read-only, research |
| Review | `Read, Grep, Glob` | Read-only, analysis |
| Generation (new files) | `Read, Write` | Can create but not edit |
| Modification | `Read, Edit` | Can edit existing |
| Deployment | `Read, Bash` | Can execute commands |
| Full Implementation | (no restriction) | Needs all tools |

**Implementation:**

```yaml
---
name: Code Review Checklist
allowed-tools: Read, Grep, Glob
---
# This skill can only read and search, not modify
```

```yaml
---
name: Report Generation
allowed-tools: Read, Write
---
# This skill can read templates and write reports
```

```yaml
---
name: Complex Implementation
# No allowed-tools = full access
---
# This skill can use any tool
```

---

### Step 5: Implement Inheritance (If Extending)

**Pattern:**

**Generic Skill:** `~/.claude/master-files/skills/generic-skills/phase-planning/SKILL.md`
```yaml
---
name: Generic Phase Planning
description: Template-based phase planning for any project
---

# Generic Phase Planning

## Configuration Required
Projects must provide:
- ${PROJECT_MASTER_PLAN_PATH}
- ${PROJECT_DOCS_PATH}
- ${PROJECT_PHASE_FORMAT}

## Standard Sections
1. Executive Summary
2. Objectives
3. Deliverables
...

## Steps
1. Read project configuration
2. Load project-specific skill if exists
3. Use project paths
4. Generate plan
```

**Project Skill:** `.claude/skills/phase-planning/SKILL.md`
```yaml
---
name: CA Lobby Phase Planning
description: CA Lobby phase planning following master project plan
extends: generic-skills/phase-planning
---

# CA Lobby Phase Planning

## Project Configuration
- PROJECT_MASTER_PLAN_PATH: Documentation/General/MASTER_PROJECT_PLAN.md
- PROJECT_DOCS_PATH: Documentation/PhaseX/Plans/
- PROJECT_PHASE_FORMAT: PHASE_[X]_[NAME]_PLAN.md

## Additional Sections (Beyond Generic)
9. Demo Data Considerations
10. Vercel Deployment Impact
...

## CA Lobby Specific Steps
1. Read Documentation/General/MASTER_PROJECT_PLAN.md
2. Verify previous completion report
3. [Use generic steps with CA Lobby paths]
```

**Resolution:** Generic base + Project overrides = Final behavior

---

## Testing Phase

### Test Plan Template

**File:** `TEST_PLAN.md` (create alongside skill)

```markdown
# Skill Test Plan: [Skill Name]

## Test 1: Activation
**Objective:** Verify skill activates on expected triggers

**Steps:**
1. Open Claude Code
2. Say: "[trigger phrase 1]"
3. Verify skill activates

**Expected:** Skill loads and begins execution

**Actual:** [Record results]

---

## Test 2: Tool Restrictions
**Objective:** Verify allowed-tools enforcement

**Steps:**
1. Activate skill
2. Attempt restricted tool use
3. Verify restriction enforced

**Expected:** Restricted tool blocked with error

**Actual:** [Record results]

---

## Test 3: Supporting Files
**Objective:** Verify templates/checklists load

**Steps:**
1. Activate skill
2. Trigger template/checklist usage
3. Verify files load correctly

**Expected:** Files accessible and used correctly

**Actual:** [Record results]

---

## Test 4: Output Format
**Objective:** Verify output matches specification

**Steps:**
1. Execute skill end-to-end
2. Review output format
3. Check all sections present

**Expected:** Output follows template/specification

**Actual:** [Record results]

---

## Test 5: Error Handling
**Objective:** Verify graceful error handling

**Steps:**
1. Trigger skill with missing prerequisites
2. Observe error handling
3. Verify helpful error message

**Expected:** Clear error message, no crash

**Actual:** [Record results]

---

## Test 6: Integration
**Objective:** Verify integration with other skills/agents

**Steps:**
1. Activate skill
2. Verify it invokes expected downstream skills/agents
3. Verify data passed correctly

**Expected:** Integration works smoothly

**Actual:** [Record results]
```

---

### Testing Checklist

- [ ] **Activation Testing**
  - [ ] Activates on expected trigger phrases
  - [ ] Doesn't activate on unrelated requests
  - [ ] Description matching works correctly

- [ ] **Tool Restriction Testing**
  - [ ] `allowed-tools` restrictions enforced
  - [ ] Helpful error when restricted tool attempted
  - [ ] Unrestricted tools work normally

- [ ] **Supporting Files Testing**
  - [ ] Templates load correctly
  - [ ] Checklists accessible
  - [ ] Reference docs load on demand
  - [ ] File paths resolve correctly

- [ ] **Output Testing**
  - [ ] Output format correct
  - [ ] All required sections present
  - [ ] Placeholders replaced
  - [ ] Files written to correct locations

- [ ] **Error Handling Testing**
  - [ ] Missing prerequisites detected
  - [ ] Clear error messages
  - [ ] Graceful degradation
  - [ ] No crashes

- [ ] **Integration Testing**
  - [ ] Invokes other skills correctly
  - [ ] Launches agents if needed
  - [ ] Data passed correctly
  - [ ] Chaining works

- [ ] **Inheritance Testing** (if applicable)
  - [ ] Generic skill loads as base
  - [ ] Project overrides apply
  - [ ] Combined behavior correct

- [ ] **Team Testing**
  - [ ] Works for all team members
  - [ ] Consistent behavior across machines
  - [ ] Git sync works correctly

---

## Deployment Phase

### Generic Skills Deployment

**Location:** `~/.claude/master-files/skills/generic-skills/`

**Process:**

```bash
# 1. Navigate to master-files
cd ~/.claude/master-files

# 2. Create skill directory
mkdir -p skills/generic-skills/my-new-skill

# 3. Add files
cd skills/generic-skills/my-new-skill
vim SKILL.md
mkdir templates checklists reference

# 4. Test locally
# [Test the skill]

# 5. Commit to master-files repo
cd ~/.claude/master-files
git add skills/generic-skills/my-new-skill
git commit -m "Add: my-new-skill for generic workflow"
git push

# 6. Sync to other machines
# On other machine:
cd ~/.claude/master-files
git pull
# Skill now available immediately in all projects
```

---

### Project Skills Deployment

**Location:** `.claude/skills/`

**Process:**

```bash
# 1. Navigate to project
cd /path/to/project

# 2. Create skill directory
mkdir -p .claude/skills/my-project-skill

# 3. Add files
cd .claude/skills/my-project-skill
vim SKILL.md
mkdir templates checklists reference

# 4. Test locally
# [Test the skill]

# 5. Commit with project
git add .claude/skills/my-project-skill
git commit -m "Add: my-project-skill for project workflow"
git push

# 6. Team pulls
# Teammate:
git pull
# Skill now available for this project
```

---

### Deployment Checklist

- [ ] **Pre-Deployment**
  - [ ] All tests passing
  - [ ] Documentation complete
  - [ ] Examples provided
  - [ ] Changelog updated
  - [ ] Version number set

- [ ] **Deployment**
  - [ ] Files in correct location
  - [ ] Git add all files
  - [ ] Descriptive commit message
  - [ ] Pushed to remote

- [ ] **Post-Deployment**
  - [ ] Verified on another machine/project
  - [ ] Team notified of new skill
  - [ ] Documentation index updated
  - [ ] Works as expected in clean environment

---

## Maintenance Phase

### Versioning Strategy

**Semantic Versioning:**
```
MAJOR.MINOR.PATCH

1.0.0 → Initial release
1.0.1 → Bug fix (backwards compatible)
1.1.0 → New feature (backwards compatible)
2.0.0 → Breaking change (not backwards compatible)
```

**Update YAML:**
```yaml
---
name: My Skill
version: 1.1.0  # Increment appropriately
---
```

**Update Changelog:**
```markdown
## Changelog

### Version 1.1.0 (2025-10-20)
- Added: New template for additional use case
- Fixed: Path resolution on Windows
- Changed: Improved error messages

### Version 1.0.0 (2025-10-19)
- Initial release
```

---

### Update Process

**For Generic Skills:**

```bash
# 1. Pull latest
cd ~/.claude/master-files
git pull

# 2. Make changes
cd skills/generic-skills/my-skill
vim SKILL.md  # Make updates

# 3. Update version and changelog
# Edit SKILL.md: version: 1.1.0
# Edit SKILL.md: Add changelog entry

# 4. Test changes
# [Test the updated skill]

# 5. Commit and push
cd ~/.claude/master-files
git add skills/generic-skills/my-skill
git commit -m "Update: my-skill v1.1.0 - [brief change description]"
git push

# 6. Sync to all machines
# Others: cd ~/.claude/master-files && git pull
```

**For Project Skills:**

```bash
# Same process but in project repo
cd /path/to/project
git pull
# Make changes
git add .claude/skills/my-skill
git commit -m "Update: my-skill v1.1.0 - [change]"
git push
```

---

## Generic vs Project-Specific Decision Tree

```
START: Creating a new skill
    ↓
┌─────────────────────────────────────┐
│ Can this skill work in ANY project  │ YES → GENERIC SKILL
│ with configuration?                 │       (~/.claude/master-files/)
└──────────────┬──────────────────────┘
               │ NO
               ↓
┌─────────────────────────────────────┐
│ Does this skill reference specific  │ YES → PROJECT SKILL
│ project files/paths/structures?     │       (.claude/skills/)
└──────────────┬──────────────────────┘
               │ NO (uncertain)
               ↓
┌─────────────────────────────────────┐
│ Could you use this skill in another │ YES → GENERIC SKILL
│ project with different config?      │       (make configurable)
└──────────────┬──────────────────────┘
               │ NO
               ↓
           PROJECT SKILL
```

**Examples:**

**Generic Skills:**
- ✅ Phase planning (any project has phases)
- ✅ Completion reports (any project needs reports)
- ✅ Database integration (any project uses databases)
- ✅ Testing workflows (any project has tests)
- ✅ Git commit strategy (any project uses git)

**Project Skills:**
- ✅ CA Lobby phase planning (references specific master plan path)
- ✅ CA Lobby BigQuery integration (specific BLN API schema)
- ✅ CA Lobby Vercel deployment (specific environment variables)
- ✅ CA Lobby doc navigator (specific documentation structure)

**Rule of Thumb:**
- If you use `${PLACEHOLDER}` for paths → Generic
- If you hardcode `Documentation/Phase2/Reports/` → Project

---

## Skill Composition Patterns

### Pattern 1: Simple Skill

**When:** Single focused task, no external files needed

```
skill-name/
└── SKILL.md
```

**Example:** Simple validation skill
```yaml
---
name: Environment Validation
description: Validate environment variables are set
allowed-tools: Read, Bash
---

# Steps
1. Check for required env vars
2. Report missing vars
3. Suggest fixes
```

---

### Pattern 2: Template-Based Skill

**When:** Generate files from templates

```
skill-name/
├── SKILL.md
└── templates/
    ├── template-1.md
    └── template-2.md
```

**SKILL.md:**
```markdown
## Step 1: Load Template
Read templates/template-1.md

## Step 2: Populate
Replace {{PLACEHOLDERS}}

## Step 3: Write
Write to output location
```

---

### Pattern 3: Checklist-Based Skill

**When:** Step-by-step verification

```
skill-name/
├── SKILL.md
└── checklists/
    ├── pre-checklist.md
    ├── main-checklist.md
    └── post-checklist.md
```

**SKILL.md:**
```markdown
## Pre-Flight
Execute checklists/pre-checklist.md

## Main Process
Execute checklists/main-checklist.md

## Verification
Execute checklists/post-checklist.md
```

---

### Pattern 4: Reference-Heavy Skill

**When:** Large pattern library, context management important

```
skill-name/
├── SKILL.md
└── reference/
    ├── pattern-1.md (500 lines)
    ├── pattern-2.md (400 lines)
    └── pattern-3.md (300 lines)
```

**SKILL.md (uses progressive disclosure):**
```markdown
## Step 1: Determine Pattern Needed
Ask user or detect

## Step 2: Load Pattern
- Pattern 1 → [reference/pattern-1.md](reference/pattern-1.md)
- Pattern 2 → [reference/pattern-2.md](reference/pattern-2.md)
- Pattern 3 → [reference/pattern-3.md](reference/pattern-3.md)

Only loads the needed pattern, not all 1,200 lines
```

---

### Pattern 5: Composite Skill

**When:** Orchestrates multiple skills/agents

```
skill-name/
├── SKILL.md
├── checklists/
│   └── validation-checklist.md
└── config/
    └── workflow-config.md
```

**SKILL.md:**
```markdown
## Step 1: Validate Prerequisites
Use checklists/validation-checklist.md

## Step 2: Launch Sub-Skills
- Invoke: skill-a
- Invoke: skill-b
- Launch agent: implementation-agent

## Step 3: Validate Results
Verify all sub-tasks complete

## Step 4: Finalize
Generate summary report
```

---

## Advanced Techniques

### Technique 1: Conditional Logic

**Use Case:** Different paths based on context

```markdown
## Step 1: Detect Environment
Determine:
- Production
- Staging
- Development

## Step 2: Apply Appropriate Logic
If Production:
  → Use checklists/production-checklist.md
  → Require manual approval
If Staging:
  → Use checklists/staging-checklist.md
  → Auto-deploy allowed
If Development:
  → Skip checklist
  → Auto-deploy
```

---

### Technique 2: Skill Chaining

**Use Case:** One skill triggers another

```markdown
## Step 1: Validate
Execute validation skill

## Step 2: Process
If validation passes:
  → Trigger processing-skill
Else:
  → Report errors and stop

## Step 3: Finalize
When processing complete:
  → Trigger finalization-skill
```

---

### Technique 3: Agent Delegation

**Use Case:** Skill delegates complex work to agent

```markdown
## Step 1: Validate Requirements (Skill)
Verify prerequisites met

## Step 2: Delegate Implementation (Agent)
Launch implementation agent:
Task({
  description: "Implement feature",
  prompt: "Detailed instructions..."
})

## Step 3: Validate Output (Skill)
Verify agent output meets standards

## Step 4: Finalize (Skill)
Generate documentation and reports
```

---

### Technique 4: Dynamic Template Selection

**Use Case:** Choose template based on context

```markdown
## Step 1: Determine Template
Based on context:
- Frontend feature → templates/frontend-template.md
- Backend feature → templates/backend-template.md
- Full-stack feature → templates/fullstack-template.md

## Step 2: Load Selected Template
Read appropriate template

## Step 3: Populate
Replace placeholders based on template type
```

---

### Technique 5: Incremental Validation

**Use Case:** Validate after each step

```markdown
## Step 1: Action A
Execute action A

### Validation
- [ ] Action A output exists
- [ ] Action A output format correct
[If fail → Stop and report error]

## Step 2: Action B
Execute action B

### Validation
- [ ] Action B output exists
- [ ] Action B output format correct
[If fail → Stop and report error]

## Step 3: Action C
[Only execute if A and B validated]
```

---

## Common Pitfalls

### Pitfall 1: Vague Description

**Problem:**
```yaml
description: Helps with planning
```

**Why it's bad:**
- Too vague
- No trigger keywords
- Won't activate when expected

**Solution:**
```yaml
description: Enforces phase planning protocol for CA Lobby project. Use when user requests to start a new phase, create implementation plan, or begin major feature development. Ensures master plan consultation and proper documentation structure.
```

---

### Pitfall 2: Mega-Skill

**Problem:**
```yaml
name: Development Helper
description: Helps with planning, implementation, testing, deployment, and documentation
```

**Why it's bad:**
- Too broad
- Hard to maintain
- Unclear when to activate
- Better as multiple focused skills

**Solution:**
Create separate skills:
- `planning-skill`
- `implementation-skill`
- `testing-skill`
- `deployment-skill`
- `documentation-skill`

---

### Pitfall 3: Forgetting Progressive Disclosure

**Problem:**
```markdown
# SKILL.md (10,000 lines)

[All patterns, examples, references in one file]
```

**Why it's bad:**
- Wastes context tokens
- Slow activation
- Hard to maintain

**Solution:**
```markdown
# SKILL.md (500 lines)

## Pattern Selection
- Pattern A → [reference/pattern-a.md](reference/pattern-a.md)
- Pattern B → [reference/pattern-b.md](reference/pattern-b.md)

Only loads needed pattern
```

---

### Pitfall 4: Hardcoding Project Paths in Generic Skills

**Problem:**
```yaml
# In generic skill
---
name: Generic Phase Planning
---

Read Documentation/General/MASTER_PROJECT_PLAN.md
```

**Why it's bad:**
- Won't work in other projects
- Defeats purpose of "generic"
- Should use placeholders

**Solution:**
```yaml
# In generic skill
---
name: Generic Phase Planning
---

Read ${PROJECT_MASTER_PLAN_PATH}
```

```yaml
# In project skill
---
name: CA Lobby Phase Planning
extends: generic-skills/phase-planning
---

PROJECT_MASTER_PLAN_PATH: Documentation/General/MASTER_PROJECT_PLAN.md
```

---

### Pitfall 5: No Tool Restrictions When Needed

**Problem:**
```yaml
---
name: Code Review
# No allowed-tools specified
---

# Could accidentally modify code during review
```

**Why it's bad:**
- Code review should be read-only
- Could introduce unintended changes
- Security/compliance issue

**Solution:**
```yaml
---
name: Code Review
allowed-tools: Read, Grep, Glob
---

# Can only read and analyze, not modify
```

---

### Pitfall 6: Missing Error Handling

**Problem:**
```markdown
## Steps
1. Read template
2. Populate template
3. Write file
```

**Why it's bad:**
- No handling if template missing
- No validation of populated data
- No verification file written

**Solution:**
```markdown
## Step 1: Read Template
Read templates/template.md
**Error Handling:** If template missing → Report error and exit

## Step 2: Populate Template
Replace placeholders
**Validation:** Verify all placeholders replaced

## Step 3: Write File
Write to output location
**Verification:** Confirm file exists and readable
```

---

## Examples & Templates

### Example 1: Minimal Skill

**File:** `~/.claude/master-files/skills/generic-skills/git-commit-message/SKILL.md`

```yaml
---
name: Git Commit Message Helper
description: Generate conventional commit messages. Use when user is committing code or needs commit message help.
allowed-tools: Read, Grep
---

# Git Commit Message Helper

## Purpose
Generate conventional commit messages following best practices

## When This Activates
- User says "commit" or "create commit message"
- User requests help with git commit

## Steps
1. Analyze staged changes (read git diff)
2. Determine commit type:
   - feat: New feature
   - fix: Bug fix
   - docs: Documentation
   - style: Formatting
   - refactor: Code restructuring
   - test: Tests
   - chore: Maintenance
3. Generate message:
   ```
   <type>(<scope>): <subject>

   <body>
   ```

## Output
Conventional commit message ready to use
```

---

### Example 2: Template-Based Skill

**Files:**
- `completion-report/SKILL.md`
- `completion-report/templates/report-template.md`

**SKILL.md:**
```yaml
---
name: Completion Report Generator
description: Generate phase completion reports from template. Use when phase complete or user says "create completion report".
allowed-tools: Read, Write
---

# Completion Report Generator

## Purpose
Generate standardized completion reports for project phases

## Steps
1. Load template from templates/report-template.md
2. Gather phase information:
   - Phase name
   - Completion date
   - Objectives achieved
   - Deliverables
   - Files modified
   - Metrics
   - Issues encountered
   - Next steps
3. Populate template with data
4. Write to Documentation/PhaseX/Reports/{{PHASE}}_COMPLETION_REPORT.md

## Output
Completion report file created
```

**templates/report-template.md:**
```markdown
# {{PHASE_NAME}} - Completion Report

**Phase:** {{PHASE_NUMBER}}
**Completion Date:** {{COMPLETION_DATE}}
**Status:** ✅ COMPLETED

## Executive Summary
{{EXECUTIVE_SUMMARY}}

## Objectives Achieved
{{OBJECTIVES}}

## Deliverables
{{DELIVERABLES}}

## Files Modified/Created
{{FILES}}

## Metrics
{{METRICS}}

## Issues Encountered
{{ISSUES}}

## Next Steps
{{NEXT_STEPS}}

## Sign-Off
{{SIGN_OFF}}
```

---

### Example 3: Checklist-Based Skill

**Files:**
- `pre-deployment/SKILL.md`
- `pre-deployment/checklists/checklist.md`

**SKILL.md:**
```yaml
---
name: Pre-Deployment Checklist
description: Verify deployment readiness. Use before deploying or when user says "ready to deploy".
allowed-tools: Read, Bash, Glob
---

# Pre-Deployment Checklist

## Purpose
Ensure application ready for deployment

## Steps
1. Execute checklist from checklists/checklist.md
2. For each item:
   - Run verification command
   - Report status (✅ pass / ❌ fail)
3. Generate summary:
   - All pass → GO for deployment
   - Any fail → NO-GO with blockers listed

## Output
GO/NO-GO decision with details
```

**checklists/checklist.md:**
```markdown
# Pre-Deployment Checklist

## Code Quality
- [ ] All tests passing
  Command: `npm test`
- [ ] Build succeeds
  Command: `npm run build`
- [ ] No console.log in code
  Command: `grep -r "console.log" src/`

## Configuration
- [ ] Environment variables set
  Verify: .env file present
- [ ] API keys valid
  Verify: Keys not expired

## Dependencies
- [ ] Dependencies up to date
  Command: `npm outdated`
- [ ] No vulnerabilities
  Command: `npm audit`

## Documentation
- [ ] CHANGELOG updated
- [ ] README current
```

---

## Summary

**Quick Implementation Steps:**

1. **Design** (30 min)
   - Define purpose
   - Map workflow
   - Write description
   - Design file structure

2. **Implement** (1-2 hours)
   - Create SKILL.md
   - Add supporting files
   - Configure tool restrictions
   - Implement inheritance if needed

3. **Test** (30 min)
   - Test activation
   - Test tool restrictions
   - Test supporting files
   - Test output

4. **Deploy** (15 min)
   - Commit to appropriate repo
   - Sync to team
   - Update documentation

5. **Maintain** (ongoing)
   - Version updates
   - Bug fixes
   - Feature additions
   - Team feedback

**Remember:**
- Keep skills focused (one capability)
- Optimize descriptions for discovery
- Use progressive disclosure
- Test thoroughly
- Document well

---

**Document End**

For more information:
- [CLAUDE_SKILLS_DEEP_DIVE.md](CLAUDE_SKILLS_DEEP_DIVE.md) - Complete skills documentation
- [SKILLS_VS_AGENTS_COMPARISON.md](SKILLS_VS_AGENTS_COMPARISON.md) - Skills vs Agents
- [templates/](templates/) - Skill templates and examples

**Version:** 1.0
**Last Updated:** October 19, 2025
**Maintained By:** Master-Files-Toolkit Team
