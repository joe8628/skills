---
name: spec-writer
description: >
  Transforms raw concept documents, idea dumps, meeting notes, forge handoff blocks, and scattered
  context files into a single coherent, structured technical specification ready for software
  development. Use this skill whenever a user provides multiple concept docs, idea files, or a
  mix of notes and wants them synthesized into a development-ready spec. Also invoke when the
  user says things like "turn this into a spec", "write a tech spec from these ideas", "I have
  notes and want a spec", "generate a spec from this concept doc", or "take my forge output and
  make a spec". Do not wait for the user to use the exact word "spec" — if they have design
  artifacts and want something structured for developers to build from, this skill applies.
version: 1.2
updated: 2026-04-14
---

## Purpose

This skill ingests one or more unstructured inputs — concept documents, idea dumps, forge handoff
blocks, meeting notes, existing partial specs, or freeform descriptions — and produces a single,
complete, development-ready technical specification. It reconciles conflicts, fills structural
gaps, eliminates redundancy, and organizes everything into a consistent format a developer or
architect can act on immediately.

This skill is NOT an architecture skill. It captures **what** must be built and **why**, not
**how** to build it. It may describe system boundaries and data flows at a high level, but does
not produce component diagrams or implementation decisions unless those are already present and
confirmed in the inputs.

---

## Trigger Contexts

- User provides a forge "Ready for Architecture" block and wants the next step
- User drops one or more concept files and asks for a spec
- User provides a mix of notes, diagrams, or partial documents from multiple contributors
- User says they have "an idea doc" or "some notes" they want cleaned up for development
- A previous session produced raw requirements that need to be formalized

---

## Inputs

Accept any combination of the following:
- **Forge handoff blocks** — structured output from the `forge` skill
- **Concept documents** — free-form docs describing a product, feature, or system
- **Idea dumps** — unorganized notes, bullet lists, brain dumps
- **Meeting notes or transcripts** — decisions and requirements captured informally
- **Partial or draft specs** — incomplete prior specs that need expansion or reconciliation
- **Inline descriptions** — user types or pastes content directly into the conversation

When the user provides files, read them all before beginning. If content is pasted inline, collect
it all before proceeding. Do not begin synthesis until all input is available.

---

## Process

The spec session is a **dialogue, not a document dump**. Work through every conflict,
gap, and ambiguity **one topic at a time** before drafting. Only produce the full spec
once all blocking topics are resolved.

---

### Step 1 — Ingest and Inventory

Read every provided input. Produce a brief **Source Inventory** listing:
- Each source (file name or label, e.g. "Paste 1", "forge handoff")
- Its type (concept doc, idea dump, partial spec, meeting notes, etc.)
- A one-sentence summary of its primary contribution

Show this to the user and confirm nothing is missing. Then announce the topic count
and start immediately:

> "I've read **N sources** and found **M topics** to work through before drafting:
> **X conflicts**, **Y blocking gaps**, **Z important/minor gaps**.
> Let's go through them one by one. Ready?"

If there are zero conflicts and zero BLOCKING gaps, state that and move directly to
Step 3 (Draft).

---

### Step 2 — Topic Loop

Work through every conflict, gap, and ambiguity **one topic per message** using this
format:

---

**Topic N of M — [Topic Title]** `[CONFLICT | BLOCKING | IMPORTANT | MINOR]`

**Context**: 2–4 sentences explaining which spec section this feeds, why it matters,
and what decision is at stake. Reference the source material that raised the issue
(e.g., "Concept doc A says X while the forge handoff says Y").

**Issue**: What is missing, contradictory, or unclear. State concretely what cannot
be written in the spec without this being resolved.

**Proposed answer**: Your recommended resolution or default assumption with explicit
rationale. For IMPORTANT and MINOR tiers this is the default that will be used if the
user confirms. For BLOCKING topics, present the most likely answer based on available
context.

*Does this work, or would you like to adjust?*

---

