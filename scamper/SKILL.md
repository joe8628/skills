---
name: scamper
description: Generates a grounded variant matrix from a stress-tested solution using seven systematic transformation lenses — invoke after six-hats evaluation is complete.
version: 1.2
updated: 2026-04-10
---

## Purpose
This skill takes a fully evaluated solution and applies seven transformation lenses to produce a set of intentional, grounded variants. Every variant is traceable to a specific lens and seeded by the evaluation findings from `six-hats`. It does NOT evaluate the variants — it generates them. The output is a variant matrix ready for architectural selection.

## Trigger
Invoke when a six-hats evaluation profile is available, including SCAMPER seeds. Triggered explicitly by `ship-of-thought` after `six-hats` completes, or invoked standalone when the user says "give me alternatives" or "what else could this be?"

## Role
You are a systematic generative engine. Your job is to produce intentional mutations of a well-understood solution — not random brainstorming. Every variant must be traceable, grounded, and non-trivial. You do NOT evaluate, score, or recommend. You generate.

## The Seven Lenses

Engage all seven lenses in order. Do not merge lenses. If the SCAMPER seeds and evaluation profile do not naturally produce a non-trivial variant for a lens, explicitly state why and mark it `N/A — no meaningful variant` in the live file. A blank lens is better than a fabricated one. This is not skipping — it is honest triage.

1. **S — Substitute**: What component, assumption, technology, or mechanism in the solution can be replaced with something else? What happens if you swap the core approach entirely?

2. **C — Combine**: What can be merged, integrated, or unified? What two elements of the solution, if fused, would produce something more powerful or simpler?

3. **A — Adapt**: What exists in another domain, industry, or context that could be borrowed and applied here? What analogous problem has already been solved differently?

4. **M — Modify / Magnify / Minimize**: What can be amplified to create more impact? What can be reduced or simplified without losing core value? What happens at extreme scale — very large or very small?

5. **P — Put to Other Uses**: Can this solution, or any part of it, serve a different purpose than originally intended? What adjacent problems does it accidentally solve?

6. **E — Eliminate**: What can be removed entirely? What happens if the most complex component simply doesn't exist? What is the irreducible minimum version of this solution?

7. **R — Reverse / Rearrange**: What happens if the order of operations is inverted? What if the user and system swap roles? What if the problem is approached from the opposite direction?

## Live Session File
Maintain a live session file at `docs/concepts/[concept-name].scamper.live.md` throughout the session. Update it **after each lens is applied** — immediately after generating variants for a lens, before moving to the next. This file is the crash-recovery checkpoint. If the session is interrupted, the next session MUST load this file and resume from the first lens still marked PENDING.

On startup: check for an existing `.scamper.live.md` file for this concept. If found, load it, confirm the current state with the user, and resume from the first incomplete lens.

## Behavior
- Load the SCAMPER Seeds section from the `six-hats` output first — these are pre-identified candidates and MUST be the starting point for the relevant lenses (E, S from Black Hat; C, M from Yellow Hat; A, R from Green Hat).
- For each lens, produce 1–3 variants. Prefer quality over quantity — one sharp variant beats three vague ones.
- Each variant must have: a name, the lens it came from, its lineage (which seed or finding generated it), and a one-sentence rationale for why it is non-trivial.
- **After completing each lens**: (1) present its variants to the user, (2) wait for acknowledgment before opening the next lens. Do not pre-generate all lenses — each lens is a separate conversation turn. Only after the user acknowledges: (3) write the variants to the live session file, mark the lens COMPLETE, and proceed.
- **Lens quality check**: Before generating variants for a lens, ask — does the six-hats seed profile naturally lead here? If not, state why and mark it `N/A` rather than fabricating a weak variant.
- After all seven lenses, produce the **Variant Matrix**: a consolidated table of all variants with their lens, lineage, and rationale.
- Identify the **High-Signal Variants**: the 2–3 variants that, based on their lineage and the evaluation profile, are most worth pursuing. Do not evaluate them — just flag them as high-signal based on where they came from.
- Ask one clarifying question only if a lens produces nothing meaningful and N/A is not obvious — do not force trivial variants.

## Input Contract
Expects:
- The Reconstructed Solution from `first-principles` (via six-hats)
- The full Six Hats Evaluation Profile
- The SCAMPER Seeds section (required — do not proceed without it)

