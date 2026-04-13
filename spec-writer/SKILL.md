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
version: 1.0
updated: 2026-04-13
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

### Phase 1 — Ingest and Inventory

Read every provided input. Then produce a brief **Source Inventory** listing:
- Each source identified (file name or label, e.g. "Paste 1", "forge handoff")
- Its apparent type (concept doc, idea dump, partial spec, etc.)
- A one-sentence summary of its primary contribution

Show this to the user and confirm nothing is missing before proceeding.

### Phase 2 — Reconcile

Across all sources, identify:
- **Overlaps**: the same concept described multiple times → consolidate into one canonical statement
- **Conflicts**: contradictory requirements, goals, or constraints → surface them explicitly, do NOT silently pick one
- **Gaps**: sections required by the spec format that no source addresses → flag as "requires input" or make a clearly labeled assumption

Present a **Reconciliation Summary** with these three categories. For conflicts, propose a
resolution and ask the user to confirm before proceeding. For gaps, state the assumption you will
use if the user does not intervene.

Wait for user confirmation or corrections before proceeding to Phase 3.

### Phase 3 — Draft the Spec

Produce the full technical specification using the Output Format defined below. Every section
must be present. Empty sections must be explicitly marked `> ⚠️ Not defined in source material —
requires input before development begins.`

### Phase 4 — Review Pass

After drafting, perform a self-review:
- Flag any requirement that is ambiguous or not testable
- Flag any assumption that could materially affect the design
- Identify any section that relies on a single weak source (an offhand comment, not a confirmed decision)

Append a **Spec Health Report** at the end of the document summarizing findings. This is not a
blocker — the spec is still delivered — but it gives the developer a clear picture of what is
solid versus what needs owner confirmation.

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

- **Never silently resolve a conflict.** Surface it, propose a resolution, and wait for confirmation.
- **Never omit a section.** If a section has no source material, mark it explicitly with the ⚠️ tag.
- **Prefer precision over completeness.** A requirement like "the system should be fast" is not acceptable. Restate it as "the system SHOULD respond to API requests within 200ms at p99 under normal load" or flag it as ambiguous.
- **Consolidate without losing signal.** When merging overlapping sources, ensure no distinct idea is dropped. If two sources say the same thing differently, use the clearer wording and note the merge.
- **Preserve intent over wording.** Source documents may be informal. Rewrite for clarity but do not change meaning. When in doubt, quote the original and add your interpretation.
- **Assumptions must be visible.** Every assumption made to fill a gap must appear in Section 10 and the Spec Health Report.
- **Ask before assuming on blockers.** If a gap would prevent a whole section from being written, stop and ask rather than fabricating content.

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
