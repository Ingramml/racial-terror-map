# Skills vs Agents: Comprehensive Comparison Guide

**Document Version:** 1.0
**Last Updated:** October 19, 2025
**Purpose:** Detailed comparison between Claude Skills and Claude Agents
**Audience:** Developers, architects, team leads

---

## Table of Contents

1.  [Executive Summary](#executive-summary)
2.  [Architectural Differences](#architectural-differences)
3.  [Context Management](#context-management)
4.  [Invocation Methods](#invocation-methods)
5.  [Use Case Comparison](#use-case-comparison)
6.  [Performance Characteristics](#performance-characteristics)
7.  [When to Use Skills vs Agents](#when-to-use-skills-vs-agents)
8.  [Integration Patterns](#integration-patterns)
9.  [Decision Flowchart](#decision-flowchart)
10. [Real-World Examples](#real-world-examples)
11. [Hybrid Approaches](#hybrid-approaches)
12. [Best Practices](#best-practices)

---

## Executive Summary

### Quick Comparison

| What You Need             | Use This | Why |
|---------------|----------|-----|
| Enforce project protocols | **SKILL** | Automatic activation, templates, checklists |
| Complex multi-step implementation | **AGENT** | Isolated context, parallel execution |
| Read-only research | **SKILL** | Tool restrictions, protocol enforcement |
| Parallel codebase exploration | **AGENT** | Multiple contexts simultaneously |
| Template-based generation | **SKILL** | Progressive disclosure, consistency |
| Specialized expertise | **AGENT** | Deep domain knowledge, autonomous operation |
| Step-by-step checklist | **SKILL** | Guided workflow, compliance |
| Background task execution | **AGENT** | Non-blocking, separate context |

### Core Philosophy

**SKILLS:**
- **Purpose:** Guide and constrain
- **Metaphor:** Checklist, template, guardrails
- **Best For:** Consistency and compliance

**AGENTS:**
- **Purpose:** Execute and deliver
- **Metaphor:** Specialist consultant, autonomous worker
- **Best For:** Complex problem-solving

---

## Architectural Differences

### System Architecture

#### SKILLS Architecture

```
Main Conversation Context
│
├─ User Request
│  ↓
├─ Claude Analyzes Context
│  ↓
├─ Skill Discovery (automatic)
│  ├─ Searches .claude/skills/
│  ├─ Searches ~/.claude/master-files/skills/generic-skills/
│  └─ Matches description to context
│  ↓
├─ Skill Activation (in same context)
│  ├─ Loads SKILL.md
│  ├─ Applies allowed-tools restrictions
│  └─ Follows instructions
│  ↓
├─ Execution (sequential, same conversation)
│  ├─ Uses only allowed tools
│  ├─ Loads supporting files progressively
│  └─ May invoke agents if needed
│  ↓
└─ Result (inline in conversation)
   └─ Continues in main conversation
```

**Context:** Single, shared conversation context
**Invocation:** Automatic (model-invoked)
**Parallelization:** Sequential only (same context)
**Tool Access:** Restricted via `allowed-tools`

---

#### AGENTS Architecture

```
Main Conversation Context
│
├─ User/Skill Explicitly Launches Agent
│  ↓
├─ Task Tool Called
│  ├─ subagent_type: "general-purpose"
│  ├─ description: "Task summary"
│  └─ prompt: "Detailed instructions"
│  ↓
├─ New Isolated Context Created
│  │
│  ├─ Agent Context (separate from main)
│  │  ├─ Agent receives prompt
│  │  ├─ Has full tool access (or restricted)
│  │  ├─ Operates autonomously
│  │  └─ No access to main conversation
│  │  ↓
│  └─ Agent Execution
│     ├─ Multi-step autonomous work
│     ├─ Complex problem-solving
│     └─ Generates final report
│     ↓
│  Agent Returns Results
│  ↓
└─ Results Merged into Main Conversation
   └─ Main conversation continues with results
```

**Context:** Separate, isolated context per agent
**Invocation:** Explicit via Task tool
**Parallelization:** Multiple agents in parallel
**Tool Access:** Full access (configurable)

---

### Storage & Organization

#### SKILLS Storage

```
Generic Skills (Reusable):
~/.claude/master-files/skills/generic-skills/
├── phase-planning/
│   ├── SKILL.md                    # YAML + instructions
│   └── templates/
│       └── phase-plan-template.md
├── completion-report/
│   ├── SKILL.md
│   └── templates/
│       └── report-template.md
└── database-integration/
    ├── SKILL.md
    └── reference/
        └── patterns.md

Project Skills (Project-specific):
/project/.claude/skills/
├── phase-planning/
│   ├── SKILL.md                    # Extends generic
│   └── config/
│       └── ca-lobby-phases.md
└── custom-skill/
    └── SKILL.md                    # Project-unique
```

**File Format:** YAML frontmatter + Markdown
**Discovery:** Via `description` field matching
**Scope:** Focused single capability

---

#### AGENTS Storage

```
Generic Agents (Reusable):
~/.claude/master-files/agents/
├── deployment/
│   ├── deployment-orchestrator.md
│   └── vercel-deployment.md
├── web-development/
│   ├── react-specialist.md
│   ├── nextjs-specialist.md
│   └── typescript-specialist.md
└── workflow-management/
    ├── general-purpose.md
    └── session-archiver.md

Project Agents (Project-specific):
/project/.claude/agents/
└── ca-lobby-specialist.md
```

**File Format:** YAML frontmatter + Markdown
**Discovery:** Via `subagent_type` parameter
**Scope:** Broader multi-capability

---

## Context Management

### How Skills Use Context

**Single Shared Context:**
```
Token Budget: 200,000 tokens (example)

Main Conversation:
├── User messages: 5,000 tokens
├── Claude responses: 10,000 tokens
├── Code files read: 20,000 tokens
│
├── Skill Activation (same context):
│   ├── SKILL.md loaded: +2,000 tokens
│   ├── Template loaded: +1,000 tokens
│   └── Skill execution: +3,000 tokens
│
└── Remaining: 159,000 tokens
```

**Impact:**
- ✅ All context preserved in main conversation
- ✅ Skill has access to entire conversation history
- ⚠️ Uses main conversation's token budget
- ⚠️ Cannot parallelize (sequential only)

---

### How Agents Use Context

**Separate Isolated Contexts:**
```
Main Conversation Context:
├── User messages: 5,000 tokens
├── Claude responses: 10,000 tokens
├── Code files read: 20,000 tokens
└── Remaining: 165,000 tokens
    (Agent does NOT consume from main context)

Agent Context 1 (Separate):
├── Agent prompt: 2,000 tokens
├── Agent research: 50,000 tokens
├── Agent implementation: 30,000 tokens
└── Agent report: 3,000 tokens
    Total: 85,000 tokens (separate budget)

Agent Context 2 (Parallel, Separate):
├── Agent prompt: 2,000 tokens
├── Agent research: 40,000 tokens
└── Agent report: 2,000 tokens
    Total: 44,000 tokens (separate budget)

Results Returned to Main:
├── Agent 1 report: +3,000 tokens
└── Agent 2 report: +2,000 tokens
    Main Context Now: 170,000 tokens used
```

**Impact:**
- ✅ Main context preserved (agents don't consume main budget)
- ✅ Can run multiple agents in parallel
- ✅ Agent has clean isolated context
- ⚠️ Agent cannot see main conversation history
- ⚠️ Must pass all needed info in agent prompt

---

## Invocation Methods

### Skill Invocation (Automatic)

**Model-Invoked Pattern:**

```yaml
# Skill: phase-planning/SKILL.md
---
name: Phase Planning
description: Enforce phase planning protocol. Use when user requests to start a new phase, create implementation plan, or begin major feature. Ensures master plan consultation.
---
```

**User Says:**
```
"Let's start planning Phase 3"
```

**What Happens:**
```
1. Claude analyzes: "planning Phase 3"
2. Searches skills for matching description
3. Finds: "Use when user requests to start a new phase"
4. Activates skill automatically
5. Follows skill instructions
6. Returns result in conversation
```

**User Control:** None (automatic)
**Transparency:** Claude may mention skill activation
**Override:** User can request different approach

---

### Agent Invocation (Explicit)

**Task Tool Pattern:**

```typescript
Task({
  subagent_type: "general-purpose",
  description: "Implement authentication system",
  prompt: `Act as a React specialist and implement a complete
           authentication system with Clerk integration...

           Requirements:
           - Login component
           - Signup component
           - Protected routes
           - Session management

           Return: Implementation summary with file paths`
})
```

**User Says:**
```
"Implement authentication with Clerk"
```

**What Happens:**
```
1. Claude analyzes task complexity
2. Determines agent needed
3. Explicitly calls Task tool
4. Launches general-purpose agent
5. Agent works in isolated context
6. Agent returns results
7. Claude presents results to user
```

**User Control:** Full (can request agent explicitly)
**Transparency:** User sees agent launch message
**Override:** User can cancel or redirect

---

## Use Case Comparison

### Protocol Enforcement

#### Use SKILLS for Protocol Enforcement

**Scenario:** Ensure every phase has a completion report

**Skill Solution:**
```yaml
---
name: Completion Report Enforcement
description: Automatically generate completion report when phase completes. Use when user finishes phase, says "phase complete", or moves to next phase.
allowed-tools: Read, Write
---

# Completion Report Skill

## Trigger Detection
Activates when user indicates phase completion

## Mandatory Steps (Cannot be skipped)
1. Read Documentation/PhaseX/Reports/ for template
2. Gather completion data
3. Generate report
4. Verify all 12 sections present
5. Write to Reports/ directory
6. Trigger master-plan-update skill

## Compliance
This skill ensures project requirement:
"Completion report MUST be created after EVERY phase"
```

**Why Skill?**
- ✅ Activates automatically (can't forget)
- ✅ Enforces mandatory steps
- ✅ Uses allowed-tools for compliance
- ✅ Template-based consistency

**Agent Alternative:**
```
User: "Create a completion report"
[Manually invoke agent each time]
```
❌ Requires manual invocation
❌ Can be forgotten
❌ No automatic enforcement

---

### Complex Implementation

#### Use AGENTS for Complex Implementation

**Scenario:** Implement complete authentication system

**Agent Solution:**
```typescript
Task({
  subagent_type: "general-purpose",
  description: "Implement Clerk authentication",
  prompt: `Implement complete Clerk authentication system:

  1. Install Clerk dependencies
  2. Create ClerkProvider wrapper
  3. Build login component with validation
  4. Build signup component with error handling
  5. Implement protected routes
  6. Add session management
  7. Create user profile page
  8. Add authentication middleware
  9. Test all flows
  10. Document implementation

  Use React best practices, TypeScript types, and error boundaries.

  Return: Summary of all files created/modified with implementation details.`
})
```

**Why Agent?**
- ✅ Isolated context for complex work
- ✅ Autonomous multi-step execution
- ✅ Can research and implement
- ✅ Returns clean summary

**Skill Alternative:**
```yaml
# Complex implementation skill
Manually follow 10-step checklist...
```
❌ Would consume main context heavily
❌ Sequential only (can't parallelize research)
❌ Less autonomous

---

### Research & Analysis

#### Use SKILLS for Guided Research

**Scenario:** Code review checklist

**Skill Solution:**
```yaml
---
name: Code Review Checklist
description: Review code changes against project standards. Use when reviewing PRs, code changes, or conducting quality checks.
allowed-tools: Read, Grep, Glob
---

# Code Review Skill (Read-Only)

## Checklist
1. [ ] Naming conventions followed
2. [ ] Documentation present
3. [ ] Tests included
4. [ ] Error handling implemented
5. [ ] No hardcoded values
6. [ ] Security best practices

## Process
For each item:
- Read relevant files
- Search for patterns
- Mark pass/fail
- Provide recommendations

## Output
Review report with findings
```

**Why Skill?**
- ✅ Read-only (allowed-tools: Read, Grep, Glob)
- ✅ Consistent checklist every time
- ✅ Template-based reporting
- ✅ Protocol compliance

---

#### Use AGENTS for Deep Research

**Scenario:** Research best authentication patterns across codebase

**Agent Solution:**
```typescript
Task({
  subagent_type: "Explore",
  description: "Research authentication patterns",
  prompt: `Research how authentication is currently implemented:

  1. Find all auth-related files
  2. Analyze patterns used
  3. Identify inconsistencies
  4. Review security practices
  5. Check for vulnerabilities
  6. Compare with industry best practices
  7. Generate recommendations report

  Be thorough - search across entire codebase.

  Return: Comprehensive analysis with findings and recommendations.`
})
```

**Why Agent?**
- ✅ Isolated context for extensive research
- ✅ Can explore entire codebase without cluttering main context
- ✅ Autonomous deep analysis
- ✅ Parallel exploration possible

---

### Template Generation

#### Use SKILLS for Template Generation

**Scenario:** Generate standardized phase plan

**Skill Solution:**
```yaml
---
name: Phase Planning
description: Generate phase plans from template. Use when starting new phases.
allowed-tools: Read, Write
---

# Phase Planning Skill

## Step 1: Load Template
Read templates/phase-plan-template.md

## Step 2: Gather Info
- Phase name
- Objectives
- Deliverables
- Timeline

## Step 3: Populate Template
Replace placeholders:
- {{PHASE_NAME}}
- {{OBJECTIVES}}
- {{DELIVERABLES}}
- {{TIMELINE}}

## Step 4: Write Plan
Write to: Documentation/PhaseX/Plans/{{PHASE_NAME}}_PLAN.md

## Output
Standardized phase plan following project template
```

**Why Skill?**
- ✅ Template-based consistency
- ✅ Progressive disclosure (loads template only when needed)
- ✅ Enforces project structure
- ✅ Quick generation

---

### Deployment Workflows

#### Use SKILLS for Deployment Checklists

**Scenario:** Pre-deployment verification

**Skill Solution:**
```yaml
---
name: Pre-Deployment Checklist
description: Verify deployment readiness. Use before deploying to production.
allowed-tools: Read, Bash, Glob
---

# Pre-Deployment Checklist Skill

## Verification Steps
1. [ ] All tests passing (run: npm test)
2. [ ] Build succeeds (run: npm run build)
3. [ ] Environment variables configured
4. [ ] No console.log in production code
5. [ ] Dependencies up to date
6. [ ] Security audit clean
7. [ ] Documentation updated

## Process
Execute each check, report status

## Output
GO/NO-GO decision with blockers listed
```

**Why Skill?**
- ✅ Consistent checklist enforcement
- ✅ Can execute verification commands
- ✅ Clear GO/NO-GO output
- ✅ Protocol compliance

---

#### Use AGENTS for Deployment Execution

**Scenario:** Complex multi-environment deployment

**Agent Solution:**
```typescript
Task({
  subagent_type: "general-purpose",
  description: "Execute multi-stage deployment",
  prompt: `Deploy application to staging then production:

  Stage 1 - Staging:
  1. Deploy to staging environment
  2. Run smoke tests
  3. Verify health checks
  4. Monitor logs for errors

  Stage 2 - Production (if staging successful):
  1. Deploy to production
  2. Run smoke tests
  3. Verify health checks
  4. Monitor metrics

  If any stage fails:
  1. Execute rollback
  2. Report failure details

  Return: Deployment report with all steps and results.`
})
```

**Why Agent?**
- ✅ Autonomous multi-stage execution
- ✅ Can handle complex decision logic
- ✅ Isolated context for deployment operations
- ✅ Clean reporting back to main conversation

---

## Performance Characteristics

### Skills Performance

| Characteristic | Performance | Notes |
|----------------|-------------|-------|
| **Activation Time** | < 1 second | Fast discovery and loading |
| **Context Usage** | Minimal (progressive disclosure) | 200-2,000 tokens typical |
| **Parallelization** | None | Sequential in same context |
| **Main Context Impact** | Medium | Uses main conversation tokens |
| **Best For** | Quick workflows | Templates, checklists, protocols |

**Performance Pattern:**
```
User Request → Skill Discovery (0.5s) → Activation (0.2s) → Execution (varies)
Total: ~1s + execution time
```

---

### Agents Performance

| Characteristic | Performance | Notes |
|----------------|-------------|-------|
| **Activation Time** | 1-3 seconds | Context creation overhead |
| **Context Usage** | High (isolated context) | 5,000-100,000 tokens typical |
| **Parallelization** | Full | Multiple agents simultaneously |
| **Main Context Impact** | Low | Only final report counts |
| **Best For** | Complex tasks | Implementation, research, analysis |

**Performance Pattern:**
```
Task Tool Call → Agent Context Creation (1-2s) → Agent Work (varies) → Report Return (1s)
Total: 2-3s + agent work time

Parallel Example:
├─ Agent 1: Research (30s) ┐
├─ Agent 2: Implementation (45s) ├─ All run simultaneously
└─ Agent 3: Testing (25s) ┘
Total: 45s (longest agent) not 100s (sequential)
```

---

## When to Use Skills vs Agents

### Decision Matrix

| Requirement | Use SKILL | Use AGENT |
|-------------|-----------|-----------|
| Enforce mandatory protocol | ✅ | ❌ |
| Provide template/checklist | ✅ | ❌ |
| Restrict tool access | ✅ | ⚠️ |
| Quick reference workflow | ✅ | ❌ |
| Complex multi-step implementation | ❌ | ✅ |
| Parallel task execution | ❌ | ✅ |
| Isolated context needed | ❌ | ✅ |
| Deep research/exploration | ⚠️ | ✅ |
| Autonomous problem-solving | ❌ | ✅ |
| Background task execution | ❌ | ✅ |
| Preserve main conversation context | ✅ | ✅ |
| Team protocol consistency | ✅ | ⚠️ |

Legend:
- ✅ Strongly recommended
- ⚠️ Possible but not ideal
- ❌ Not suitable

---

## Integration Patterns

### Pattern 1: Skill Invokes Agent

**Use Case:** Protocol enforcement that requires complex implementation

```yaml
---
name: Phase Implementation Skill
description: Execute phase implementation following protocol
---

# Phase Implementation Skill

## Step 1: Verify Plan Exists (Skill enforces)
Read Documentation/PhaseX/Plans/{{PHASE}}_PLAN.md
If missing → Error: "Must create plan first"

## Step 2: Launch Implementation Agent
[Skill invokes agent for complex work]

Task({
  subagent_type: "general-purpose",
  description: "Implement {{PHASE}}",
  prompt: "Follow plan at Documentation/PhaseX/Plans/{{PHASE}}_PLAN.md
           Implement all deliverables..."
})

## Step 3: Validate Agent Output (Skill enforces)
Check:
- All deliverables implemented
- Tests passing
- Documentation updated

## Step 4: Generate Completion Report (Skill enforces)
[Trigger completion-report skill]
```

**Benefits:**
- ✅ Skill ensures protocol (plan exists, validation, reporting)
- ✅ Agent handles complex implementation
- ✅ Best of both worlds

---

### Pattern 2: Agent Uses Skill Knowledge

**Use Case:** Agent references skill checklists/templates

```typescript
Task({
  subagent_type: "general-purpose",
  description: "Deploy application",
  prompt: `Deploy application following deployment skill checklist.

  1. Read .claude/skills/cloud-deployment/SKILL.md
  2. Follow pre-deployment checklist
  3. Execute deployment
  4. Follow post-deployment checklist

  Use skill as authoritative guide.

  Return: Deployment report with checklist completion status.`
})
```

**Benefits:**
- ✅ Agent has autonomy for execution
- ✅ Skill provides authoritative checklist
- ✅ Consistency maintained

---

### Pattern 3: Parallel Agents with Skill Coordination

**Use Case:** Multiple complex tasks coordinated by skill

```yaml
---
name: Release Workflow Skill
description: Coordinate release across multiple workstreams
---

# Release Workflow Skill

## Step 1: Launch Parallel Agents

Agent 1 - Testing:
Task({description: "Run full test suite", ...})

Agent 2 - Documentation:
Task({description: "Generate release docs", ...})

Agent 3 - Deployment:
Task({description: "Deploy to staging", ...})

## Step 2: Collect Results (Skill coordinates)
Wait for all agents to complete

## Step 3: Validate Combined Results (Skill enforces)
- All tests passing?
- Documentation complete?
- Deployment successful?

## Step 4: Make GO/NO-GO Decision (Skill decides)
If all pass → Proceed to production
If any fail → Halt and report blockers
```

**Benefits:**
- ✅ Parallel execution (faster)
- ✅ Skill coordinates workflow
- ✅ Protocol enforcement maintained

---

## Decision Flowchart

```
START: Need to accomplish task
    ↓
┌───────────────────────────────┐
│ Does task require enforcing   │ YES → USE SKILL
│ mandatory protocol/checklist? │
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Does task need tool           │ YES → USE SKILL
│ restrictions (read-only)?     │       (with allowed-tools)
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Is task template-based        │ YES → USE SKILL
│ generation with consistency?  │
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Is task complex multi-step    │ YES → USE AGENT
│ implementation?               │
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Does task need parallel       │ YES → USE AGENT(S)
│ execution?                    │       (multiple in parallel)
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Does task need isolated       │ YES → USE AGENT
│ context (preserve main)?      │
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Is task deep research/        │ YES → USE AGENT
│ exploration?                  │       (Explore type)
└───────────────┬───────────────┘
                │ NO
                ↓
┌───────────────────────────────┐
│ Is task quick reference       │ YES → USE SKILL
│ or guidance?                  │
└───────────────┬───────────────┘
                │ NO
                ↓
          DEFAULT → USE SKILL
          (simpler, automatic activation)
```

---

## Real-World Examples

### Example 1: CA Lobby Phase Planning

**Requirement:** Start new phase following strict protocol

**Solution: SKILL (phase-planning)**

**Why:**
- ✅ Must enforce protocol (master plan consultation)
- ✅ Template-based (phase plan structure)
- ✅ Automatic activation (user says "start phase")
- ✅ Read-only during planning (allowed-tools: Read, Glob)
- ✅ Quick workflow (< 5 minutes)

**Implementation:**
```yaml
---
name: CA Lobby Phase Planning
description: Enforce phase planning protocol. Use when starting new CA Lobby phases.
allowed-tools: Read, Glob, WebFetch
extends: generic-skills/phase-planning
---

Ensures:
1. Master plan consulted (MANDATORY)
2. Previous phase completion report exists
3. Phase plan follows template
4. Prerequisites verified
```

---

### Example 2: Authentication System Implementation

**Requirement:** Implement Clerk authentication in CA Lobby

**Solution: AGENT (general-purpose)**

**Why:**
- ✅ Complex multi-step implementation
- ✅ Needs research (Clerk docs, best practices)
- ✅ Multiple files to create/modify
- ✅ Autonomous problem-solving needed
- ✅ Isolated context preserves main conversation

**Implementation:**
```typescript
Task({
  subagent_type: "general-purpose",
  description: "Implement Clerk authentication",
  prompt: `Implement complete Clerk authentication:
  1. Research Clerk + React integration patterns
  2. Install dependencies
  3. Create ClerkProvider wrapper
  4. Build login/signup components
  5. Implement protected routes
  6. Add session management
  7. Test all flows

  Return: Implementation summary with file paths`
})
```

---

### Example 3: Code Review Before Merge

**Requirement:** Review PR against project standards

**Solution: SKILL (code-review-checklist)**

**Why:**
- ✅ Consistent checklist every time
- ✅ Read-only (no modifications during review)
- ✅ Protocol enforcement
- ✅ Template-based report

**Implementation:**
```yaml
---
name: Code Review Checklist
description: Review code changes. Use when reviewing PRs or code quality checks.
allowed-tools: Read, Grep, Glob
---

Checklist:
1. [ ] Naming conventions
2. [ ] Documentation
3. [ ] Tests included
4. [ ] Error handling
5. [ ] No hardcoded values
6. [ ] Security practices

Output: Review report with pass/fail for each item
```

---

### Example 4: Parallel Codebase Exploration

**Requirement:** Research authentication patterns across entire codebase

**Solution: AGENTS (multiple Explore agents in parallel)**

**Why:**
- ✅ Need parallel execution (faster)
- ✅ Isolated contexts (don't clutter main)
- ✅ Deep exploration required
- ✅ Multiple search strategies simultaneously

**Implementation:**
```typescript
// Launch 3 parallel agents
Agent 1: Task({subagent_type: "Explore", prompt: "Find all auth components"})
Agent 2: Task({subagent_type: "Explore", prompt: "Find all API endpoints using auth"})
Agent 3: Task({subagent_type: "Explore", prompt: "Find all auth configuration files"})

// All run simultaneously, return combined results
```

---

### Example 5: Deployment Workflow

**Requirement:** Deploy to production with verification

**Solution: HYBRID (Skill + Agent)**

**Skill (pre-deployment-checklist):**
```yaml
---
name: Pre-Deployment Checklist
description: Verify deployment readiness
allowed-tools: Read, Bash, Glob
---

Checklist:
1. Run tests → npm test
2. Build → npm run build
3. Verify env vars
4. Security audit
5. Documentation updated

Output: GO/NO-GO decision
```

**Agent (deployment-execution):**
```typescript
Task({
  subagent_type: "general-purpose",
  description: "Execute deployment",
  prompt: `Deploy to production:
  1. Deploy to Vercel
  2. Run smoke tests
  3. Verify health checks
  4. Monitor for errors

  If issues: Execute rollback

  Return: Deployment report`
})
```

**Why Hybrid:**
- ✅ Skill enforces pre-deployment protocol (can't skip)
- ✅ Agent handles complex deployment execution
- ✅ Skill validates post-deployment
- ✅ Best of both worlds

---

## Hybrid Approaches

### Pattern: Skill → Agent → Skill

**Workflow:**
```
1. Skill enforces prerequisites
   ↓
2. Skill launches agent for complex work
   ↓
3. Agent executes autonomously
   ↓
4. Agent returns results
   ↓
5. Skill validates results
   ↓
6. Skill enforces post-processing (reports, documentation)
```

**Example: Complete Phase Workflow**

```yaml
---
name: Complete Phase Workflow
description: End-to-end phase execution with protocol enforcement
---

# Complete Phase Workflow Skill

## Phase 1: Planning (Skill enforces)
- Verify master plan consulted
- Create phase plan from template
- Get user approval

## Phase 2: Implementation (Agent executes)
Launch implementation agent:
Task({
  description: "Implement phase deliverables",
  prompt: "Follow phase plan and implement all deliverables..."
})

## Phase 3: Validation (Skill enforces)
- Verify all deliverables implemented
- Run tests
- Check documentation

## Phase 4: Reporting (Skill enforces)
- Generate completion report
- Update master plan
- Archive session

## Phase 5: Deployment (Agent executes if needed)
If deployment required:
  Task({description: "Deploy changes", ...})
```

**Benefits:**
- ✅ Protocol never skipped (skill enforces)
- ✅ Complex work delegated (agent executes)
- ✅ Validation guaranteed (skill checks)
- ✅ Documentation automatic (skill generates)

---

## Best Practices

### For Skills

**DO:**
- ✅ Focus on single capability
- ✅ Optimize description for discovery
- ✅ Use allowed-tools for restrictions
- ✅ Provide templates and checklists
- ✅ Make activation automatic via good description
- ✅ Use progressive disclosure for large docs

**DON'T:**
- ❌ Create mega-skills with multiple purposes
- ❌ Require user to manually invoke
- ❌ Include complex implementation logic
- ❌ Load all supporting files at once
- ❌ Hardcode project-specific paths in generic skills

---

### For Agents

**DO:**
- ✅ Provide detailed, specific prompts
- ✅ Use for complex multi-step tasks
- ✅ Leverage isolated context
- ✅ Run multiple agents in parallel when possible
- ✅ Specify expected output format
- ✅ Include error handling instructions

**DON'T:**
- ❌ Use for simple tasks (overhead not worth it)
- ❌ Launch agents for protocol enforcement
- ❌ Assume agent has main conversation context
- ❌ Forget to specify what to return
- ❌ Use agents when skill restrictions needed

---

### For Integration

**DO:**
- ✅ Let skills invoke agents for complex work
- ✅ Have agents reference skill checklists
- ✅ Use skills for protocol, agents for execution
- ✅ Coordinate multiple agents with skills
- ✅ Validate agent output with skills

**DON'T:**
- ❌ Try to enforce protocols with agents
- ❌ Use skills for complex implementation
- ❌ Duplicate logic between skills and agents
- ❌ Forget which has which responsibility

---

## Summary Decision Guide

### Quick Reference

**Need to enforce a protocol or checklist?**
→ **SKILL** (automatic, consistent, template-based)

**Need to implement something complex?**
→ **AGENT** (autonomous, isolated context, can research)

**Need to restrict tool access?**
→ **SKILL** (allowed-tools field)

**Need to run tasks in parallel?**
→ **AGENTS** (multiple simultaneous)

**Need template-based generation?**
→ **SKILL** (progressive disclosure, consistency)

**Need deep codebase exploration?**
→ **AGENT** (isolated context, autonomous)

**Need both protocol AND complex work?**
→ **HYBRID** (skill invokes agent, validates results)

---

**Document End**

For more information:
- [CLAUDE_SKILLS_DEEP_DIVE.md](CLAUDE_SKILLS_DEEP_DIVE.md) - Complete skills documentation
- [SKILLS_IMPLEMENTATION_GUIDE.md](SKILLS_IMPLEMENTATION_GUIDE.md) - How to create skills
- [Sub-agents Guide](../agents/sub-agents-guide.md) - Agent documentation

**Version:** 1.0
**Last Updated:** October 19, 2025
**Maintained By:** Master-Files-Toolkit Team
