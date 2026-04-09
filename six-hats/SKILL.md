---
name: six-hats
description: Multi-perspective stress-test of a reconstructed solution using six bounded evaluation lenses — invoke after first-principles reconstruction is complete.
version: 1.1
updated: 2026-04-09
---

## Purpose
This skill evaluates a first-principles reconstruction by forcing six strictly separated perspectives in sequence. Each hat is a bounded lens — no mixing, no bleed. The output is a full evaluation profile: strengths, risks, gaps, emotional signal, creative pressure points, and a synthesis. It does NOT generate solution variants — that is the job of `scamper`.

## Trigger
Invoke when a first-principles reconstruction document is available and needs multi-dimensional evaluation before variant generation. Triggered explicitly by `ship-of-thought` after `first-principles` completes, or invoked standalone when the user says "stress-test this" or "what are we missing?"

## Role
You are a structured evaluator. Your job is to prevent any single mode of thinking from dominating the assessment. You enforce hat boundaries strictly — optimism does not bleed into caution, facts do not bleed into intuition. Each pass produces a clean, bounded output that seeds the next.

## Hat Sequence and Definitions

Execute hats in this order. Do not reorder. Do not skip.

1. **🔵 Blue Hat — Process**: Define the objective and scope of this evaluation session. What are we trying to decide? What does a good evaluation output look like? Sets the frame for all subsequent hats.

2. **⚪ White Hat — Facts**: What data, evidence, and verifiable information exists to support or challenge this reconstruction? No interpretation. No opinion. Facts and gaps only.

3. **🟡 Yellow Hat — Optimism**: What is the best-case outcome if this reconstruction is correct? What genuine strengths and benefits does it offer? Committed advocacy — no hedging.

4. **⚫ Black Hat — Caution**: What can go wrong? What are the risks, failure modes, logical flaws, and worst-case scenarios? No solutions here — diagnosis only.

5. **🟢 Green Hat — Creativity**: What alternatives, modifications, or adjacent directions does this reconstruction suggest? Where is there creative pressure — things that almost work but need a twist?

6. **🔴 Red Hat — Intuition**: What is the gut reaction? What feels wrong or right that cannot yet be articulated? Emotional and intuitive signal only — no justification required.

## Live Session File
Maintain a live session file at `docs/concepts/[concept-name].sixhats.live.md` throughout the session. Update it **after each hat is completed** — immediately after the user confirms a hat is complete and before opening the next. This file is the crash-recovery checkpoint. If the session is interrupted, the next session MUST load this file and resume from the first hat still marked PENDING or IN_PROGRESS.

On startup: check for an existing `.sixhats.live.md` file for this concept. If found, load it, confirm the current state with the user, and resume from the first incomplete hat.

## Behavior
- Run each hat as a discrete, labeled pass. Complete one fully before opening the next.
- At the end of each hat, ask: "Does this feel complete, or should we go deeper on anything before moving on?" If yes, deepen. If no, advance.
- **After the user confirms a hat is complete**: write its full output to the live session file and mark it COMPLETE before beginning the next hat.
- After all six hats, produce a **Synthesis**: the most important signal from each hat, and what collectively they reveal about the reconstruction.
- Explicitly seed `scamper`: from the Black Hat, identify the top E (Eliminate) and S (Substitute) candidates. From Yellow Hat, identify the top C (Combine) and M (Modify) candidates. From Green Hat, identify the top A (Adapt) and R (Reverse) candidates. This seeding is a required output section.
- Do not allow the user to skip the Black Hat — it is the highest-value pass.

## Input Contract
Expects:
- The Reconstructed Solution from `first-principles`
- The Axiom Registry
- The Delta statement

If any of these is missing, surface what is absent and wait.

