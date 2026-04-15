---
name: writing-plans
description: >
  Write implementation plans from an approved design or spec. Activates with an approved design
  or spec document. Breaks work into bite-sized tasks with exact file paths, lean TDD code blocks,
  and commit points — fast enough to ship without wasted tokens. Use when the user has an approved
  spec or design and wants a plan a coding agent can execute immediately. Triggers on: "write the
  plan", "create an implementation plan", "plan this feature", or after brainstorming/spec-writer
  produces an approved document. This is a faster alternative to superpowers:writing-plans —
  same structure, leaner code blocks, no post-write review pass.
version: 1.2
updated: 2026-04-15
---

## Purpose

Write an implementation plan a coding agent can follow task by task: **what to build, which files to touch, minimal failing test, core implementation.** No boilerplate — the agent reads files and fills that in.

## Announce

Say at start: "I'm using the writing-plans skill to create the implementation plan."

## Context

- Run in a dedicated worktree (created by brainstorming or using-git-worktrees skill)
- Save plan to: `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- User preferences for plan location override this default

---

## Compactness Rules

**These rules exist because plans should be dense, not long. A 300-line plan beats a 2000-line plan every time.**

| Rule | What it means |
|------|---------------|
| **Files on one line** | `Files: create \`a.py\`, modify \`b.py\`, test \`tests/test_a.py\`` |
| **Test block ≤ 5 lines** | Assertion only — no imports, no setup, no class, no docstring |
| **Impl block ≤ 8 lines** | Signature + core logic only — no imports, no class wrapper |
| **Steps as bullets, not prose** | Each step is one imperative line with inline code |
| **DRY across tasks** | Second time same pattern appears: "same as Task N" is enough |
| **No inline commentary** | Don't explain what the code does in the plan — it's code |
| **No redundant headers** | One `---` separator between tasks is enough |

Violating these adds lines without adding information. The implementer reads the codebase — write only what is non-obvious from context.

---

## No Placeholders

Never write:
- "TBD", "TODO", "implement later", "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (for the core logic — repeat it; agents may read tasks out of order)
- Function signatures with `pass` / `...` and no guidance

If you cannot write a lean block, write a fuller one. Lean is a target, not an excuse to skip content.

---

## Process

### Step 1 — Skeleton Pass

Write ALL task headers with file paths before writing any code:

```
### Task 1: [Name] — Files: create `x.py`, test `tests/test_x.py`
### Task 2: [Name] — Files: modify `y.py`, test `tests/test_y.py`
```

Check skeleton against the spec. Every requirement maps to at least one task? If not, add tasks now.

### Step 2 — Fill Each Task

Fill all tasks in one pass. Do not wait for approval between tasks.

### Step 3 — Spec Coverage Table

```
## Spec Coverage
| Requirement | Task |
|-------------|------|
| FR-01: ...  | Task 2 |
```

If any requirement has no task, add it inline. No separate review loop.

---

## Task Format

```markdown
### Task N: [Component Name]
Files: create `exact/path/new.py`, test `tests/exact/test_new.py`

- [ ] **Test** — add to `tests/exact/test_new.py`:
  ```py
  def test_behavior():
      assert fn(input) == expected
  ```
  Run `pytest tests/exact/test_new.py` → FAIL

- [ ] **Implement** — add to `exact/path/new.py`:
  ```py
  def fn(param: Type) -> ReturnType:
      [core logic, 3–8 lines]
      return result
  ```

- [ ] **Commit** — `type: what this task delivers`
```

### Step 4 — only when needed

Add a Step 4 only for: integration test against a live API/DB, meaningful manual check (start server, hit route), or infrastructure verification (migration ran). Skip "run all tests" — the execution skill handles that.

### Complex implementations

If core logic exceeds ~15 lines, split into two tasks (data transform + I/O). Never write a 20-line impl block in a plan.

---

## Plan Document Format

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development
> (recommended) or superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [one sentence]
**Spec:** [path to spec/design doc]
**Stack:** [key technologies]

---

### Task 1: [Name]
...

---

## Spec Coverage
| Requirement | Task |
|-------------|------|
```

---

## Rules

- **TDD** — every code task has a failing test step. No exceptions.
- **Exact paths** — all file paths are exact relative paths from repo root.
- **Context isolation** — Task 4 must be implementable without reading Tasks 1–3. Repeat essential references; never write "as defined in Task N".
- **Frequent commits** — every task ends with a commit. Format: `type: description`.
- **YAGNI** — no tasks for features not in the spec. Note likely future needs as a comment in the relevant task, not a task of their own.

---

## Relationship to superpowers:writing-plans

Same plan format and execution skills (superpowers:subagent-driven-development, superpowers:executing-plans). Difference is generation speed and output size. Fall back to the original skill only if you need full production-ready code in the plan for safety.
