# Project Structure
**Last updated:** 2026-05-21 12:11:46

## Directory Tree
```
ClaudeCode/
├── .claude/
│   └── skills/
│       ├── end-feature/
│       │   └── SKILL.md
│       ├── start-feature/
│       │   └── SKILL.md
│       └── update-project-structure/
│           └── SKILL.md
├── PROJECT_STRUCTURE.md
└── .git/ (excluded)
```

## File-by-File Contents

### `.claude/skills/start-feature/SKILL.md`
**Configuration file for the `start-feature` skill.**

This is a Claude Code skill definition that automates starting a new feature branch:
* **Name:** `start-feature`
* **Description:** Starts a new feature branch from a clean main branch and renames the session.
* **Argument:** `[feature_name]` – The name of the feature branch to create.
* **Allowed Tools:** `Bash(git)`
* **Disable Model Invocation:** true (shell script only, no model inference)

**Shell Script Logic:**
1. Validates a feature name is provided; exits if missing.
2. Verifies the current branch is `main` or `master`; aborts if not.
3. Confirms the working tree is clean (no uncommitted changes).
4. Creates a new branch (or checks out existing one) with the given feature name.
5. Renames the Claude Code session to `<project>/<feature_name>` format.

### `.claude/skills/end-feature/SKILL.md`
**Configuration file for the `end-feature` skill.**

This is a Claude Code skill definition that automates ending a feature branch workflow:
* **Name:** `end-feature`
* **Description:** Stages all changes, commits with a message, and pushes the current feature branch (must not be main).
* **Argument:** `[commit message]` – The commit message to use.
* **Allowed Tools:** `Bash(git:*)`
* **Disable Model Invocation:** true (shell script only, no model inference)

**Shell Script Logic:**
1. **Pre-flight checks:**
   * Confirms the current branch is NOT `main` (safety guard).
   * Requires a commit message argument; prompts if missing.
2. **Commit and push:**
   * Stages all changes with `git add .`
   * Commits with the provided message.
   * Pushes to origin with upstream tracking on first push (`git push -u origin HEAD`).
3. **Final response:** Reports that the branch was pushed and is ready for review.

**Usage Example:**
```bash
/end-feature "Add user authentication"
```

### `.claude/skills/update-project-structure/SKILL.md`
**Configuration file for the `update-project-structure` skill.**

This is a Claude Code skill definition that generates or incrementally updates the `PROJECT_STRUCTURE.md` document:
* **Name:** `update-project-structure`
* **Description:** Generates or incrementally updates PROJECT_STRUCTURE.md with a directory tree, file paths, and class/function summaries.
* **Context:** `fork` – Runs in a separate agent context.
* **Agent:** `Explore` – Uses the Explore agent for file discovery.
* **Allowed Tools:** `Bash(find,git)`, `Read`, `Grep`, `Glob`, `Write`

**Workflow:**
1. **Check State:** Determines if `PROJECT_STRUCTURE.md` exists.
   * If missing → Full generation from scratch.
   * If present → Incremental update (only changed files).
2. **Full Generation:**
   * Builds a directory tree using `find` command.
   * Scans for source code files (`.py`, `.js`, `.ts`, etc.) using `Glob`.
   * Extracts classes, methods, and functions from each file.
   * Writes the complete document to `PROJECT_STRUCTURE.md`.
3. **Incremental Update:**
   * Finds files modified since last update timestamp.
   * Updates the directory tree if new files were added.
   * Patches file entries with updated class/function summaries.
   * Saves modifications back to `PROJECT_STRUCTURE.md`.
4. **Output Format:** Generates markdown with sections for directory tree and file-by-file contents.

---

## Project Summary
This is a **Claude Code skill library** for managing Git workflows and project documentation within the Claude Code IDE.

**Skills Included:**
- **`start-feature`** – Safely creates and switches to a new feature branch with session renaming.
- **`end-feature`** – Commits and pushes feature branches with safety checks against the main branch.
- **`update-project-structure`** – Maintains an auto-updating project structure document.

**Files Processed:** 3 SKILL.md files (all configuration/documentation, no executable source code).