## Process
1. Check `docs/concepts/` for an existing `[concept-name].scamper.live.md`. If found, load it and resume from the first lens not marked COMPLETE. Confirm with the user before proceeding.
2. Load the input document from `six-hats`
3. Extract SCAMPER Seeds — these seed the relevant lenses
4. Apply each lens in order (S → C → A → M → P → E → R). For each lens: generate variants, present to user, wait for acknowledgment, then save to live file and mark COMPLETE before proceeding to the next.
5. For each lens: generate 1–3 variants, name them, trace their lineage
6. Compile the Variant Matrix
7. Flag High-Signal Variants
8. Output the full Variant Matrix Document

## Boundaries
- MUST use SCAMPER Seeds from `six-hats` as starting points — not ignored
- MUST engage all seven lenses — may mark a lens `N/A` with a stated reason, but may not silently skip or fabricate a weak variant to fill the slot
- MUST NOT evaluate or rank variants — flagging high-signal is tracing, not ranking
- MUST NOT produce vague variants — every variant must be specific enough to be architected
- MUST trace every variant to its lens and lineage
- P lens (Put to Other Uses) MUST explore at least one adjacent problem space

## Live Session File Format

```markdown
# SCAMPER — Live Session: [Concept Name]

**Status**: IN_PROGRESS
**Source**: [six-hats filename]
**Last Updated**: YYYY-MM-DD

## Lens Progress

| Lens | Name | Status |
|------|------|--------|
| S | Substitute   | COMPLETE / IN_PROGRESS / PENDING / N/A |
| C | Combine      | COMPLETE / IN_PROGRESS / PENDING / N/A |
| A | Adapt        | COMPLETE / IN_PROGRESS / PENDING / N/A |
| M | Modify       | COMPLETE / IN_PROGRESS / PENDING / N/A |
| P | Put to Other Uses | COMPLETE / IN_PROGRESS / PENDING / N/A |
| E | Eliminate    | COMPLETE / IN_PROGRESS / PENDING / N/A |
| R | Reverse      | COMPLETE / IN_PROGRESS / PENDING / N/A |

## Variants (in progress)

*(Appended as each lens is completed)*

### S — Substitute
- **[Variant name]** *(seed: [lineage])* — <rationale>
```

## Output Document Format

```markdown
# SCAMPER Variant Matrix: [Concept Name]

**Stage**: GENERATE
**Source**: six-hats output — [filename or session reference]
**Created**: YYYY-MM-DD

## Seeded Starting Points

*(Carried forward from six-hats SCAMPER Seeds)*

- E/S candidates: <from Black Hat>
- C/M candidates: <from Yellow Hat>
- A/R candidates: <from Green Hat>

---

## Variants by Lens

### S — Substitute
- **[Variant name]** *(seed: [lineage])* — <what was substituted and what it produces>

### C — Combine
- **[Variant name]** *(seed: [lineage])* — <what was merged and what it produces>

### A — Adapt
- **[Variant name]** *(seed: [lineage])* — <what was borrowed from where and how it applies>

### M — Modify
- **[Variant name]** *(seed: [lineage])* — <what was amplified, reduced, or scaled and what it produces>

### P — Put to Other Uses
- **[Variant name]** *(seed: [lineage])* — <what adjacent problem this solves or alternate use>

### E — Eliminate
- **[Variant name]** *(seed: [lineage])* — <what was removed and what the minimal version looks like>

### R — Reverse
- **[Variant name]** *(seed: [lineage])* — <what was inverted or rearranged and what it produces>

---

## Variant Matrix

| Variant | Lens | Lineage | Rationale |
|---------|------|---------|-----------|
| [name]  | S    | [seed]  | <why non-trivial> |
| ...     | ...  | ...     | ... |

---

## High-Signal Variants

*Flagged based on lineage strength and evaluation profile — not scored or ranked.*

1. **[Variant name]** — <why its lineage makes it high-signal>
2. **[Variant name]** — <why its lineage makes it high-signal>

---

## Pipeline Complete

**Original concept**: <one line — what the pipeline started with>

**Reconstruction**: <one line — what first-principles produced>

**Top risk identified**: <carried from six-hats>

**Variants generated**: N (across 7 lenses)

**High-signal variants**: <names>

**Recommended next step**: Select a variant and hand off to architecture, or return to `forge` if no variant is viable.
```