**After the user responds:**
- **Accepted** (explicitly or no objection): record as confirmed, move to next topic.
- **Challenged or redirected**: discuss, reach resolution, record, move to next topic.
- **Skipped**: record as a deferred open decision (OD-XX in Section 11); flag with ⚠️
  in the affected spec section.
- **"Skip remaining"** / **"skip wizard"**: stop the loop, treat all unresolved topics
  as ⚠️ markers in the spec, and proceed to drafting.
- Never present more than one topic per message. One topic, one decision, one step forward.

**Topic ordering:**

1. **Conflicts** first — contradictions in the source material block everything else
2. **BLOCKING gaps** — sections that cannot be drafted without an answer
3. **IMPORTANT gaps** — sections that can use a default, but the risk is real
4. MINOR gaps are silently resolved with stated defaults unless the user asks about them

**Gap tiers:**

| Tier | Label | Meaning |
|------|-------|---------|
| 1 | **BLOCKING** | Entire section cannot be written without this. |
| 2 | **IMPORTANT** | Can draft with a stated assumption, but assumption carries real risk. |
| 3 | **MINOR** | Low-stakes; a reasonable default is defensible — resolve silently. |

**Progress line**: After each topic, include a one-liner:
> Topics: ✅ 4 resolved | 🔄 Topic 5 in progress | 6 remaining (2 blocking)

---

### Step 3 — Draft the Spec

Only begin drafting once all BLOCKING and IMPORTANT topics are resolved (or explicitly
deferred by the user). Before drafting, confirm:

> "All blocking topics resolved. Drafting the spec now."

Produce the full technical specification using the Output Format defined below. Every
section must be present. Sections with unresolved deferred topics must be marked:
`> ⚠️ Not defined — open decision OD-XX requires resolution before development begins.`

Every answer confirmed during Step 2 feeds directly into the spec. Do not ask the user
to repeat themselves.

---

### Step 4 — Review Pass

After drafting, perform a self-review:
- Flag any requirement that is ambiguous or not testable
- Flag any assumption that could materially affect the design
- Identify any section that relies on a single weak source (an offhand comment, not a
  confirmed decision)

Append a **Spec Health Report** at the end of the document summarizing findings. This is
not a blocker — the spec is still delivered — but it gives the architect a clear picture
of what is solid versus what needs owner confirmation.

---

## Output Format

Produce a markdown file. When a filesystem is available, save it as
`spec-[feature-or-project-name].md` and present the file to the user. Otherwise, output inline.

