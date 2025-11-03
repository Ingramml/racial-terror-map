---
description: Initialize project session and sync skills
---

You are starting a new Claude Code session for this project.

# Session Initialization Tasks

## 1. Create Skills Directory

First, ensure the active skills directory exists:

```bash
mkdir -p .claude/skills
```

## 2. Sync Skills from Master-Files

Compare `.claude/skills/` with `.claude/master-files/skills/` and copy any new project-specific skills.

### Skip These Documentation Files:
- SKILL_TEMPLATE.md
- SKILL_YAML_REFERENCE.md
- CLAUDE_SKILLS_DEEP_DIVE.md
- SKILLS_VS_AGENTS_COMPARISON.md
- SKILLS_IMPLEMENTATION_GUIDE.md
- SKILLS_MEMORY_INSTRUCTIONS.md
- IMPLEMENTATION_ROADMAP.md
- Any files in `/templates/` subdirectories
- Any files in `/generic-skills/` subdirectories

### Process:

1. List all `.md` files in `.claude/master-files/skills/` (excluding subdirectories and docs)
2. For each skill file:
   - Check if it already exists in `.claude/skills/`
   - If NOT exists: Copy to `.claude/skills/`
   - Track which skills were copied vs already existed

3. Use Bash tool to perform the sync operation

## 3. Report Results

After syncing, provide a summary:

**Format:**
```
✅ Session Initialized

📂 Skills Synced:
- qgis2web-cleanup.md [copied]
- other-skill.md [already existed]

📊 Summary:
- Total skills available: X
- New skills copied: X
- Skills already active: X

🎯 Ready to start working!
```

## 4. Welcome Message

Provide a brief welcome message mentioning:
- What skills are now available
- The project's purpose (Racial Terror historical map)
- That you're ready to help

---

**Use the Bash tool to perform all file operations.**
