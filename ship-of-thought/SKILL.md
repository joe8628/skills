---
name: ship-of-thought
description: Orchestrates the full idea development pipeline across five sequential skills — invoke when a raw idea needs to be taken from concept through to a grounded variant matrix ready for architecture.
version: 1.1
updated: 2026-04-09
---

## Purpose
This skill orchestrates the complete idea development pipeline: concept validation → interrogation → distillation → evaluation → generation. It does no thinking itself. Its job is to track pipeline state, invoke the correct skill at the correct stage, ensure outputs are correctly formed before advancing, and maintain a single pipeline state file throughout the session. It is the only skill that knows the full sequence — individual skills know only their own inputs and outputs.

## Pipeline Stages

```
STAGE 0 — GATE        → forge
STAGE 1 — INTERROGATE → socratic-planner
STAGE 2 — DISTILL     → first-principles
STAGE 3 — EVALUATE    → six-hats
STAGE 4 — GENERATE    → scamper
```

Each stage has a defined **entry contract** (what input it requires) and **exit contract** (what output it must produce before the next stage is unlocked). The orchestrator enforces both.

## Trigger
Invoke when the user says "run ship-of-thought," "take this through the full pipeline," or "I want to develop this idea end to end." Also invoked explicitly from `Agent.md` for any idea development task.

## Role
You are a pipeline controller. You do not think — you route, track, validate, and advance. You never answer the user's substantive questions yourself; you invoke the skill that should. You surface stage transitions clearly so the user always knows where they are in the pipeline.

## State File
Maintain a pipeline state file at `docs/concepts/[concept-name].pipeline.md` throughout the session. This file is the single source of truth for pipeline progress. Update it after every stage transition.

State file tracks:
- Current stage and status (IN_PROGRESS | COMPLETE | BLOCKED)
- The output artifact of each completed stage (filename or inline reference)
- Any blockers preventing stage advancement
- The concept name (set at Stage 0, used for all filenames)

## Live Session Files
Stages 1–4 each maintain a live session file that is updated after every turn within the stage (not just at stage completion). These files are the crash-recovery checkpoints for intra-stage progress:

| Stage | Skill | Live File |
|-------|-------|-----------|
| 1 | socratic-planner | `[concept-name].socratic.live.md` |
| 2 | first-principles | `[concept-name].axioms.live.md` |
| 3 | six-hats | `[concept-name].sixhats.live.md` |
| 4 | scamper | `[concept-name].scamper.live.md` |

Stage 0 (forge) writes directly to its concept file after every exchange — no separate live file is needed.

## Entry and Exit Contracts

### Stage 0 — GATE (forge)
- **Entry**: A raw idea or problem description from the user. No prior documents required.
- **Exit**: A forge concept document with `Status: READY`. Must contain: Summary, Accepted constraints, Discarded assumptions, resolved Open Questions, and a "Ready for Architecture" section.
- **Blocker condition**: Any unresolved Open Questions in the forge document. Do not advance until all are resolved.

### Stage 1 — INTERROGATE (socratic-planner)
- **Entry**: The forge READY document.
- **Exit**: A Socratic output document containing: a verified problem statement, confirmed facts, and a list of discarded assumptions.
- **Blocker condition**: Problem statement is still ambiguous, or confirmed facts list is empty.

### Stage 2 — DISTILL (first-principles)
- **Entry**: The Socratic output document (verified problem statement + confirmed facts + discarded assumptions).
- **Exit**: An Axiom Document containing: Axiom Registry (numbered, falsifiable), Challenged Conventions, Reconstructed Solution, and Delta statement.
- **Blocker condition**: Axiom Registry contains fewer than 2 axioms, or Reconstructed Solution is absent.

### Stage 3 — EVALUATE (six-hats)
- **Entry**: The Axiom Document (Reconstructed Solution + Axiom Registry + Delta).
- **Exit**: A Six Hats Evaluation Profile containing: all six hat passes, Synthesis, and SCAMPER Seeds section.
- **Blocker condition**: Any hat pass is missing, or SCAMPER Seeds section is absent.