```markdown
# Technical Specification: [Project / Feature Name]

**Version**: 1.0-draft  
**Date**: [today]  
**Status**: Draft — pending review  
**Sources**: [list of input documents used]

---

## 1. Problem Statement

One to three paragraphs. What problem does this solve? Who experiences it? What does failure look
like today without this system?

---

## 2. Goals

What this spec commits to delivering. Each goal should be specific and verifiable.

- Goal 1
- Goal 2

## 3. Non-Goals

What is explicitly out of scope. Prevents scope creep during development.

- Not in scope: X
- Not in scope: Y

---

## 4. Users and Stakeholders

Who interacts with this system and in what capacity.

| Role | Description | Primary Interaction |
|------|-------------|---------------------|
| ... | ... | ... |

---

## 5. Functional Requirements

Organized by domain or feature area. Each requirement uses "MUST", "SHOULD", or "MAY" language
(RFC 2119). Each is numbered for traceability.

### 5.1 [Domain / Feature Area]

- **FR-01**: The system MUST ...
- **FR-02**: The system SHOULD ...

### 5.2 [Domain / Feature Area]

- **FR-03**: ...

---

## 6. Non-Functional Requirements

Performance, reliability, security, scalability, and operational constraints.

| ID | Category | Requirement |
|----|----------|-------------|
| NFR-01 | Performance | ... |
| NFR-02 | Security | ... |
| NFR-03 | Availability | ... |

---

## 7. System Context

High-level description of what this system is, what it is not, and how it sits within the broader
ecosystem. Include a boundary description: what is inside this system vs. what it depends on
externally.

Actors and external systems that interact with this spec's subject should be listed here.

> If a context diagram was provided in the source material, describe it in prose here.

---

## 8. Key Concepts and Data Model

Domain vocabulary and primary data entities. Not a full schema — captures the shape of data this
system owns, transforms, or depends on.

### 8.1 Glossary

| Term | Definition |
|------|------------|
| ... | ... |

### 8.2 Core Entities

For each primary entity: name, purpose, key attributes (not exhaustive), and relationships.

---

## 9. Interface Contracts

APIs, events, file formats, or other contracts this system exposes or consumes. Include enough
detail for integration to begin.

### Exposed Interfaces

| Interface | Type | Description |
|-----------|------|-------------|
| ... | REST / Event / File | ... |

### Consumed Interfaces

| Interface | Provider | Purpose |
|-----------|----------|---------|
| ... | ... | ... |

---

## 10. Constraints and Assumptions

Technical, organizational, or environmental constraints. Explicitly stated assumptions that
the spec depends on.

**Constraints:**
- C-01: ...

**Assumptions:**
- A-01: [Assumption made due to gap in source material — confirm before development]

---

## 11. Open Decisions

Decisions not yet made that will affect implementation. Each needs an owner and a deadline.

| ID | Question | Options | Owner | Required By |
|----|----------|---------|-------|-------------|
| OD-01 | ... | Option A / Option B | TBD | Before dev begins |

---

## 12. Out of Scope

Explicit list of things that could reasonably be assumed to be in scope but are not.

---

## Appendix: Spec Health Report

Generated automatically. Identifies areas that need attention before development begins.

**Ambiguous requirements:**
- FR-XX: [reason it is ambiguous]

**Unconfirmed assumptions:**
- A-XX: [what it affects if wrong]

**Weak sources:**
- Section X relies on: [offhand comment / single unconfirmed note]

**Unresolved conflicts from source material:**
- [Conflict description and proposed resolution]
```

---

## Behaviors and Rules

- **Never silently resolve a conflict.** Surface it as a topic, propose a resolution, and wait for confirmation.
- **Never omit a section.** If a section has no source material, mark it explicitly with the ⚠️ tag.
- **Never skip the topic loop.** Step 2 is mandatory unless there are genuinely zero conflicts and zero gaps, or the user says "skip".
- **One topic per message.** Present exactly one conflict or gap per turn. Never batch multiple topics together.
- **Never draft over a BLOCKING gap.** If a BLOCKING gap is present and the user has not answered or explicitly deferred it, do not fabricate content — hold on that section.
- **Prefer precision over completeness.** A requirement like "the system should be fast" is not acceptable. Restate it as "the system SHOULD respond to API requests within 200ms at p99 under normal load" or flag it as ambiguous.
- **Consolidate without losing signal.** When merging overlapping sources, ensure no distinct idea is dropped. If two sources say the same thing differently, use the clearer wording and note the merge.
- **Preserve intent over wording.** Source documents may be informal. Rewrite for clarity but do not change meaning. When in doubt, quote the original and add your interpretation.
- **Confirmed answers are canon.** Everything the user confirms during Step 2 is treated as confirmed input, not assumption. Do not re-flag these answers as assumptions in the Spec Health Report.
- **Assumptions must be visible.** Every assumption made to fill a gap the user did NOT answer must appear in Section 10 and the Spec Health Report.
- **Proposed answers must have rationale.** For every topic, the proposed answer must reference specific context from the source material, not just be a generic default.

---

## Relationship to Other Skills

- **forge** → spec-writer: forge produces the "Ready for Architecture" block. spec-writer accepts
  it directly as a primary input alongside any supporting documents.
- **spec-writer** → architect: the output of this skill is the primary input for an architecture
  session. The spec defines what to build; the architect defines how.

---

## Handoff Output

When the spec is complete, append this summary block for the next agent or session:

```
## Ready for Architecture

**Spec file**: spec-[name].md
**Status**: [Draft / Reviewed / Approved]
**Solid sections**: [list sections with strong source coverage]
**Needs confirmation before architecture begins**:
- [List open decisions and unconfirmed assumptions]
```
