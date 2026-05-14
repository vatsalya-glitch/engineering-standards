# Architecture Decision Record Template

> An Architecture Decision Record (ADR) captures a
> significant technical decision made during a project
> — what was decided, why, what alternatives were
> considered, and what the consequences are.
>
> Write one ADR per significant decision.
> Write it at the moment the decision is made.
> Never reconstruct from memory.

---

## How to Use This Template

1. Copy this file
2. Rename it: `ADR-XXX-short-title.md`
   Example: `ADR-001-delta-lake-over-parquet.md`
3. Fill in every section
4. Commit it alongside the code that implements
   the decision
5. Never delete an ADR — supersede it with a new one

---

## ADR Numbering Convention
ADR-001 → first decision
ADR-002 → second decision
ADR-003 → third decision

Decisions are numbered in the order they are made —
not by importance. A low number means it was decided
early, not that it is more important.

---

## ADR Status Definitions
PROPOSED   → under discussion, not yet decided
ACCEPTED   → decided and being implemented
SUPERSEDED → replaced by a newer ADR (link to it)
DEPRECATED → no longer relevant (explain why)
REJECTED   → considered but not adopted (explain why)

---

# ADR-XXX — [Short Title of Decision]

**Status:** `PROPOSED` | `ACCEPTED` | `SUPERSEDED` | `DEPRECATED` | `REJECTED`
**Date:** YYYY-MM-DD
**Author:** [your name / GitHub handle]
**Project:** [project name]
**Supersedes:** [ADR-XXX if this replaces a previous decision, otherwise N/A]
**Superseded by:** [ADR-XXX if this has been replaced, otherwise N/A]

---

## Context

> Describe the situation that required a decision.
> What problem were you solving?
> What constraints existed?
> What were the technical, business, or operational
> pressures that made this decision necessary?
>
> Write this as if explaining to someone who was not
> in the room when the decision was made.
> Be specific. Avoid vague statements like
> "we needed something better".

[Your context here]

---

## Decision

> State the decision clearly and directly.
> One or two sentences maximum.
> Start with "We will..." or "We decided to..."
>
> This section should be immediately understandable
> without reading any other section.

We will / We decided to...

---

## Alternatives Considered

> List every alternative that was seriously considered.
> For each alternative explain:
> - What it is
> - Why it was considered
> - Why it was not chosen
>
> Never leave this section empty.
> If you only considered one option — you did not
> make a decision, you made an assumption.

### Alternative 1 — [Name]

**What it is:**
[Brief description]

**Why considered:**
[What made this a viable option]

**Why rejected:**
[Specific reasons this was not chosen]

---

### Alternative 2 — [Name]

**What it is:**
[Brief description]

**Why considered:**
[What made this a viable option]

**Why rejected:**
[Specific reasons this was not chosen]

---

### Alternative 3 — [Name] *(add more as needed)*

**What it is:**
[Brief description]

**Why considered:**
[What made this a viable option]

**Why rejected:**
[Specific reasons this was not chosen]

---

## Consequences

> Describe what becomes true as a result of
> this decision.
> Include both positive and negative consequences.
> Be honest about the tradeoffs.
>
> Every decision has consequences.
> If you cannot identify any negative consequences
> — you have not thought hard enough.

### Positive Consequences
- [What this decision enables]
- [What problems it solves]
- [What it makes easier]

### Negative Consequences
- [What this decision costs]
- [What it makes harder]
- [What technical debt it introduces]
- [What future decisions it constrains]

---

## Constraints Accepted

> List the constraints this decision places on
> future work. These are the guardrails that future
> engineers must respect because of this decision.
>
> Example:
> "All Silver writes must include a _run_date column
> because this decision relies on partition-level
> overwrite by run date."

- [Constraint 1]
- [Constraint 2]
- [Constraint 3]

---

## Validation

> How will you know this decision was correct?
> What does success look like?
> What would cause you to revisit this decision?

**Success looks like:**
[Measurable or observable outcome that confirms
the decision was right]

**Revisit this decision if:**
[Conditions that would make this decision wrong
or obsolete]

---

## References

> Link to any external resources, documentation,
> benchmarks, or prior art that informed this decision.

- [Link or reference 1]
- [Link or reference 2]

---

## Notes

> Any additional context, open questions, or
> follow-up actions that don't fit elsewhere.

[Any additional notes]

---

*This ADR follows the
[engineering-standards](https://github.com/vatsalya-glitch/engineering-standards)
maintained by vatsalya-glitch.*
