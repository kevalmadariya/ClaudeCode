---
name: create-spec-docs
description: Create a detailed spec document in ./spec-document/{name}.md after researching the project codebase.
argument-hint: [spec-doc-name] ["goal or problem statement"]
arguments: [spec-doc-name, description]
disable-model-invocation: true
allowed-tools: Bash(mkdir) Read Write Grep Glob
---

# Spec document generator

## What this skill does

Takes a spec name (`$spec-doc-name`) and a description (`$description`), then:

1. Researches the existing project (patterns, code, constraints)
2. Fills out a spec document with all required sections
3. Saves it to `./spec-document/$spec-doc-name.md`

---

## Instructions

### 1. Understand the feature

The user wants a spec for: **$description**

Identify which part of the system this touches based on the description.

### 2. Research the project

- `Glob` for relevant source files (e.g., `**/*.py`, `**/*.js`, `**/*.ts` – infer from the project).
- `Grep` for keywords from `$description` to find existing implementations or patterns.
- Read a few representative files to grasp conventions, error handling, and dependencies.

### 3. Create the spec document

First, ensure the output directory exists:

```bash
mkdir -p ./spec-document


# Spec: $spec-doc-name

## Problem Statement
$description

## Functional Requirements
- FR1: ...
- FR2: ...
- FR3: ...
(add more as appropriate)

## Input/Output Behavior
- Input: what the system receives (user actions, API payloads)
- Processing: high‑level steps the system must perform
- Output: what the system produces or changes

## Constraints
- (e.g., must reuse existing library X, no breaking changes to Y, performance limits)

## Edge Cases
- (e.g., empty input, extremely large values, concurrent access)

## Acceptance Criteria
- AC1: ...
- AC2: ...
- AC3: ...