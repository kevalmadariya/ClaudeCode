---
name: update-project-structure
description: Generates or incrementally updates PROJECT_STRUCTURE.md with a directory tree, file paths, and class/function summaries.
context: fork
agent: Explore
allowed-tools: Bash(find,git), Read, Grep, Glob, Write
model: nvidia_nim/moonshotai/kimi-k2.6
---

# Incremental Project Structure Documenter

## What this skill does
Maintains an up-to-date `PROJECT_STRUCTURE.md` in the root directory. 
- **First run**: Generates the file from scratch, including a full directory tree. 
- **Subsequent runs**: Scans for files modified since the last update and patches their specific entries.

---

## Instructions

### 1. Check State
Use the `Read` tool to check if `./PROJECT_STRUCTURE.md` exists.
- If **missing**, proceed to **Full Generation**.
- If **present**, proceed to **Incremental Update**.

### 2. Full Generation
1. **Build the Directory Tree:**  
   Run `find . -not -path '*/\.*' -not -path '*/node_modules/*' -not -path '*/__pycache__/*' | sort`
   Convert this output into a visual tree structure.
2. **Scan Source Files:**  
   Use `Glob` to find all relevant source code files (e.g., `**/*.py`, `**/*.js`, `**/*.ts`).
3. **Extract Contents:**  
   Read each file and extract:
   - **Classes:** Name and 1-line description of purpose.
   - **Methods/Functions:** Name and 1-line description of what it does.
4. **Format & Write (CRITICAL STEP):**  
   Compile the content using the required layout below. **You MUST use the `Write` tool** to save the final text directly into a new file named `./PROJECT_STRUCTURE.md` in the project root. Do not output the generated markdown to the chat.

### 3. Incremental Update
1. **Find Modified Files:**  
   Run `find . -type f -newer PROJECT_STRUCTURE.md -not -path '*/\.*' -not -path '*/node_modules/*'` to get a list of files changed since the document was last updated.
2. **Update Tree:**  
   If new files were added, manually insert their paths into the existing `## Directory Tree` section.
3. **Update Contents:**  
   For each changed file, read it, extract the updated classes/functions, and overwrite *only* that file's subsection in the `## File-by-File Contents`. If the file is entirely new, append a new subsection.
4. **Save (CRITICAL STEP):**  
   Update the `Last updated` header timestamp. **You MUST use the `Write` tool** to save the modifications back to `./PROJECT_STRUCTURE.md`.

### 4. Wrap Up
Return a brief summary to the user detailing how many files were processed or updated. Do not output the entire file contents to the chat.

---

## Required Output Format for PROJECT_STRUCTURE.md

```md
# Project Structure
**Last updated:** YYYY-MM-DD HH:MM:SS

## Directory Tree
[Insert visual tree here using ASCII/Markdown format]
├── src/
│   ├── main.py
│   └── utils.py
└── README.md

## File-by-File Contents

### `src/utils.py`
* **Class `DataLoader`** – Loads and preprocesses raw dataset files.
  * `load_csv(path)` – Reads a CSV and returns a parsed dataframe.
  * `clean_nulls()` – Strips empty rows from the active dataframe.
* **Function `normalize_string(text)`** – Standardizes casing and removes special characters.

### `src/main.py`
...