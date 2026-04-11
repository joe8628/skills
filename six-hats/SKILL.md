---
name: six-hats
description: Multi-perspective stress-test of a reconstructed solution using six bounded evaluation lenses — invoke after first-principles reconstruction is complete.
version: 1.2
updated: 2026-04-10
---

## Purpose
This skill evaluates a first-principles reconstruction by forcing six strictly separated perspectives in sequence. Each hat is a bounded lens — no mixing, no bleed. The output is a full evaluation profile: strengths, risks, gaps, emotional signal, creative pressure points, and a synthesis.

## Trigger
Invoke when a first-principles reconstruction document is available and needs multi-dimensional evaluation. Triggered explicitly by `ship-of-thought` after `first-principles` completes, or invoked standalone when the user says "stress-test this" or "what are we missing?"

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
- At the end of each hat, pose the **hat-specific exit prompt** (see Hat Exit Protocols below). Do NOT use a generic "does this feel complete?" question.
- A hat is not complete until its **blocking criteria** are satisfied (see Hat Exit Protocols). Do not write the hat to the live file or advance until they are met.
- If the user's response does not satisfy the blocking criteria, acknowledge what is still missing and re-ask. Push exactly once — if the second attempt still falls short, flag what criterion remains unmet and ask them to address it explicitly.
- **After blocking criteria are satisfied**: write the hat's full output plus the user's responses to the live session file, mark it COMPLETE, then open the next hat.
- After all six hats, produce a **Synthesis**: the most important signal from each hat, and what collectively they reveal about the reconstruction.
- Do not allow the user to skip the Black Hat — it is the highest-value pass.

## Hat Exit Protocols

Each hat has two components that must both be satisfied before advancing:
- **Exit prompt**: The specific question posed to the user after the hat's analysis is presented.
- **Blocking criteria**: The minimum the user's response must contain. If the response does not meet these, do not advance.

---

### 🔵 Blue Hat — Exit Protocol

**Exit prompt:**
> "Before we move to White Hat: what specific decision will this evaluation help you make? And what outcome would cause you to stop or pivot the idea entirely?"

**Blocking criteria:**
- User has named a concrete decision (not "whether to proceed" — something specific like "which implementation path" or "whether the market assumption holds").
- User has named at least one abort condition — a finding that would kill or fundamentally redirect the idea.

---

### ⚪ White Hat — Exit Protocol

**Exit prompt:**
> "Which data gap listed here is the most dangerous to your reconstruction — the one that, if it turns out to be wrong, breaks the most? Also: is there any fact or constraint you know from your context that isn't reflected here?"

**Blocking criteria:**
- User has identified one specific gap as highest-risk (not "all of them" — they must pick).
- User has either added a fact from their context OR explicitly confirmed the list is complete from their vantage point.

---

### 🟡 Yellow Hat — Exit Protocol

**Exit prompt:**
> "Which strength listed here do you find most compelling — the one you'd lead with if pitching this? Which one are you least convinced by, or think is overstated?"

**Blocking criteria:**
- User has named a top strength (their pick, not just agreeing with the list).
- User has named at least one strength they question or consider overstated.

---

### ⚫ Black Hat — Exit Protocol

**Exit prompt:**
> "Rank the top three risks from most to least likely. Then name the one you're most tempted to dismiss — and tell me why you'd dismiss it."

**Blocking criteria:**
- User has produced a ranking (partial is acceptable — at least two risks ordered).
- User has named a risk they're tempted to dismiss AND given a reason. This is mandatory: the dismissal impulse is often where the most important blind spots live.

---

### 🟢 Green Hat — Exit Protocol

**Exit prompt:**
> "Pick one direction from this hat that you'd actually pursue if you had to choose today. What is the single thing stopping you from pursuing a second one right now?"

**Blocking criteria:**
- User has selected one specific direction (not "all of them are interesting").
- User has named a constraint, risk, or gap that limits pursuing a second direction — forces a trade-off articulation.

---

### 🔴 Red Hat — Exit Protocol

**Exit prompt:**
> "Score your gut reaction to this reconstruction: 1 (walk away) to 10 (ship it). What's the single thing driving that number — the feeling you can't yet fully explain?"

**Blocking criteria:**
- User has given a numeric score.
- User has named one driving factor — even vague or inarticulate is acceptable here. "It feels too complicated" counts. Silence does not.

## Input Contract
Expects:
- The Reconstructed Solution from `first-principles`
- The Axiom Registry
- The Delta statement

If any of these is missing, surface what is absent and wait.

## Process
1. Check `docs/concepts/` for an existing `[concept-name].sixhats.live.md`. If found, load it and resume from the first hat not marked COMPLETE. Confirm with the user before proceeding.
2. Load the input document from `first-principles`
3. Run Blue Hat — establish evaluation objective and scope. Apply exit protocol. Save to live file only after blocking criteria are met.
4. Run White Hat — surface facts and data gaps. Apply exit protocol. Save to live file only after blocking criteria are met.
5. Run Yellow Hat — advocate for the reconstruction. Apply exit protocol. Save to live file only after blocking criteria are met.
6. Run Black Hat — diagnose risks and failure modes. Apply exit protocol. Save to live file only after blocking criteria are met.
7. Run Green Hat — surface creative pressure points and alternatives. Apply exit protocol. Save to live file only after blocking criteria are met.
8. Run Red Hat — capture intuitive and emotional signal. Apply exit protocol. Save to live file only after blocking criteria are met.
9. Produce Synthesis
10. Output the full Evaluation Profile

## Boundaries
- MUST complete all six hats — no skipping
- MUST NOT mix hat perspectives within a single pass
- Black Hat MUST identify at least two distinct failure modes
- MUST use the hat-specific exit prompt after each hat — never a generic "feel complete?" question
- MUST NOT advance to the next hat until the hat's blocking criteria are satisfied
- MUST NOT write a hat as COMPLETE in the live file until blocking criteria are met
- If the user's response does not meet blocking criteria, flag exactly what is missing and re-ask once before explicitly naming the unmet criterion

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

<What the six passes collectively reveal about this reconstruction. What is the most important insight? What is the dominant signal? What cannot be ignored?>

---

## Evaluation Summary

**Dominant strength**: <the single most important finding from Yellow or Green Hat>

**Top risk**: <the single most important finding from Black Hat>

**Overall signal**: <one tight paragraph — what the reconstruction looks like after full evaluation>
```
