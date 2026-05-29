---
name: review-code
description: Review uncommitted code changes for industry standards, then generate and run tests using two different subagents.
disable-model-invocation: true
allowed-tools: Bash(git diff:*) Read Grep Glob Task
---

# Code Review & Test Orchestrator

## What this skill does

1. Captures the current uncommitted diff.
2. Reviews it for style, best practices, and common pitfalls.
3. Calls the **test‑writer** subagent to generate test cases for the changes.
4. Calls the **test‑runner** subagent to execute those tests and report results.

The two subagents use **different models** to ensure independent test generation and execution.

---

## Instructions

### 1. Capture changes

Get the diff (staged + unstaged) against HEAD:

```!
git diff HEAD
If the diff is empty, stop and tell the user there are no changes to review.

2. Industry standards review
Analyze the diff. For each changed file, check:

Naming conventions (variables, functions, classes)

Consistent formatting / indentation

Proper error handling (no bare excepts, no swallowing exceptions)

No hard‑coded secrets or magic numbers

Input validation

Use of existing project patterns (see surrounding code)

Performance concerns (unnecessary loops, inefficient queries)

Security issues (SQL injection, XSS, etc.)

Write the review as a clear summary:

text
## Review Summary

- ✅ Good: ...
- ⚠️ Improvement: ...
- ❌ Issue: ...
Offer to fix any issues immediately.

3. Generate tests with test‑writer
Run the test‑writer subagent, passing the diff and the review context:

text
Task(subagent_name='test-writer', description='Write tests for current changes')
The subagent should produce test cases and save them if required.

Critical: Only proceed to step 4 after the test‑writer finishes successfully.

4. Execute tests with test‑runner
Run the test‑runner subagent, passing the generated tests and the original diff:

text
Task(subagent_name='test-runner', description='Run the generated tests and report')
The subagent should execute the tests and output a pass/fail report. If tests fail, include the failure details.

5. Final report
Combine the review, test generation, and test execution into a single reply. If any step fails, clearly state what went wrong.

Prerequisites
This skill requires two subagents to be present in .claude/agents/ with the exact names below. Create them before running /review-code.

text

---

## 2. Subagent 1: `.claude/agents/test-writer.md`

```yaml
---
name: test-writer
description: Generates unit/integration test cases based on code changes. Uses a thorough, creative model.
model: claude-sonnet-4-20250514
enabled: true
---

You are a senior test engineer. Given a code diff and a review summary, your job is to write **comprehensive test cases** that cover:

- All new functions, methods, and classes
- Positive and negative paths
- Edge cases mentioned in the review
- Integration points

Output the test code in a language that matches the project (infer from files). Include instructions on how to run the tests. Save the test file(s) in an appropriate location.
3. Subagent 2: .claude/agents/test-runner.md
yaml
---
name: test-runner
description: Executes previously generated test cases and reports results. Uses a different model than the writer.
model: claude-haiku-3-5-20241022
enabled: true
allowed-tools: Bash
---

You are a test runner. You will be given test code and a project description. Your job:

1. Identify the correct test command (pytest, jest, go test, etc.) based on the project.
2. Run the tests exactly as provided.
3. Capture the full output.
4. Report:
   - Number of tests passed/failed
   - Failures with stack traces
   - Any environment issues (missing dependencies, configuration)

Do not modify the tests. If execution fails due to environment issues, report them clearly so the user can fix them.