# Claude Skills System - Memory Instructions

**Document Type:** Permanent Memory File for Claude Code
**Purpose:** Instructions for Claude on how to use the Skills system
**Last Updated:** October 19, 2025

---

## 🎯 What This System Provides

This master-files repository contains a complete Claude Skills implementation including:

- **Comprehensive Documentation:** Deep dive guides, comparisons, implementation tutorials
- **Generic Skills:** 7 reusable skills that work across any project
- **Project Templates:** Templates for creating project-specific skills
- **Integration Patterns:** How skills work with agents and workflows

---

## 📂 Skill Discovery Locations

### Priority Order (Check in This Sequence)

1. **Project-Specific Skills** (HIGHEST PRIORITY)
   - **Location:** `.claude/skills/` (in current project directory)
   - **Purpose:** Project-customized skills with specific paths and configurations
   - **Example:** `.claude/skills/phase-planning/SKILL.md`
   - **When to Use:** Always check here FIRST for project overrides

2. **Generic Skills** (FALLBACK)
   - **Location:** `~/.claude/master-files/skills/generic-skills/`
   - **Purpose:** Reusable skills that work across any project with configuration
   - **Example:** `~/.claude/master-files/skills/generic-skills/phase-planning/SKILL.md`
   - **When to Use:** If no project-specific skill exists, use generic version

3. **Plugin Skills** (LOWEST PRIORITY)
   - **Location:** Bundled with installed plugins
   - **Purpose:** Plugin-specific capabilities
   - **When to Use:** Only if neither project nor generic skill exists

---

## 🔍 How Skills Are Selected

### Skill Resolution Logic

```
User Request: "Let's start planning Phase 3"

Step 1: Analyze Context
- Keywords: "planning", "Phase 3"
- Intent: User wants to plan a new phase
- Project: CA Lobby (or other project)

Step 2: Search Project-Specific Skills FIRST
- Check: .claude/skills/phase-planning/SKILL.md
- Found? YES → Use this (project-specific)
- Has extends: generic-skills/phase-planning?
  - YES → Load generic as base, apply project overrides
  - NO → Use project skill as-is

Step 3: Fallback to Generic (if Step 2 not found)
- Check: ~/.claude/master-files/skills/generic-skills/phase-planning/SKILL.md
- Found? YES → Use with placeholders (needs project config)
- Found? NO → No skill available

Step 4: Activate Best Match
- Load SKILL.md
- Parse YAML frontmatter
- Apply tool restrictions if specified
- Execute skill instructions
```

---

## 🚀 Skill Activation Priority

### When Multiple Skills Could Match

**Priority Rules:**

1. **Project-Specific** wins over Generic
   - Example: `.claude/skills/database-integration/` beats `~/.claude/master-files/skills/generic-skills/database-integration/`

2. **More Specific Description** wins over Generic
   - Skill with "BigQuery integration for CA Lobby lobby data" beats "Database integration for any project"

3. **Exact Keyword Match** wins over Partial
   - User says "create completion report" → Skill with "completion report" in description

4. **Context Alignment** matters
   - If in CA Lobby project and skill says "Use for CA Lobby", higher priority

---

## 📋 Available Generic Skills

### Core Workflow Skills

1. **phase-planning** (`generic-skills/phase-planning/`)
   - **Purpose:** Template-based phase planning for any project
   - **Triggers:** "start phase", "plan phase", "new phase"
   - **Provides:** Phase plan template, prerequisite verification
   - **Requires:** Project configuration for paths

2. **completion-report** (`generic-skills/completion-report/`)
   - **Purpose:** Generate completion reports from template
   - **Triggers:** "phase complete", "create completion report"
   - **Provides:** Standardized completion report with 12 sections
   - **Requires:** Project report structure configuration

3. **commit-strategy** (`generic-skills/commit-strategy/`)
   - **Purpose:** Generate conventional commit messages
   - **Triggers:** "commit", "commit message", "git commit"
   - **Provides:** Conventional commits format, commit templates
   - **Requires:** None (works immediately)

### Technical Skills

4. **database-integration** (`generic-skills/database-integration/`)
   - **Purpose:** Database integration patterns for SQL/NoSQL
   - **Triggers:** "database", "query", "schema", "migration"
   - **Provides:** PostgreSQL, BigQuery, MongoDB, MySQL patterns
   - **Requires:** Database type configuration

5. **cloud-deployment** (`generic-skills/cloud-deployment/`)
   - **Purpose:** Cloud deployment workflows for any platform
   - **Triggers:** "deploy", "deployment", "production"
   - **Provides:** Vercel, AWS, Azure, GCP deployment checklists
   - **Requires:** Cloud provider configuration

