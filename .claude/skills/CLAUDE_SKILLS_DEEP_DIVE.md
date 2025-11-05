# Claude Skills: Deep Dive Technical Guide

**Document Version:** 1.0
**Last Updated:** October 19, 2025
**Purpose:** Comprehensive technical documentation on Claude Skills in Claude Code
**Audience:** Developers, team leads, technical architects

---

## Table of Contents

1. [What Are Claude Skills?](#what-are-claude-skills)
2. [Skills vs Agents vs Slash Commands](#skills-vs-agents-vs-slash-commands)
3. [Technical Architecture](#technical-architecture)
4. [File Structure & Organization](#file-structure--organization)
5. [YAML Frontmatter Specification](#yaml-frontmatter-specification)
6. [The `allowed-tools` Security Model](#the-allowed-tools-security-model)
7. [Discovery & Activation Mechanism](#discovery--activation-mechanism)
8. [Progressive Disclosure](#progressive-disclosure)
9. [Skill Inheritance & Extension](#skill-inheritance--extension)
10. [Advanced Patterns](#advanced-patterns)
11. [Best Practices](#best-practices)
12. [Testing & Validation](#testing--validation)
13. [Team Collaboration](#team-collaboration)
14. [Integration Strategies](#integration-strategies)
15. [Troubleshooting](#troubleshooting)

---

## What Are Claude Skills?

### Definition

**Claude Skills** are modular capability packages that extend Claude's functionality within Claude Code. They consist of:

1. **Required:** A `SKILL.md` file containing:
   - YAML frontmatter (metadata and configuration)
   - Markdown instructions for Claude to follow

2. **Optional:** Supporting files:
   - Templates
   - Reference documentation
   - Scripts
   - Checklists
   - Examples

### Key Characteristics

**Model-Invoked** (Not User-Invoked):
- Claude automatically decides when to activate a skill
- Based on task context matching the skill's `description` field
- No explicit user command needed (unlike slash commands)

**Purpose-Focused:**
- Each skill addresses ONE specific capability
- Examples: "Phase planning", "Completion report generation", "Database integration"
- Narrow scope ensures reliability and predictability

**Protocol Enforcement:**
- Skills are ideal for enforcing project workflows
- Ensure mandatory steps are never skipped
- Provide consistent templates and checklists

---

## Skills vs Agents vs Slash Commands

### Comparison Table

| Aspect | **SKILLS** | **AGENTS** | **SLASH COMMANDS** |
|--------|-----------|-----------|-------------------|
| **Invocation** | Model-invoked (automatic) | Explicitly launched via Task tool | User types `/command` |
| **Context** | Same conversation | Separate isolated context | Same conversation |
| **Purpose** | Protocols, workflows, templates | Complex multi-step tasks | Quick shortcuts |
| **Scope** | Single focused capability | Broader multi-capability | Single predefined action |
| **Tool Access** | Restricted via `allowed-tools` | Full access (configurable) | Full access |
| **Parallelization** | Sequential (same context) | Parallel (separate contexts) | Sequential |
| **File Location** | `.claude/skills/NAME/SKILL.md` | `.claude/agents/name.md` | `.claude/commands/name.md` |
| **Discovery** | Via `description` matching | Via `subagent_type` parameter | Via `/` prefix |
| **Result** | Inline in conversation | Returns to main conversation | Inline in conversation |
| **Best For** | Checklists, templates, compliance | Research, implementation | Frequent repetitive tasks |
| **Configuration** | YAML frontmatter | YAML frontmatter | Markdown only |
| **Context Preservation** | Uses main context | Isolated context | Uses main context |

### When to Use Each

#### ✅ Use SKILLS when:
- Enforcing mandatory protocols (phase planning, completion reports)
- Providing templates (commit messages, documentation structure)
- Restricting tool access (read-only operations, security-sensitive tasks)
- Guiding step-by-step workflows (testing procedures, deployment checklists)
- Ensuring consistency (naming conventions, file organization)

**Example:** "Always generate a completion report after finishing a phase"

#### ✅ Use AGENTS when:
- Performing complex multi-step implementations
- Running parallel research tasks
- Isolating context to preserve main conversation
- Delegating specialized expertise (React specialist, deployment orchestrator)
- Background task execution (session archiving, documentation generation)

**Example:** "Launch a general-purpose agent to implement the authentication system"

#### ✅ Use SLASH COMMANDS when:
- Frequent repetitive operations (/commit, /deploy, /test)
- Simple predefined workflows
- Quick access to common tasks
- User wants explicit control over invocation

**Example:** "/archive" to archive current session

---

## Technical Architecture

### Skill Lifecycle

```
1. User Request
   ↓
2. Claude Analyzes Context
   ↓
3. Skill Discovery
   - Searches .claude/skills/ (project-specific first)
   - Searches ~/.claude/master-files/skills/generic-skills/ (generic fallback)
   ↓
4. Description Matching
   - Compares request context to skill descriptions
   - Selects best-matching skill(s)
   ↓
5. Skill Activation
   - Loads SKILL.md file
   - Reads YAML frontmatter
   - Applies tool restrictions if specified
   ↓
6. Execution
   - Follows skill instructions
   - Uses only allowed-tools (if restricted)
   - Loads supporting files as needed
   ↓
7. Result
   - Returns output inline in conversation
   - May invoke agents if needed
   - May trigger other skills
```

### Storage Hierarchy

**Priority Order (highest to lowest):**

1. **Project-Specific Skills:** `.claude/skills/`
   - Checked first
   - Highest priority
   - Project-customized

2. **Generic Skills:** `~/.claude/master-files/skills/generic-skills/`
   - Fallback if no project skill
   - Reusable across projects
   - Synced via git

3. **Plugin Skills:** Bundled with installed plugins
   - Lowest priority
   - Plugin-specific capabilities

### File System Organization

```
~/.claude/
├── master-files/
│   └── skills/
│       ├── generic-skills/          # Reusable skills
│       │   ├── phase-planning/
│       │   ├── completion-report/
│       │   └── database-integration/
│       └── templates/               # Skill templates
│
/project-root/
└── .claude/
    └── skills/                      # Project-specific skills
        ├── phase-planning/          # Extends generic
        ├── database-integration/    # Extends generic
        └── custom-skill/            # Project-unique
```

---

## File Structure & Organization

### Required Structure

Every skill MUST have at minimum:

```
skill-name/
└── SKILL.md
```

### Recommended Structure

For production-ready skills:

```
skill-name/
├── SKILL.md                    # Main skill definition (required)
├── README.md                   # Skill documentation (optional)
├── templates/                  # Template files (optional)
│   ├── template-1.md
│   └── template-2.md
├── checklists/                 # Checklists (optional)
│   ├── pre-checklist.md
│   └── post-checklist.md
├── reference/                  # Reference docs (optional)
│   ├── patterns.md
│   └── examples.md
└── config/                     # Configuration (optional)
    └── defaults.md
```

### Naming Conventions

**Skill Directory:**
- Use kebab-case: `phase-planning`, `database-integration`
- Be descriptive: `cloud-deployment` not `deploy`
- Avoid abbreviations: `documentation-structure` not `doc-struct`

**Supporting Files:**
- Use descriptive names: `phase-plan-template.md` not `template.md`
- Include purpose: `pre-deployment-checklist.md` not `checklist.md`

---

## YAML Frontmatter Specification

### Complete Specification

```yaml
---
name: Skill Display Name                    # REQUIRED
description: Detailed description...        # REQUIRED
allowed-tools: Tool1, Tool2, Tool3          # OPTIONAL
extends: generic-skills/base-skill          # OPTIONAL (project skills only)
version: 1.0.0                              # OPTIONAL
author: Your Name                           # OPTIONAL
---
```

### Field Descriptions

#### `name` (REQUIRED)
- **Type:** String
- **Purpose:** Human-readable skill name
- **Format:** Title Case
- **Example:** `"CA Lobby Phase Planning"`
- **Best Practice:** Be specific and descriptive

#### `description` (REQUIRED)
- **Type:** String
- **Purpose:** CRITICAL for skill discovery
- **Format:** 1-3 sentences
- **Must Include:**
  - What the skill does
  - When Claude should use it
  - Trigger keywords
- **Example:**
  ```yaml
  description: Enforce phase planning protocol for CA Lobby project. Use when user requests to start a new phase, create implementation plan, or begin major feature development. Ensures master plan consultation and proper documentation structure.
  ```

**Description Best Practices:**
- Include action verbs: "Use when...", "Activate for...", "Invoke when..."
- List trigger keywords: "phase", "planning", "implementation"
- Be specific about context: "CA Lobby project" not just "project"
- Front-load important terms: Start with the primary purpose

**❌ Bad Description:**
```yaml
description: Helps with planning
```

**✅ Good Description:**
```yaml
description: Enforces master plan consultation before starting any new project phase. Use when user says "start new phase", "plan phase X", or "begin implementation". Verifies prerequisites, creates phase documentation, and defines micro save points.
```

#### `allowed-tools` (OPTIONAL)
- **Type:** Comma-separated list
- **Purpose:** Restrict which tools Claude can use
- **Format:** `Tool1, Tool2, Tool3`
- **Available Tools:**
  - `Read` - Read files
  - `Write` - Write new files
  - `Edit` - Edit existing files
  - `Bash` - Run bash commands
  - `Glob` - Find files by pattern
  - `Grep` - Search file contents
  - `WebFetch` - Fetch web content
  - `Task` - Launch agents
- **Example:**
  ```yaml
  allowed-tools: Read, Glob, Grep
  ```
- **Use Cases:**
  - **Read-only skills:** `Read, Glob, Grep` (research, analysis)
  - **Documentation skills:** `Read, Write` (documentation generation)
  - **Planning skills:** `Read, Glob, WebFetch` (planning, no code changes)

**Security Benefits:**
- Prevents accidental file modifications during planning
- Ensures compliance with read-only policies
- Reduces risk in security-sensitive operations

#### `extends` (OPTIONAL)
- **Type:** String (path to generic skill)
- **Purpose:** Inherit from generic skill
- **Format:** `generic-skills/skill-name`
- **Example:**
  ```yaml
  extends: generic-skills/phase-planning
  ```
- **Behavior:**
  - Loads generic skill as base
  - Applies project-specific overrides
  - Combines generic + project instructions

#### `version` (OPTIONAL)
- **Type:** Semantic version string
- **Format:** `MAJOR.MINOR.PATCH`
- **Example:** `1.0.0`, `2.1.3`
- **Best Practice:** Track breaking changes

#### `author` (OPTIONAL)
- **Type:** String
- **Purpose:** Attribution
- **Example:** `"CA Lobby Team"`

---

## The `allowed-tools` Security Model

### Purpose

The `allowed-tools` field provides:
1. **Security:** Prevent unintended tool access
2. **Compliance:** Enforce read-only or write-only policies
3. **Clarity:** Explicit about skill capabilities
4. **Safety:** Reduce risk during sensitive operations

### Tool Categories

#### Read-Only Tools
```yaml
allowed-tools: Read, Glob, Grep, WebFetch
```
**Use for:**
- Research and analysis skills
- Documentation discovery
- Code review checklists
- Planning phases (before implementation)

**Example Skill:**
```yaml
---
name: Code Review Checklist
description: Provides code review checklist. Use when reviewing code changes or pull requests.
allowed-tools: Read, Glob, Grep
---
```

#### Write-Only Tools
```yaml
allowed-tools: Write
```
**Use for:**
- File generation skills
- Report creation
- Documentation generation (new files only)

#### Edit-Only Tools
```yaml
allowed-tools: Read, Edit
```
**Use for:**
- Code modification skills
- Refactoring workflows
- Configuration updates

#### Full Access (Default)
```yaml
# No allowed-tools field = all tools available
```
**Use for:**
- Implementation skills
- Deployment workflows
- Complex multi-step tasks

### Security Patterns

#### Pattern 1: Planning Phase (Read-Only)
```yaml
---
name: Phase Planning
description: Create implementation plans before coding
allowed-tools: Read, Glob, WebFetch
---
# During planning, cannot modify code
# Can only read existing files and research
```

#### Pattern 2: Implementation Phase (Full Access)
```yaml
---
name: Phase Implementation
description: Execute planned implementation tasks
# No allowed-tools = full access during implementation
---
```

#### Pattern 3: Review Phase (Read + Analysis)
```yaml
---
name: Code Review
description: Review code for quality and standards
allowed-tools: Read, Grep, Glob
---
# Can analyze code but cannot modify
```

---

## Discovery & Activation Mechanism

### How Claude Discovers Skills

1. **User Makes Request**
   ```
   User: "I want to start planning Phase 3"
   ```

2. **Claude Analyzes Context**
   - Extracts keywords: "planning", "Phase 3"
   - Understands intent: User wants to plan a new phase
   - Checks project context: CA Lobby project

3. **Skill Search**
   ```
   Search Order:
   1. .claude/skills/                              (project-specific)
   2. ~/.claude/master-files/skills/generic-skills/ (generic)
   3. Plugins                                       (if installed)
   ```

4. **Description Matching**
   ```
   Skill: phase-planning
   Description: "...Use when user requests to start a new phase,
                 create implementation plan..."

   Match Score: HIGH (contains "planning", "phase")
   → Skill Activated
   ```

5. **Skill Loading**
   - Reads SKILL.md
   - Parses YAML frontmatter
   - Loads instructions
   - Applies tool restrictions

### Activation Triggers

#### Strong Triggers (High Confidence)
- Exact keyword matches in description
- User explicitly mentions skill purpose
- Context strongly aligns with skill domain

**Example:**
```yaml
description: Use when user says "create completion report" or
             "document phase completion"
```
```
User: "Create a completion report for Phase 2"
→ STRONG MATCH → Skill activates immediately
```

#### Weak Triggers (Medium Confidence)
- Related keywords
- Similar intent
- Contextual alignment

**Example:**
```yaml
description: Database integration patterns for SQL and NoSQL
```
```
User: "How do I query the BigQuery database?"
→ MEDIUM MATCH → Skill may activate with other considerations
```

#### No Trigger (Low Confidence)
- Unrelated keywords
- Different intent
- Context mismatch

### Optimization for Discovery

**Description Formula:**
```yaml
description: [Primary Action] for [Specific Context]. Use when [Trigger Conditions]. [What it ensures/provides].
```

**Example:**
```yaml
description: Enforces completion report generation for CA Lobby phases. Use when user completes a phase, finishes implementation, or says "create completion report". Ensures all 12 required sections are documented and master plan is updated.
```

**Breakdown:**
- **Primary Action:** "Enforces completion report generation"
- **Specific Context:** "for CA Lobby phases"
- **Trigger Conditions:** "when user completes a phase, finishes implementation, or says 'create completion report'"
- **What it ensures:** "all 12 required sections are documented and master plan is updated"

---

## Progressive Disclosure

### Concept

**Progressive Disclosure** = Claude loads supporting files only when needed, not all at once.

**Benefits:**
- **Context Efficiency:** Doesn't waste context on unused information
- **Performance:** Faster skill activation
- **Scalability:** Skills can reference large documentation sets

### How It Works

**SKILL.md (Always Loaded):**
```markdown
---
name: Database Integration
description: Database patterns for SQL and NoSQL
---

# Database Integration Skill

## Overview
This skill provides database integration guidance.

## Supported Databases
For specific patterns, see:
- [PostgreSQL Patterns](reference/postgres-patterns.md)
- [BigQuery Patterns](reference/bigquery-patterns.md)
- [MongoDB Patterns](reference/mongo-patterns.md)

## Steps
1. Determine database type
2. Load appropriate pattern file (linked above)
3. Apply patterns to user's context
```

**Progressive Loading:**
```
Initial Load:
- SKILL.md (main instructions) ← Always loaded

When Claude needs PostgreSQL help:
- reference/postgres-patterns.md ← Loaded on demand

When Claude needs BigQuery help:
- reference/bigquery-patterns.md ← Loaded on demand
```

### Implementation Pattern

**Main Skill File (SKILL.md):**
- Keep concise
- Provide overview
- Link to detailed references
- Include decision tree for which reference to load

**Supporting Files:**
- Detailed patterns
- Extensive examples
- Large checklists
- Template libraries

**Example Structure:**
```
database-integration/
├── SKILL.md                      # 200 lines (always loaded)
├── reference/
│   ├── postgres-patterns.md      # 500 lines (loaded when needed)
│   ├── bigquery-patterns.md      # 400 lines (loaded when needed)
│   ├── mongodb-patterns.md       # 450 lines (loaded when needed)
│   └── orm-patterns.md           # 300 lines (loaded when needed)
└── templates/
    ├── migration-template.sql    # Loaded when needed
    └── schema-template.sql       # Loaded when needed
```

**Context Savings:**
- Without progressive disclosure: 2,350 lines loaded every time
- With progressive disclosure: 200 lines + ~400 lines when specific DB needed
- Savings: ~82% context reduction

---

## Skill Inheritance & Extension

### Generic vs Project-Specific Pattern

**Generic Skills** provide base templates.
**Project Skills** extend with project-specific details.

### How Extension Works

#### 1. Generic Skill (Base)

**File:** `~/.claude/master-files/skills/generic-skills/phase-planning/SKILL.md`

```yaml
---
name: Generic Phase Planning
description: Template-based phase planning for any project. Use when starting project phases. Configuration required via project settings.
allowed-tools: Read, Glob
---

# Generic Phase Planning Skill

## Purpose
Provides universal phase planning workflow.

## Configuration Required
Projects must provide:
- ${PROJECT_MASTER_PLAN_PATH} - Path to master plan
- ${PROJECT_DOCS_PATH} - Documentation directory
- ${PROJECT_PHASE_FORMAT} - Phase naming format

## Standard Sections
1. Executive Summary
2. Objectives
3. Prerequisites
4. Deliverables
5. Success Criteria
6. Risks
7. Timeline
8. Micro Save Points

## Steps (Generic)
1. Read project configuration from .claude/skills/SKILLS_PROJECT_CONFIGURATION.md
2. Check if project has phase-planning skill (extends this one)
3. Load project-specific paths and formats
4. Generate plan using project template
5. Verify prerequisites
6. Present plan for approval
```

#### 2. Project Skill (Extension)

**File:** `.claude/skills/phase-planning/SKILL.md` (CA Lobby)

```yaml
---
name: CA Lobby Phase Planning
description: CA Lobby phase planning following master project plan protocol. Use when starting phases in CA Lobby project.
extends: generic-skills/phase-planning
---

# CA Lobby Phase Planning Skill

## Project Configuration
- PROJECT_MASTER_PLAN_PATH: Documentation/General/MASTER_PROJECT_PLAN.md
- PROJECT_DOCS_PATH: Documentation/PhaseX/Plans/
- PROJECT_PHASE_FORMAT: PHASE_[X]_[NAME]_PLAN.md

## Additional Sections Required (Beyond Generic 8)
9. Demo Data Considerations
10. Vercel Deployment Impact
11. BigQuery Integration Points
12. Integration with Existing Components

## CA Lobby Specific Steps
1. Read Documentation/General/MASTER_PROJECT_PLAN.md (MANDATORY)
2. Verify previous phase completion report exists
3. Check Documentation/PhaseX/Reports/ for last report
4. Create new plan in Documentation/PhaseX/Plans/
5. Follow CA Lobby template (12 sections)
6. Define micro save points with 30-45 min intervals
7. Consider demo data vs backend API mode
8. Plan Vercel deployment testing

## Prerequisites Specific to CA Lobby
- Previous phase MUST have completion report
- Master plan MUST be updated with previous phase status
- All previous deliverables MUST be deployed
- Demo data mode MUST be functional
```

#### 3. Resolution Logic

When user says: **"Let's plan Phase 3"**

```
Step 1: Check project-specific
  → Found: .claude/skills/phase-planning/SKILL.md
  → Has extends: generic-skills/phase-planning

Step 2: Load generic base
  → Load: ~/.claude/master-files/skills/generic-skills/phase-planning/SKILL.md
  → Load 8 standard sections
  → Load generic steps

Step 3: Apply project overrides
  → Add 4 CA Lobby-specific sections (9-12)
  → Override steps with CA Lobby specifics
  → Replace ${PROJECT_MASTER_PLAN_PATH} with actual path

Step 4: Execute combined skill
  → Uses generic structure
  → Applies CA Lobby specifics
  → Generates plan with 12 sections (not just 8)
  → References CA Lobby documentation paths
```

### Benefits of Inheritance

**For Generic Skills:**
- ✅ Reusable across all projects
- ✅ Maintain industry best practices
- ✅ One update benefits all projects
- ✅ No duplication

**For Project Skills:**
- ✅ Project-specific without reinventing structure
- ✅ Extend only what's needed
- ✅ Override behavior selectively
- ✅ Version controlled with project

**For Teams:**
- ✅ Consistent base structure
- ✅ Project customization as needed
- ✅ Shared generic improvements
- ✅ Project autonomy preserved

---

## Advanced Patterns

### Pattern 1: Multi-Stage Skills

**Use Case:** Complex workflows with distinct phases

**Example: Deployment Skill**
```yaml
---
name: Cloud Deployment
description: Multi-stage deployment workflow
---

# Cloud Deployment Skill

## Stage 1: Pre-Deployment
[checklist: checklists/pre-deployment.md]
- Environment variables
- Build configuration
- Test passing

## Stage 2: Deployment
[checklist: checklists/deployment.md]
- Deploy to staging
- Run smoke tests
- Verify health checks

## Stage 3: Post-Deployment
[checklist: checklists/post-deployment.md]
- Monitor logs
- Verify metrics
- Update documentation

## Rollback Procedure
If any stage fails:
[procedure: checklists/rollback.md]
```

### Pattern 2: Skill Chaining

**Use Case:** One skill triggers another

**Example: Phase Completion Workflow**
```yaml
---
name: Phase Completion Workflow
description: Complete a project phase end-to-end
---

# Phase Completion Workflow

## Step 1: Test & Validate
[Trigger: testing-workflow skill]

## Step 2: Generate Completion Report
[Trigger: completion-report skill]

## Step 3: Update Master Plan
[Trigger: master-plan-update skill]

## Step 4: Deploy
[Trigger: cloud-deployment skill]

## Step 5: Archive Session
[Trigger: session-archiver agent]
```

### Pattern 3: Conditional Logic

**Use Case:** Different paths based on context

**Example: Database Integration**
```yaml
---
name: Database Integration
description: Database patterns with conditional logic
---

# Database Integration Skill

## Step 1: Detect Database Type
Read configuration to determine:
- BigQuery → Load reference/bigquery-patterns.md
- PostgreSQL → Load reference/postgres-patterns.md
- MongoDB → Load reference/mongo-patterns.md

## Step 2: Apply Appropriate Patterns
[Conditional based on Step 1 detection]

## Step 3: Verify Integration
[Database-specific verification checklist]
```

### Pattern 4: Template-Based Generation

**Use Case:** Generate files from templates

**Example: Completion Report Skill**
```yaml
---
name: Completion Report
description: Generate completion reports from templates
allowed-tools: Read, Write
---

# Completion Report Skill

## Step 1: Load Template
Read templates/completion-report-template.md

## Step 2: Gather Information
- Phase objectives
- Deliverables achieved
- Files modified
- Metrics

## Step 3: Populate Template
Replace placeholders:
- {{PHASE_NAME}}
- {{COMPLETION_DATE}}
- {{DELIVERABLES}}
- {{METRICS}}

## Step 4: Write Report
Write to: Documentation/PhaseX/Reports/{{PHASE_NAME}}_COMPLETION_REPORT.md

## Step 5: Trigger Next Skill
[Invoke: master-plan-update skill]
```

### Pattern 5: Read-Only Validation

**Use Case:** Validate without modifying

**Example: Code Review Skill**
```yaml
---
name: Code Review Checklist
description: Review code against standards
allowed-tools: Read, Grep, Glob
---

# Code Review Checklist Skill

## Validation Steps (Read-Only)
1. Read files being reviewed
2. Check against standards:
   - [ ] Naming conventions
   - [ ] Documentation present
   - [ ] Tests included
   - [ ] Error handling
3. Generate review report
4. Provide feedback (no modifications)

## Output
Checklist with:
- ✅ Items passing
- ❌ Items failing
- 💡 Recommendations
```

---

## Best Practices

### 1. Scope Definition

**✅ DO:**
- Focus on ONE capability per skill
- Be specific in skill name
- Clearly define boundaries

**❌ DON'T:**
- Create mega-skills with multiple unrelated functions
- Use vague names like "helper" or "utility"
- Mix different concerns

**Example:**

❌ **Bad:**
```yaml
name: Development Helper
description: Helps with various development tasks
```

✅ **Good:**
```yaml
name: Phase Planning
description: Enforces phase planning protocol with master plan consultation
```

### 2. Description Optimization

**Formula:**
```
[Action] for [Context]. Use when [Triggers]. [Ensures/Provides].
```

**✅ DO:**
- Front-load important keywords
- Include explicit trigger phrases
- Be specific about context
- Mention what it ensures/prevents

**❌ DON'T:**
- Be vague or generic
- Use jargon without explanation
- Forget trigger keywords

### 3. Tool Restriction Strategy

**When to Restrict:**
- ✅ Planning phases (read-only)
- ✅ Review/validation operations
- ✅ Security-sensitive tasks
- ✅ Compliance-required workflows

**When Not to Restrict:**
- ✅ Implementation skills
- ✅ Deployment workflows
- ✅ Complex multi-step tasks

### 4. File Organization

**✅ DO:**
- Use consistent directory structure
- Group related files in subdirectories
- Name files descriptively
- Include README for complex skills

**❌ DON'T:**
- Dump all files in root
- Use cryptic file names
- Mix unrelated files

### 5. Documentation

**Required Documentation:**
- Clear purpose statement
- Activation triggers
- Steps/workflow
- Expected outputs
- Integration points

**Optional but Recommended:**
- Examples
- Edge cases
- Troubleshooting
- Version history

### 6. Version Control

**✅ DO:**
- Use semantic versioning
- Document breaking changes
- Maintain changelog
- Tag stable versions

**❌ DON'T:**
- Make breaking changes without version bump
- Skip documentation of changes

### 7. Testing

**Test Before Deployment:**
- ✅ Skill activates correctly
- ✅ Tool restrictions work
- ✅ Supporting files load
- ✅ Templates populate correctly
- ✅ Integration with other skills

**Test Scenarios:**
- Happy path (expected use)
- Edge cases
- Error conditions
- Tool restriction violations

---

## Testing & Validation

### Testing Checklist

#### Phase 1: Local Testing

**1. Skill Discovery**
```
Test: Does skill activate on expected triggers?

Actions:
- Use trigger phrases from description
- Verify skill loads
- Check YAML parsed correctly

Expected: Skill activates automatically
```

**2. Tool Restrictions**
```
Test: Do allowed-tools restrictions work?

Actions:
- Set allowed-tools: Read, Glob
- Attempt Write operation
- Verify restriction enforced

Expected: Write blocked, error message shown
```

**3. Supporting Files**
```
Test: Do supporting files load correctly?

Actions:
- Reference template file
- Load checklist
- Verify content accessible

Expected: Files load on demand
```

**4. Template Population**
```
Test: Do templates populate with correct data?

Actions:
- Trigger template-based skill
- Verify placeholders replaced
- Check output format

Expected: Template populated correctly
```

#### Phase 2: Integration Testing

**1. Skill Chaining**
```
Test: Can skills trigger other skills?

Actions:
- Use skill that invokes another
- Verify second skill activates
- Check data passed correctly

Expected: Chained skills execute in order
```

**2. Agent Integration**
```
Test: Can skills invoke agents?

Actions:
- Use skill that launches agent
- Verify agent receives correct prompt
- Check agent results returned

Expected: Skill → Agent → Results flow works
```

**3. Inheritance**
```
Test: Does skill inheritance work?

Actions:
- Create project skill extending generic
- Verify generic base loads
- Check project overrides apply

Expected: Combined behavior (generic + project)
```

#### Phase 3: Team Testing

**1. Team Member Testing**
```
Test: Do skills work for all team members?

Actions:
- Have teammate pull skill
- Verify activation
- Check consistent behavior

Expected: Same behavior across team
```

**2. Cross-Project Testing**
```
Test: Do generic skills work in different projects?

Actions:
- Use generic skill in another project
- Provide project configuration
- Verify skill adapts

Expected: Skill adapts to new project context
```

### Validation Tools

**Manual Validation:**
```bash
# Check YAML syntax
cat SKILL.md | grep -A 10 "^---$"

# Verify file structure
ls -R skill-name/

# Test from clean state
rm -rf .claude/skills/test-skill
cp -r test-skill .claude/skills/
# Then trigger in Claude
```

**Automated Validation (Future):**
```bash
# Skill linter (to be developed)
claude-skill-lint .claude/skills/phase-planning/

# Skill tester
claude-skill-test .claude/skills/phase-planning/ --scenario "new phase"
```

---

## Team Collaboration

### Sharing Skills via Git

#### Generic Skills (Master-Files Repo)

**Repository:** `~/.claude/master-files` (synced to GitHub)

**Workflow:**
```bash
# Developer A adds new generic skill
cd ~/.claude/master-files/skills/generic-skills
mkdir new-skill
vim new-skill/SKILL.md

# Commit and push
cd ~/.claude/master-files
git add skills/generic-skills/new-skill
git commit -m "Add: new-skill for generic workflow"
git push

# Developer B syncs
cd ~/.claude/master-files
git pull
# Skill immediately available in all projects
```

#### Project-Specific Skills (Project Repo)

**Repository:** Project repo (e.g., CA_lobby)

**Workflow:**
```bash
# Developer A adds project skill
cd /path/to/ca_lobby
mkdir -p .claude/skills/project-skill
vim .claude/skills/project-skill/SKILL.md

# Commit with project code
git add .claude/skills/project-skill
git commit -m "Add: project-specific skill for CA Lobby"
git push

# Developer B pulls project
git pull
# Skill available for CA Lobby project only
```

### Skill Documentation Standards

**Required in SKILL.md:**
```markdown
# Skill Name

## Purpose
What this skill does

## Activation Triggers
When it activates

## Configuration
What settings are needed

## Steps
How it works

## Examples
Usage examples

## Integration
How it integrates with other skills/agents

## Changelog
Version history
```

### Code Review for Skills

**Review Checklist:**
- [ ] Description optimized for discovery
- [ ] Tool restrictions appropriate
- [ ] Supporting files well-organized
- [ ] Templates tested
- [ ] Documentation complete
- [ ] Examples provided
- [ ] Versioned appropriately
- [ ] No hardcoded paths (unless project-specific)

---

## Integration Strategies

### Skills + Agents

**Pattern 1: Skill Invokes Agent**
```yaml
---
name: Complex Implementation Skill
description: Handles complex implementation with agent delegation
---

# Steps
1. Validate requirements (skill does this)
2. Launch implementation agent for complex work
   [Invoke: Task tool with general-purpose agent]
3. Validate agent output (skill does this)
4. Generate completion report (skill does this)
```

**Pattern 2: Agent Uses Skill Knowledge**
```yaml
---
name: Deployment Agent
description: Agent that references deployment skill checklist
---

# Agent Instructions
1. Read .claude/skills/cloud-deployment/SKILL.md
2. Follow checklist from skill
3. Execute deployment steps
4. Report back to main conversation
```

### Skills + Slash Commands

**Pattern: Slash Command Triggers Skill**
```markdown
# /deploy command (.claude/commands/deploy.md)

Trigger the cloud-deployment skill to deploy this project.

Include:
- Pre-deployment checks
- Deployment execution
- Post-deployment validation
```

### Skills + Workflows

**Pattern: Workflow References Multiple Skills**
```markdown
# Opening Workflow

1. Context Setup
   [Trigger: context-manager skill]

2. Verify Project State
   [Trigger: project-state skill]

3. Load Documentation
   [Trigger: doc-navigator skill]

4. Ready for work
```

---

## Troubleshooting

### Skill Not Activating

**Problem:** Skill doesn't activate when expected

**Diagnosis:**
1. Check description matches user request
2. Verify skill file location
3. Confirm YAML syntax valid
4. Check for conflicting skills

**Solutions:**
- Improve description with more trigger keywords
- Move skill to correct location
- Fix YAML syntax errors
- Make skill description more specific

### Tool Restrictions Not Working

**Problem:** Skill uses tools not in `allowed-tools`

**Diagnosis:**
1. Check YAML syntax for `allowed-tools`
2. Verify tool names correct
3. Check for skill inheritance issues

**Solutions:**
- Fix YAML formatting
- Use correct tool names (case-sensitive)
- Ensure project skill inherits restrictions

### Supporting Files Not Loading

**Problem:** Templates or checklists not accessible

**Diagnosis:**
1. Check file paths in SKILL.md
2. Verify files exist
3. Check markdown link syntax

**Solutions:**
- Use correct relative paths
- Ensure files committed to repo
- Fix markdown link syntax: `[text](path.md)`

### Inheritance Not Working

**Problem:** Project skill doesn't extend generic properly

**Diagnosis:**
1. Check `extends:` field syntax
2. Verify generic skill path
3. Check for conflicting instructions

**Solutions:**
- Fix extends path: `generic-skills/skill-name`
- Ensure generic skill exists
- Review conflict resolution logic

### Team Sync Issues

**Problem:** Skills work for one developer but not another

**Diagnosis:**
1. Check git sync status
2. Verify file permissions
3. Check skill locations

**Solutions:**
```bash
# Sync master-files
cd ~/.claude/master-files && git pull

# Sync project skills
cd /project && git pull

# Verify symlinks
ls -la .claude/master-files
```

---

## Appendix: Quick Reference

### Skill Creation Checklist

- [ ] Create skill directory with descriptive name
- [ ] Create SKILL.md with YAML frontmatter
- [ ] Write optimized description (with triggers)
- [ ] Define allowed-tools if restrictions needed
- [ ] Add supporting files if needed
- [ ] Test skill activation
- [ ] Test tool restrictions
- [ ] Document in README
- [ ] Commit to appropriate repo
- [ ] Share with team

### YAML Frontmatter Template

```yaml
---
name: Skill Display Name
description: [Action] for [Context]. Use when [Triggers]. [Ensures].
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, WebFetch, Task
extends: generic-skills/base-skill
version: 1.0.0
author: Your Name
---
```

### Directory Structure Template

```
skill-name/
├── SKILL.md
├── README.md
├── templates/
│   └── template-file.md
├── checklists/
│   └── checklist-file.md
├── reference/
│   └── reference-file.md
└── config/
    └── defaults.md
```

---

**Document End**

For additional resources:
- [SKILLS_VS_AGENTS_COMPARISON.md](SKILLS_VS_AGENTS_COMPARISON.md) - Detailed comparison
- [SKILLS_IMPLEMENTATION_GUIDE.md](SKILLS_IMPLEMENTATION_GUIDE.md) - How to create skills
- [SKILLS_MEMORY_INSTRUCTIONS.md](SKILLS_MEMORY_INSTRUCTIONS.md) - Memory file for Claude
- [templates/](templates/) - Skill templates and examples

**Version:** 1.0
**Last Updated:** October 19, 2025
**Maintained By:** Master-Files-Toolkit Team
