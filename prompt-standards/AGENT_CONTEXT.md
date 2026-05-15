# AGENT_CONTEXT.md

> Read this document completely before doing anything.
> This is your operating manual for every session.

---

## What This Repository Is

This repository is a living personal engineering
standard for building production-grade data
engineering projects.

It contains:
```
→ The principles that govern every decision
→ The sequence every project follows
→ The structure every step uses
→ The strategies that shape every prompt
→ The format for every documented decision
```

All documents referenced in this file live
inside this repository. You do not need to
search externally.

---

## The Nature of These Standards

These standards are not rigid rules.
They are the current best thinking of the
engineer who owns this repository.

They evolve with every project.
They reflect what has been learned so far —
not theoretical completeness.

Your job is to apply them precisely as they
exist today. Not to interpret them. Not to
expand them. Not to question them mid-project.

---

## Your Role in Evolution

If during a project you notice:
```
→ A gap in the standards
→ An improvement that could be made
→ A new pattern worth documenting
```

Do not modify any standard document during
the project.
Do not propose changes unsolicited mid-step.

Instead:
```
→ Note the observation
→ Surface it to the engineer at session end
→ Engineer decides whether to update standards
```

This keeps current work clean and lets
standards evolve deliberately.

---

## Step 1 — Understand the Session First

Before reading any standards or doing any work —
ask the engineer this question:

```
"What are we building today and where
are we in the process?

To help you answer efficiently — you can share:

PROJECT
→ What is the project name?
→ What problem does it solve?
→ What is the repo URL?

CURRENT POSITION
→ Are we starting fresh or continuing?
→ If continuing — which phase and step?

CURRENT STATE
→ What has already been built?
→ What was the last thing completed?
→ Were there any unresolved issues?

You can answer in any format — a few sentences,
a list, or paste previous context.
I will extract what I need and confirm it
back to you before doing anything."
```

Wait for the engineer to respond.
Do not read any document yet.
Do not assume anything.
Do not begin any work.

---

## Step 2 — Read the Standards

After the engineer responds — read these documents
in this order from inside this repository.
Extract only what is specified.
Do not re-read between steps unless instructed.

---

**Document 1**
```
FILE:    ENGINEERING_STANDARDS.md
EXTRACT: The 10 principles by name and
         statement only.
         Reference specific principles
         per step as instructed.
```

**Document 2**
```
FILE:    data-pipeline-playbook.md
EXTRACT: The full phase and step sequence.
         The current phase context.
         The current step standard and
         signal only.
         Do not read steps beyond current.
```

**Document 3**
```
FILE:    prompt-standards/PROMPT_TEMPLATE.md
EXTRACT: The 9 section structure.
         What each section requires from you.
         Every step follows this format.
```

**Document 4**
```
FILE:    prompt-standards/STRATEGY_REGISTRY.md
EXTRACT: Which prompting strategy applies
         to each section of the current step.
         Apply it. Do not deviate.
```

**Document 5**
```
FILE:    ADR_TEMPLATE.md
EXTRACT: The ADR format.
         When an ADR is required.
         You will be told when to write one.
```

---

## Step 3 — Confirm Understanding

After reading all documents — extract the
structured information from the engineer's
response and confirm in exactly this format:

```
SESSION CONFIRMATION
────────────────────
PROJECT:       [name and repo]
PHASE:         [phase number and name]
STEP:          [step number and name]
ALREADY BUILT: [list or "nothing yet"]
OPEN ISSUES:   [list or "none"]

APPLICABLE PRINCIPLES:
→ Principle [X]: [one line — how it applies]
→ Principle [X]: [one line — how it applies]

SCOPE — IN:
→ [what will be built]
→ [what will be built]

SCOPE — OUT:
→ [what will not be built]
→ [what will not be built]

VALIDATION GATES:
→ Gate 1: [what and how]
→ Gate 2: [what and how]

READY TO BUILD: YES
Awaiting your approval to proceed.
```

**Example of a correctly filled confirmation:**

```
SESSION CONFIRMATION
────────────────────
PROJECT:       spotify-lakehouse
               github.com/vatsalya-glitch/spotify-lakehouse
PHASE:         Phase 0 — Understand & Design
STEP:          Step 0.1 — Define the Problem
ALREADY BUILT: Nothing yet
OPEN ISSUES:   None

APPLICABLE PRINCIPLES:
→ Principle 2: Explore data before processing —
  define the problem before writing any code
→ Principle 10: Document decisions at the moment
  they are made — answers go into ADR immediately

SCOPE — IN:
→ Document answers to the 6 problem definition
  questions
→ Write ADR-001 capturing the problem definition

SCOPE — OUT:
→ No code written at this step
→ No architecture decisions yet
→ No technology choices yet

VALIDATION GATES:
→ Gate 1: All 6 questions answered in writing
→ Gate 2: ADR-001 committed to repo

READY TO BUILD: YES
Awaiting your approval to proceed.
```

---

## Step 4 — Wait for Approval

Do not begin building until the engineer responds
with this exact phrase:

```
APPROVED — proceed to [step number]
```

Any other response is not approval.
If unclear — ask:

```
"Do I have approval to proceed to [step number]?"
```

---

## Step 5 — Execute the Current Step Only

When approved:

```
BUILD    → current step scope only
           one logical component at a time
           complete before moving on

VALIDATE → run every validation gate
           show actual terminal output
           never summarise or simulate

STOP     → at scope boundary
           when anything is ambiguous
           when a decision is needed
           when a validation gate fails

ASK      → before assuming anything
           before substituting technology
           before making any design decision
```

---

## Validation Gate Format

For every gate show exactly this:

```
GATE:     [what is being validated]
COMMAND:  [exact command run]
OUTPUT:   [actual terminal output]
STATUS:   PASS | FAIL
ACTION:   [if FAIL — what was fixed]
```

Never write "this should work" or "tests pass".
Prove it. Show the output.

---

## Non-Negotiable Rules

```
ALWAYS
→ Ask before reading any document
→ Confirm understanding before building
→ Show actual output for every command
→ Stop at scope boundary
→ Ask when anything is unclear
→ Wait for explicit approval language
→ Write ADR when instructed

NEVER
→ Proceed without APPROVED signal
→ Simulate or summarise validation
→ Use TODOs or placeholders in code
→ Build beyond current step scope
→ Substitute technology without approval
→ Write tests designed only to pass
→ Make decisions that belong to engineer
→ Assume when something is unspecified
→ Modify any standard document mid-project
```

---

## Deviation Protocol

If any standard cannot be followed:

```
STEP 1 → Stop immediately
STEP 2 → State what cannot be followed
STEP 3 → State exactly why
STEP 4 → Propose one alternative
STEP 5 → Wait for explicit approval
STEP 6 → Document in ADR before proceeding
```

An undocumented deviation is a violation.

---

## Token Management

```
→ Read all 5 documents once at session start
→ Do not re-read between steps
→ Reference specific sections only when needed
→ Keep confirmations in the defined format
→ Keep responses precise and scoped
→ If context is becoming unclear — say so
   and ask engineer to re-anchor the session
```

---

## Reference Map

```
PRINCIPLES     → ENGINEERING_STANDARDS.md
SEQUENCE       → data-pipeline-playbook.md
STEP STRUCTURE → prompt-standards/PROMPT_TEMPLATE.md
STRATEGIES     → prompt-standards/STRATEGY_REGISTRY.md
DECISIONS      → ADR_TEMPLATE.md
```

---

*Attach this file at the start of every session.*
*You are working with vatsalya-glitch.*
*These are their standards. Follow them precisely.*
*Version 1.0 — May 2026*