### Documentation Skills

6. **documentation-structure** (`generic-skills/documentation-structure/`)
   - **Purpose:** Organize project documentation
   - **Triggers:** "documentation", "organize docs", "doc structure"
   - **Provides:** Standard doc structure, README templates
   - **Requires:** Project documentation preferences

7. **testing-workflow** (`generic-skills/testing-workflow/`)
   - **Purpose:** Test execution and verification workflows
   - **Triggers:** "run tests", "testing", "test workflow"
   - **Provides:** Testing checklists, coverage requirements
   - **Requires:** Test framework configuration

---

## ⚠️ MANDATORY Usage Scenarios

### Critical: These Skills MUST ALWAYS Be Used

#### 1. **completion-report** Skill

**MANDATORY Trigger:** After EVERY phase completion, feature completion, or major milestone

**Why Mandatory:**
- Project requirement: Completion report MUST be created after every phase
- Ensures documentation never skipped
- Maintains project history
- Required before starting next phase

**When to Activate:**
- User says "phase complete", "finished phase", "done with phase"
- User attempts to start new phase without completing previous
- Major feature implementation finishes
- Before moving to next milestone

**Enforcement:**
```
If user tries to start new phase:
  1. Check for completion report from previous phase
  2. If missing → BLOCK and say:
     "⚠️ Previous phase missing completion report.
      Must create completion report before proceeding.
      Activating completion-report skill..."
  3. Activate completion-report skill
  4. Only proceed to new phase after report created
```

---

#### 2. **phase-planning** Skill

**MANDATORY Trigger:** BEFORE starting any new project phase

**Why Mandatory:**
- Project requirement: Must consult master plan before new phases
- Ensures prerequisites verified
- Prevents skipping planning steps
- Maintains project structure

**When to Activate:**
- User says "start phase", "begin phase", "plan phase"
- User requests "implement new feature" (without plan)
- User attempts implementation without documented plan

**Enforcement:**
```
If user wants to start new phase:
  1. Activate phase-planning skill FIRST
  2. Skill verifies:
     - Master plan consulted
     - Previous phase complete
     - Prerequisites met
     - Plan documented
  3. Get user approval on plan
  4. Only then proceed to implementation
```

---

#### 3. **master-plan-update** Skill (If Exists in Project)

**MANDATORY Trigger:** After completion report created

**Why Mandatory:**
- Project requirement: Master plan MUST stay synchronized
- Tracks project progress
- Updates phase statuses
- Links to completion reports

**When to Activate:**
- Immediately after completion-report skill finishes
- Before starting next phase
- When phase status changes

**Chain:**
```
completion-report skill completes
    ↓
Automatically trigger master-plan-update skill
    ↓
Master plan updated with:
    - Phase marked COMPLETED
    - Completion date added
    - Link to completion report
    - Next phase updated
```

---

## 🔄 Integration Pattern

### Skills + Agents: How They Work Together

#### Pattern 1: Skill Invokes Agent

**Scenario:** Protocol enforcement that requires complex implementation

```yaml
Phase Implementation Skill:

Step 1: Verify Plan Exists (Skill enforces)
  ↓
Step 2: Launch Implementation Agent (Agent executes)
  Task({
    subagent_type: "general-purpose",
    description: "Implement phase deliverables",
    prompt: "Follow plan and implement..."
  })
  ↓
Step 3: Validate Agent Output (Skill enforces)
  ↓
Step 4: Generate Completion Report (Skill enforces)
```

**When Claude Should Do This:**
- Skill detects complex multi-step implementation needed
- Skill has verified prerequisites
- Skill delegates implementation to agent
- Skill validates agent results
- Skill ensures protocol compliance (reports, docs)

---

#### Pattern 2: Agent Uses Skill Knowledge

**Scenario:** Agent references skill checklists during work

```typescript
Task({
  subagent_type: "general-purpose",
  description: "Deploy application",
  prompt: `Deploy following the cloud-deployment skill checklist.

  1. Read .claude/skills/cloud-deployment/SKILL.md
  2. Follow pre-deployment checklist
  3. Execute deployment
  4. Follow post-deployment checklist

  Use skill as authoritative guide.`
})
```

**When Claude Should Do This:**
- Agent needs standardized checklist
- Skill provides authoritative process
- Agent executes, skill guides

---

### Skills vs Agents Decision

