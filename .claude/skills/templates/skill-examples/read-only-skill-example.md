# Read-Only Skill Example

**Purpose:** Example of a read-only skill using `allowed-tools` restrictions
**Use Case:** Code review, analysis, research - operations that should not modify code

---

## Example: Code Review Checklist

### File: `.claude/skills/code-review/SKILL.md`

```yaml
---
name: Code Review Checklist
description: Review code changes against project standards and best practices. Use when reviewing pull requests, code changes, or conducting quality checks. Ensures consistent code quality.
allowed-tools: Read, Grep, Glob
version: 1.0.0
---

# Code Review Checklist

## Purpose
Provide consistent code review process following project standards

## When This Activates
- User says "review code", "code review", "check PR"
- User requests quality check
- Pull request review needed

## Tool Restrictions
This skill is READ-ONLY:
- ✅ Can: Read files, search code, find patterns
- ❌ Cannot: Modify files, create files, run commands

## Steps

### Step 1: Identify Files to Review
Use Glob to find changed files or user-specified files

**Actions:**
- List all files to review
- Categorize by type (frontend, backend, tests, docs)

---

### Step 2: Execute Review Checklist
For each file, check against standards:

#### Code Quality
- [ ] Naming conventions followed
  - Variables: camelCase
  - Functions: descriptive names
  - Classes: PascalCase
- [ ] Code properly formatted
- [ ] No commented-out code
- [ ] No console.log statements
- [ ] No debug code

#### Documentation
- [ ] Functions have comments
- [ ] Complex logic explained
- [ ] API endpoints documented
- [ ] README updated if needed

#### Testing
- [ ] Tests present for new features
- [ ] Edge cases covered
- [ ] Error cases tested
- [ ] Test names descriptive

#### Security
- [ ] No hardcoded credentials
- [ ] No sensitive data in code
- [ ] Input validation present
- [ ] Error messages don't leak info

#### Performance
- [ ] No obvious performance issues
- [ ] Efficient algorithms used
- [ ] Database queries optimized
- [ ] No unnecessary re-renders (React)

---

### Step 3: Generate Review Report
Create review report with findings

**Report Format:**
```markdown
# Code Review Report

**Date:** [Date]
**Files Reviewed:** [Count]
**Reviewer:** Claude Code Review Skill

## Summary
- ✅ Pass: [Count] items
- ⚠️ Warning: [Count] items
- ❌ Fail: [Count] items

## Detailed Findings

### [Filename]
- ✅ Naming conventions: PASS
- ❌ Documentation: FAIL - Missing function comments
- ⚠️ Testing: WARNING - Edge cases not fully covered

### [Filename]
...

## Recommendations
1. [Recommendation 1]
2. [Recommendation 2]
...

## Overall Assessment
[APPROVE / REQUEST CHANGES / REJECT]
```

---

## Output
Review report with:
- Checklist results
- Specific issues found
- Recommendations
- Overall assessment

## Example Usage

**User Says:**
```
"Review the authentication code in src/auth/"
```

**Skill Activates:**
1. Finds all files in src/auth/
2. Executes review checklist on each
3. Generates report:

```markdown
# Code Review Report

**Files Reviewed:** 3
- src/auth/login.js
- src/auth/signup.js
- src/auth/middleware.js

## Findings

### src/auth/login.js
- ✅ Naming conventions: PASS
- ✅ Code formatting: PASS
- ❌ Security: FAIL - Password sent in plain text on line 42
- ⚠️ Error handling: WARNING - Generic error messages could be more specific

### src/auth/signup.js
- ✅ Naming conventions: PASS
- ❌ Validation: FAIL - Email validation missing
- ✅ Testing: PASS - Good test coverage

### src/auth/middleware.js
- ✅ All checks: PASS

## Critical Issues
1. **SECURITY**: Password transmission not encrypted (login.js:42)
2. **VALIDATION**: Email validation missing (signup.js:28)

## Recommendations
1. Use bcrypt for password hashing before transmission
2. Add email validation using regex or library
3. Improve error messages for better user experience

## Overall Assessment
**REQUEST CHANGES** - Security issues must be addressed before merge
```

---

## Why Read-Only?

**Benefits of `allowed-tools: Read, Grep, Glob`:**

1. **Safety:** Cannot accidentally modify code during review
2. **Compliance:** Enforces read-only policy for reviews
3. **Trust:** Reviewers can use skill knowing it won't change anything
4. **Security:** Prevents unintended edits

**What Happens If Skill Tries to Edit?**

```
Skill: [Attempts to use Edit tool to fix issue]

System: ❌ Error: Edit tool not allowed in this skill
        Skill is restricted to: Read, Grep, Glob

Skill: [Falls back to reporting issue instead of fixing]
```

---

## Notes

- This skill reports issues but doesn't fix them
- For automated fixes, use a different skill with Edit access
- Read-only ensures compliance with "review but don't modify" workflow

---

**Example Version:** 1.0.0
**Last Updated:** October 19, 2025
