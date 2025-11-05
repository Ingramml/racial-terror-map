---
name: Cloud Deployment
description: Cloud deployment workflows for Vercel, AWS, Azure, and GCP. Use before deploying, when user says "deploy" or "ready to deploy". Ensures verification at each stage with comprehensive checklists.
allowed-tools: Read, Bash, Glob
version: 1.0.0
---

# Cloud Deployment

## Purpose
Provide deployment verification checklists and workflows for major cloud platforms.

## When This Activates
- User says "deploy", "deployment", "ready to deploy"
- User mentions cloud platform (Vercel, AWS, Azure, GCP)
- Before production deployment

## Steps

### Step 1: Detect Cloud Provider
Identify target platform from project config or ask user

### Step 2: Execute Pre-Deployment Checklist
Run checklists/pre-deployment-checklist.md

### Step 3: Deploy
Execute deployment (if authorized)

### Step 4: Post-Deployment Verification
Run checklists/post-deployment-checklist.md

### Step 5: Monitor
Verify health checks, logs, metrics

---

## Supported Platforms
- Vercel
- AWS (EC2, Lambda, ECS)
- Azure (App Service, Functions)
- Google Cloud (App Engine, Cloud Run)

---

## Changelog
### Version 1.0.0 (2025-10-20)
- Initial release

---

**End of Skill**
