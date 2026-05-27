# Project Structure
**Last updated:** 2026-05-27 15:40:00

## Directory Tree
```
ClaudeCode/
├── .agents/
│   └── skills/
│       ├── create-spec-doc/
│       │   └── SKILL.md
│       ├── end-feature/
│       │   └── SKILL.md
│       ├── start-feature/
│       │   └── SKILL.md
│       └── update-project-structure/
│           └── SKILL.md
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

### `.agents/skills/create-spec-doc/SKILL.md`
**Configuration file for the `create-spec-docs` skill.**

This is a Claude Code skill definition that generates structured feature specification documents:
* **Name:** `create-spec-docs`
* **Description:** Create a detailed spec document in `./spec-document/{name}.md` after researching the project codebase.
* **Arguments:** `[spec-doc-name, description]` – Name and description of the feature to spec.
* **Allowed Tools:** `Bash(mkdir)`, `Read`, `Write`, `Grep`, `Glob`
* **Disable Model Invocation:** true (shell script only, no model inference)

**Shell Script Logic:**
1. Validates the feature name and description.
2. Performs research via file globbing, grepping, and reading.
3. Creates a `./spec-document` directory and writes the feature spec containing Problem Statement, Functional Requirements, Input/Output Behavior, Constraints, Edge Cases, and Acceptance Criteria.

### `.agents/skills/start-feature/SKILL.md`
**Configuration file for the `start-feature` skill (for the Antigravity agent).**

This is a Claude Code skill definition that automates starting a new feature branch:
* **Name:** `start-feature`
* **Description:** Start a new feature branch from a clean main branch and rename the session.
* **Argument:** `[feature_name]` – The name of the feature branch to create.
* **Allowed Tools:** `Bash(git add, git commit, git checkout, git status, git rev-parse, git show-ref, git diff, git log, git push, git pull, git fetch, git clone, git init, git merge)`, `Command(/rename)`
* **Disable Model Invocation:** false (model invocation enabled to execute slash commands)

**Shell Script Logic:**
1. Validates a feature name is provided.
2. Verifies the current branch is `main` or `master`.
3. Confirms the working tree is clean.
4. Creates a new branch (or checks out existing one) with the given feature name.
5. Renames the Claude Code session to `<project>/<feature_name>` format using the `/rename` command.

### `.agents/skills/end-feature/SKILL.md`
**Configuration file for the `end-feature` skill (for the Antigravity agent).**

This is a Claude Code skill definition that automates ending a feature branch workflow:
* **Name:** `end-feature`
* **Description:** Stage all changes, commit with a message, and push the current feature branch (must not be main).
* **Argument:** `[commit_message]` – The commit message to use.
* **Allowed Tools:** `Bash(git add, git commit, git checkout, git status, git rev-parse, git show-ref, git diff, git log, git push, git pull, git fetch, git clone, git init, git merge)`
* **Disable Model Invocation:** true

**Shell Script Logic:**
1. Confirms the current branch is NOT `main` or `master`.
2. Requires a commit message argument.
3. Stages all changes (`git add .`), commits, and pushes upstream (`git push -u origin HEAD`).

### `.agents/skills/update-project-structure/SKILL.md`
**Configuration file for the `update-project-structure` skill (for the Antigravity agent).**

This is a Claude Code skill definition that generates or incrementally updates the `PROJECT_STRUCTURE.md` document:
* **Name:** `update-project-structure`
* **Description:** Generates or incrementally updates PROJECT_STRUCTURE.md with a directory tree, file paths, and class/function summaries.
* **Context:** `fork`
* **Agent:** `Explore`
* **Allowed Tools:** `Bash(find, git add, git commit, git checkout, git status, git rev-parse, git show-ref, git diff, git log, git push, git pull, git fetch, git clone, git init, git merge)`, `view_file`, `grep_search`, `list_dir`, `write_to_file`, `replace_file_content`, `multi_replace_file_content`

### `.claude/skills/start-feature/SKILL.md`
**Legacy configuration file for the `start-feature` skill.**

* **Allowed Tools:** `Bash(git)`
* **Disable Model Invocation:** true

### `.claude/skills/end-feature/SKILL.md`
**Legacy configuration file for the `end-feature` skill.**

* **Allowed Tools:** `Bash(git)`
* **Disable Model Invocation:** true

### `.claude/skills/update-project-structure/SKILL.md`
**Legacy configuration file for the `update-project-structure` skill.**

* **Allowed Tools:** `Bash(find,git), Read, Grep, Glob, Write`

---

## Project Summary
This is a **Claude Code skill library** for managing Git workflows, project documentation, and feature specifications within the Claude Code IDE.

**Skills Included:**
- **`create-spec-docs`** – Generates structured feature specification documents after codebase research.
- **`start-feature`** – Safely creates and switches to a new feature branch with session renaming.
- **`end-feature`** – Commits and pushes feature branches with safety checks against the main branch.
- **`update-project-structure`** – Maintains an auto-updating project structure document.

**Files Processed:** 8 SKILL.md files (4 under `.agents/` and 3 under `.claude/`, plus 1 new skill config).
