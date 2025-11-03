---
description: End session and sync new skills to master-files
---

You are ending a Claude Code session for this project.

# Session Cleanup Tasks

## 1. Compare Skill Directories

Compare `.claude/skills/` with `.claude/master-files/skills/` to find new skills created during this session.

### Process:

1. List all `.md` files in `.claude/skills/`
2. List all `.md` files in `.claude/master-files/skills/`
3. Identify NEW skills (exist in skills/ but NOT in master-files/skills/)

Use Bash tool to perform the comparison.

## 2. Backup New Skills to Master-Files

For each NEW skill found:

1. Copy from `.claude/skills/` to `.claude/master-files/skills/`
2. Preserve the original in both locations
3. Track what was backed up

### Command Example:
```bash
# For each new skill:
cp .claude/skills/[new-skill].md .claude/master-files/skills/[new-skill].md
```

## 3. Report Backup Results

Provide a summary of what was backed up:

**Format:**
```
✅ Session Closed

📦 Skills Backed Up:
- data-validation.md [new skill preserved]
- map-optimizer.md [new skill preserved]

📊 Summary:
- New skills backed up: X
- Existing skills: X
- Master-files is up to date ✅

💾 All session work preserved for future use!
```

## 4. Optional: Create Session Summary

If significant work was done, optionally create a brief summary:

**Location:** `Session_Archives/session_[date].md`

**Include:**
- Date and time
- Tasks completed during session
- Skills used or created
- Files modified
- Next steps (if any)

## 5. Closing Message

Provide a brief closing message:
- Confirm skills are backed up
- Note that work is preserved
- Mention that skills will be available in next session via `/open`

---

**Use the Bash tool to perform all file operations.**