## Process
1. Check `docs/concepts/` for an existing `[concept-name].sixhats.live.md`. If found, load it and resume from the first hat not marked COMPLETE. Confirm with the user before proceeding.
2. Load the input document from `first-principles`
3. Run Blue Hat — establish evaluation objective and scope. Save to live file on completion.
4. Run White Hat — surface facts and data gaps. Save to live file on completion.
5. Run Yellow Hat — advocate for the reconstruction. Save to live file on completion.
6. Run Black Hat — diagnose risks and failure modes. Save to live file on completion.
7. Run Green Hat — surface creative pressure points and alternatives. Save to live file on completion.
8. Run Red Hat — capture intuitive and emotional signal. Save to live file on completion.
9. Produce Synthesis
10. Produce SCAMPER seeds
11. Output the full Evaluation Profile

## Boundaries
- MUST complete all six hats — no skipping
- MUST NOT mix hat perspectives within a single pass
- MUST NOT produce solution variants — that belongs to `scamper`
- MUST produce explicit SCAMPER seeds as a required output section
- Black Hat MUST identify at least two distinct failure modes
- MUST ask for deepening confirmation after each hat before advancing

## Live Session File Format

```markdown
# Six Hats Evaluation — Live Session: [Concept Name]

**Status**: IN_PROGRESS
**Source**: [first-principles filename]
**Last Updated**: YYYY-MM-DD

## Hat Progress

| Hat | Name | Status |
|-----|------|--------|
| 🔵 Blue   | Process    | COMPLETE / IN_PROGRESS / PENDING |
| ⚪ White  | Facts      | COMPLETE / IN_PROGRESS / PENDING |
| 🟡 Yellow | Optimism   | COMPLETE / IN_PROGRESS / PENDING |
| ⚫ Black  | Caution    | COMPLETE / IN_PROGRESS / PENDING |
| 🟢 Green  | Creativity | COMPLETE / IN_PROGRESS / PENDING |
| 🔴 Red    | Intuition  | COMPLETE / IN_PROGRESS / PENDING |

## Completed Hat Outputs

*(Full content of each completed hat — appended as hats are finished)*

### 🔵 Blue Hat — Process
<output>

### ⚪ White Hat — Facts
<output>
```

## Output Document Format

```markdown
# Six Hats Evaluation: [Concept Name]

**Stage**: EVALUATE
**Source**: first-principles output — [filename or session reference]
**Created**: YYYY-MM-DD

## Evaluation Objective

*(Blue Hat)* <What this evaluation is trying to determine and what a good outcome looks like.>

---

## Facts and Gaps

*(White Hat)*

**Known:**
- <fact>

**Unknown / Data gaps:**
- <gap>

---

## Strengths

*(Yellow Hat)*

- <genuine strength or benefit>

---

## Risks and Failure Modes

*(Black Hat)*

- **[Risk label]**: <description — specific, not vague>

---

## Creative Pressure Points

*(Green Hat)*

- <alternative, modification, or adjacent direction worth exploring>

---

## Intuitive Signal

*(Red Hat)*

- <gut reaction or emotional signal — no justification required>

---

## Synthesis

<What the six passes collectively reveal about this reconstruction. What is the most important insight? What is the dominant signal? What cannot be ignored going into variant generation?>

---

## SCAMPER Seeds

*Direct input for the scamper skill. Derived from hat findings.*

- **Eliminate (E)** candidates: <from Black Hat risks — what to cut>
- **Substitute (S)** candidates: <from Black Hat — what to replace>
- **Combine (C)** candidates: <from Yellow Hat strengths — what to merge>
- **Modify (M)** candidates: <from Yellow Hat — what to amplify or reduce>
- **Adapt (A)** candidates: <from Green Hat — what to borrow from other domains>
- **Reverse (R)** candidates: <from Green Hat — what to invert or rearrange>

---

## Ready for Generation

**Evaluation summary**: <one tight paragraph — what the reconstruction looks like after full evaluation, what its dominant strength is, and what its most significant risk is>

**Top risk**: <the single most important finding from Black Hat>

**Top opportunity**: <the single most important finding from Yellow or Green Hat>
```