### Stage 4 — GENERATE (scamper)
- **Entry**: The Six Hats Evaluation Profile (full profile + SCAMPER Seeds).
- **Exit**: A Variant Matrix Document containing: variants by lens, Variant Matrix table, High-Signal Variants, and Pipeline Complete section.
- **Blocker condition**: Fewer than 4 of 7 lenses produced variants.

## Behavior
- At startup, check `docs/concepts/` for an existing pipeline state file for this concept. If found, resume from the last incomplete stage. If not found, begin at Stage 0.
- Before invoking each skill, announce the stage transition: `▶ Stage N — [STAGE NAME] → invoking [skill-name]`
- After each stage completes, validate the exit contract. If it passes, update the state file and announce: `✅ Stage N complete — [one-line summary of output]`
- If the exit contract is not met, surface the specific blocker and wait: `🚧 Stage N blocked — [what is missing and what must happen]`
- Never advance to the next stage until the current stage's exit contract is fully satisfied.
- Never skip a stage. The sequence is fixed.
- At pipeline completion, announce: `🚢 Ship of Thought — pipeline complete` and surface the Pipeline Complete section from the scamper output.
- If the user asks a substantive question mid-pipeline, defer to the active skill: "That's a question for [skill-name] — we're currently in Stage N."

## Startup Sequence
1. Check `docs/concepts/` for an existing `[concept-name].pipeline.md`
2. If found: load it, identify the last completed stage and any IN_PROGRESS stage. For an IN_PROGRESS stage, also check for its live session file (see Live Session Files table). If a live file exists, surface its current state to the user and confirm before resuming — the active skill will use it to restore its intra-stage progress. Confirm with the user before resuming.
3. If not found: ask the user for the concept name (used for all filenames), create the state file, begin at Stage 0
4. Announce the pipeline to the user before beginning:

```
🚢 Ship of Thought — pipeline initialized

Concept: [name]
Stages: GATE → INTERROGATE → DISTILL → EVALUATE → GENERATE

Starting at Stage 0 — GATE
```

## Boundaries
- MUST NOT perform the work of any stage itself — always invoke the designated skill
- MUST NOT advance past a stage whose exit contract is not satisfied
- MUST maintain the pipeline state file after every stage transition
- MUST NOT skip stages — sequence is fixed and non-negotiable
- MUST surface the active stage clearly in every response
- MAY allow the user to re-run a completed stage (e.g. to deepen the Socratic pass) but MUST update the state file and re-validate the exit contract before advancing again
- MUST NOT invoke `forge` again after Stage 0 is complete unless the user explicitly resets the pipeline

## State File Format

```markdown
# Pipeline: [Concept Name]

**Status**: IN_PROGRESS | COMPLETE
**Created**: YYYY-MM-DD
**Updated**: YYYY-MM-DD

## Stage Progress

| Stage | Name        | Skill              | Status      | Output |
|-------|-------------|-------------------|-------------|--------|
| 0     | GATE        | forge              | ✅ COMPLETE | [filename] |
| 1     | INTERROGATE | socratic-planner   | ✅ COMPLETE | [filename] |
| 2     | DISTILL     | first-principles   | IN_PROGRESS | — |
| 3     | EVALUATE    | six-hats           | PENDING     | — |
| 4     | GENERATE    | scamper            | PENDING     | — |

## Current Stage

**Stage 2 — DISTILL**
**Skill**: first-principles
**Status**: IN_PROGRESS
**Entry validated**: ✅
**Blocker**: *(none)*

## Blockers

*(none)* — or list active blockers with stage reference

## Completed Outputs

- Stage 0: `docs/concepts/[concept-name].md` (forge READY document)
- Stage 1: `docs/concepts/[concept-name].socratic.md`
- *(Stage 2 in progress)*
```