**Use SKILL when:**
- ✅ Enforcing mandatory protocol
- ✅ Providing template or checklist
- ✅ Need tool restrictions (read-only)
- ✅ Quick workflow (< 5 min)
- ✅ Consistency critical

**Use AGENT when:**
- ✅ Complex multi-step implementation
- ✅ Need parallel execution
- ✅ Need isolated context
- ✅ Deep research/exploration
- ✅ Autonomous problem-solving

**Use BOTH (Hybrid) when:**
- ✅ Protocol enforcement + complex work
- ✅ Skill validates, agent executes
- ✅ Skill coordinates, agents work in parallel

---

## 📖 Documentation References

### Primary Documentation

1. **[CLAUDE_SKILLS_DEEP_DIVE.md](CLAUDE_SKILLS_DEEP_DIVE.md)**
   - Complete technical documentation
   - Architecture, file structure, YAML spec
   - Progressive disclosure, inheritance
   - Best practices, testing, troubleshooting

2. **[SKILLS_VS_AGENTS_COMPARISON.md](SKILLS_VS_AGENTS_COMPARISON.md)**
   - Detailed comparison: Skills vs Agents
   - When to use each
   - Integration patterns
   - Real-world examples

3. **[SKILLS_IMPLEMENTATION_GUIDE.md](SKILLS_IMPLEMENTATION_GUIDE.md)**
   - How to create skills step-by-step
   - Design, implement, test, deploy
   - Generic vs project-specific decision tree
   - Advanced patterns and techniques

### Templates

4. **[templates/SKILL_TEMPLATE.md](templates/SKILL_TEMPLATE.md)**
   - Basic skill template to copy
   - Standard structure
   - Commented examples

5. **[templates/SKILL_YAML_REFERENCE.md](templates/SKILL_YAML_REFERENCE.md)**
   - Complete YAML frontmatter reference
   - All available fields
   - Examples for each field type

---

## 🔧 Integration with Existing Master-Files

### How Skills Complement Other Master-Files Resources

**Skills** (NEW - Protocol Enforcement)
- **Purpose:** Enforce workflows, provide templates, restrict tools
- **Activation:** Automatic (model-invoked)
- **Use For:** Protocols, checklists, templates, compliance

**Agents** (EXISTING - Complex Execution)
- **Purpose:** Complex multi-step tasks, research, implementation
- **Activation:** Explicit via Task tool
- **Use For:** Implementation, research, autonomous problem-solving

**Workflows** (EXISTING - Session Management)
- **Purpose:** Opening/closing workflows, session archival
- **Activation:** Manual or scheduled
- **Use For:** Session start/end procedures

**Guides** (EXISTING - Reference Documentation)
- **Purpose:** Learning resources, deployment guides, troubleshooting
- **Activation:** Manual reference
- **Use For:** Learning, problem-solving, reference

### Combined Usage Example

```
Opening Workflow (EXISTING):
├─ Load project context
├─ Verify git status
└─ Ready for work

Phase Planning (NEW SKILL):
├─ Activate when user starts phase
├─ Enforce master plan consultation
├─ Generate phase plan from template
└─ Get user approval

Implementation (AGENT):
├─ Skill launches general-purpose agent
├─ Agent implements deliverables
├─ Agent returns results
└─ Skill validates

Completion (NEW SKILL):
├─ Activate when phase done
├─ Generate completion report
├─ Update master plan
└─ Trigger next phase

Closing Workflow (EXISTING):
├─ Archive session
├─ Sync master-files
└─ Update documentation
```

---

## 🎨 Skill Naming Conventions

### Generic Skills

**Location:** `~/.claude/master-files/skills/generic-skills/SKILL-NAME/`

**Naming:**
- Use kebab-case: `phase-planning`, `database-integration`
- Be descriptive: `cloud-deployment` not `deploy`
- Avoid abbreviations: `documentation-structure` not `doc-struct`
- Indicate scope: `phase-planning` (generic) not `ca-lobby-planning`

### Project Skills

**Location:** `.claude/skills/SKILL-NAME/`

**Naming:**
- Same name as generic if extending
- Add project prefix if unique: `ca-lobby-doc-navigator`
- Use kebab-case
- Be specific about project context in description, not name

**Why Same Names?**
- Skill inheritance pattern
- Project skill extends generic with same name
- Example:
  - Generic: `~/.claude/master-files/skills/generic-skills/phase-planning/`
  - Project: `.claude/skills/phase-planning/` (extends generic)

---

## ⚡ Quick Reference: When to Use Which Skill

### Workflow Skills

