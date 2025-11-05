---
name: Testing Workflow
description: Test execution and verification workflows for any testing framework. Use when user says "run tests", "testing workflow", or before deployment. Ensures comprehensive testing coverage and verification.
allowed-tools: Read, Bash, Glob
version: 1.0.0
---

# Testing Workflow

## Purpose
Provide standardized testing workflows and verification checklists for projects using any testing framework.

## When This Activates
- User says "run tests", "test", "testing"
- Before deployment
- After implementation complete

## Steps

### Step 1: Detect Test Framework
Identify testing framework from project:
- Jest
- Pytest
- Mocha
- JUnit
- etc.

### Step 2: Execute Test Checklist
Run checklists/testing-checklist.md

### Step 3: Run Tests
Execute appropriate test command

### Step 4: Report Results
Summarize test results, coverage, failures

### Step 5: Suggest Fixes
If tests fail, suggest debugging approaches

---

## Supported Frameworks
- JavaScript: Jest, Mocha, Jasmine
- Python: Pytest, unittest
- Java: JUnit, TestNG
- Other: Framework-agnostic patterns

---

## Changelog
### Version 1.0.0 (2025-10-20)
- Initial release

---

**End of Skill**