| User Says | Activate Skill | Purpose |
|-----------|----------------|---------|
| "Start new phase" | `phase-planning` | Enforce planning protocol |
| "Phase complete" | `completion-report` | Generate completion report |
| "Update master plan" | `master-plan-update` | Sync master plan |
| "Create commit" | `commit-strategy` | Generate commit message |

### Technical Skills

| User Says | Activate Skill | Purpose |
|-----------|----------------|---------|
| "Database query", "Schema" | `database-integration` | Database patterns |
| "Deploy", "Production" | `cloud-deployment` | Deployment workflow |
| "Run tests", "Testing" | `testing-workflow` | Test execution |
| "Documentation structure" | `documentation-structure` | Organize docs |

---

## 🔄 Synchronization

### How Skills Sync Across Projects and Machines

**Generic Skills:**
```bash
# On Machine A (after creating/updating skill)
cd ~/.claude/master-files
git add skills/generic-skills/new-skill
git commit -m "Add: new-skill"
git push

# On Machine B (sync)
cd ~/.claude/master-files
git pull
# Skill now available in ALL projects on Machine B
```

**Project Skills:**
```bash
# In CA Lobby project
git add .claude/skills/ca-lobby-skill
git commit -m "Add: ca-lobby-skill"
git push

# Teammate pulls
git pull
# Skill available in CA Lobby project only
```

**Daily Workflow:**
```bash
# Start of day - sync generic skills
cd ~/.claude/master-files && git pull

# Work in projects with latest skills
# All projects immediately have latest generic skills
```

---

## 🛡️ Compliance and Enforcement

### Skills as Guardrails

Skills are designed to PREVENT skipping mandatory steps:

**Example: Completion Report Enforcement**

```
Without Skill:
User: "Let's start Phase 3"
Claude: "Okay, let's plan Phase 3..."
[Previous phase completion report might be skipped]

With Skill (MANDATORY):
User: "Let's start Phase 3"
Claude:
  1. Checks for Phase 2 completion report
  2. If missing → "⚠️ Phase 2 missing completion report"
  3. Activates completion-report skill
  4. Generates report FIRST
  5. Then proceeds to Phase 3 planning
[Report cannot be skipped]
```

**Enforcement Mechanisms:**

1. **Automatic Activation**
   - Skills activate based on description matching
   - No user action required
   - Can't be "forgotten"

2. **Tool Restrictions**
   - `allowed-tools` prevents unintended operations
   - Read-only during planning (can't modify code)
   - Write-only during reports (can't delete)

3. **Prerequisite Checks**
   - Skills verify prerequisites before proceeding
   - Block if requirements not met
   - Clear error messages

4. **Chaining**
   - One skill triggers another
   - Ensures complete workflow
   - Example: completion-report → master-plan-update

---

## 📝 Summary for Claude

### Key Points to Remember

1. **Check Project Skills FIRST**
   - Always look in `.claude/skills/` before generic

2. **Use Skills for Protocol, Agents for Implementation**
   - Skills: Templates, checklists, enforcement
   - Agents: Complex work, research, autonomous tasks

3. **MANDATORY Skills Cannot Be Skipped**
   - `completion-report` after every phase
   - `phase-planning` before every phase
   - `master-plan-update` after completion reports

4. **Skills Can Invoke Agents**
   - Skill validates prerequisites
   - Skill launches agent for complex work
   - Skill validates agent output
   - Skill ensures documentation

5. **Progressive Disclosure**
   - Skills load supporting files only when needed
   - Saves context tokens
   - Faster activation

6. **Tool Restrictions Matter**
   - Planning skills: Read-only
   - Report skills: Read + Write
   - Review skills: Read-only
   - Implementation: Full access

7. **Inheritance Pattern**
   - Generic provides base structure
   - Project extends with specifics
   - Combined behavior = best of both

---

## 🎯 Action Items for Claude

When starting a conversation:

1. **Check for project skills** in `.claude/skills/`
2. **Know which skills are mandatory** (completion-report, phase-planning)
3. **Use skills automatically** based on user intent
4. **Invoke agents from skills** when complex work needed
5. **Enforce protocols** via skills (don't skip steps)
6. **Reference skill documentation** when uncertain

---

**Document End**

**Version:** 1.0
**Last Updated:** October 19, 2025
**Maintained By:** Master-Files-Toolkit Team

**Purpose:** This file provides permanent instructions for Claude on how to discover, activate, and use skills within the Claude Code environment. Skills are a critical part of protocol enforcement and workflow consistency.
