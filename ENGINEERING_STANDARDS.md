# Engineering Standards
### Universal Principles for Data Engineering

> These standards are stack-agnostic and apply to any
> data engineering project regardless of technology,
> company, or scale. Built from production
> data engineering experience.

---

## The Standards
1. Infrastructure Before Application Code
2. Understand Data Before Processing It
3. Local First, Staging Second, Production Last
4. Fail Fast and Loudly at Every Boundary
5. Every Layer Must Be Independently Reprocessable
6. Schema Is a Contract, Not a Suggestion
7. Idempotency Is Non-Negotiable
8. Observability Is Designed In, Not Added Later
9. Test Behaviour, Not Implementation
10. Document Decisions at the Moment They Are Made
---

---

## Principle 1 — Infrastructure Before Application Code

**Statement:**
> Never write a single line of application code until
> the infrastructure it depends on is defined and
> provisioned.

**Reasoning:**
Application code is built on top of infrastructure.
Infrastructure defines the boundaries, contracts, and
resources that code operates within. Writing code
against undefined infrastructure means building on
assumptions — and assumptions in engineering always
surface as production incidents. Defining infrastructure
first forces clarity on storage contracts, access
patterns, network boundaries, and cost implications
before any logic is written.

**Violation:**
Skipping infrastructure definition leads to code that
works in isolation but fails in integration. The failure
mode is always the same — the code is correct, the
foundation wasn't there.

**Signal:**
On any new project, a Data Engineer's first commit is
always infrastructure definition. Never application
logic.

---

## Principle 2 — Understand Data Before Processing It

**Statement:**
> Always profile and explore source data before
> defining schemas, writing transformations, or making
> pipeline design decisions.

**Reasoning:**
Data is always messier than documentation describes.
Null rates are higher than expected. Value distributions
break assumptions. Edge cases exist that no stakeholder
mentioned. Every design decision made without seeing
real data is a guess. Guesses become bugs. Bugs in data
pipelines corrupt downstream decisions — which are often
invisible until significant damage is done.

**Violation:**
Designing transformations based on assumed data quality
leads to silent failures — data that looks correct but
contains systematic errors that only surface when
business decisions go wrong.

**Signal:**
A Data Engineer's first question on any project is always:
"Can I access sample data?" Not "Where is the
codebase?"

---

## Principle 3 — Local First, Staging Second, Production Last

**Statement:**
> Every pipeline must be fully executable locally,
> validated on a staging environment that mirrors
> production, before any production system is touched.

**Reasoning:**
Production systems serve real users, cost real money,
and carry real risk. Local development enables fast,
cheap iteration with zero risk. Staging bridges the gap
between local assumptions and production reality —
exposing integration failures, permission issues, and
scale differences before they become incidents.
Skipping any environment in this progression transfers
risk to the next environment.

**The Three Environments:**

| Environment | Purpose | Cost | Risk |
|---|---|---|---|
| Local | Fast iteration | Zero | Zero |
| Staging | Integration validation | Low | Low |
| Production | Business use | Full | Real |

**Violation:**
Deploying directly from local to production converts
development bugs into production incidents. The cost
of fixing a bug grows exponentially at each environment
— cheap locally, expensive in staging, catastrophic
in production.

**Signal:**
A Data Engineer never deploys directly from local to
production. The question they always ask is: "Has this
been validated in staging?"

---

## Principle 4 — Fail Fast and Loudly at Every Boundary

**Statement:**
> Every boundary between systems must validate inputs
> aggressively and fail immediately with a clear,
> actionable error — never silently pass invalid data
> downstream.

**Reasoning:**
The cost of a data error compounds at every layer it
passes through undetected. An error caught at ingestion
is a five-minute fix. The same error discovered after
it has propagated through processing, serving, and
business decisions is a multi-day incident that destroys
trust. Loud, early failures are a feature — not a sign
of fragility.

**Violation:**
Silent failures are the most dangerous failure mode in
data engineering. Data that looks valid but isn't —
zero rows, wrong types, missing fields — flows
downstream undetected and corrupts every system it
touches.

**Signal:**
A Data Engineer adds explicit validation at every system
boundary. Each boundary is a contract. Contracts are
enforced strictly, not trusted blindly.

---

## Principle 5 — Every Layer Must Be Independently Reprocessable

**Statement:**
> Every layer of a data system must be reprocessable
> from its own inputs without modifying, re-executing,
> or depending on layers above or below it.

**Reasoning:**
Pipelines fail. Business logic changes. Source data
gets corrected. When reprocessing is needed, it should
be surgical — affecting only the layer that needs to
change. Tightly coupled layers force full reprocessing
when partial reprocessing is sufficient, multiplying
cost, time, and risk. Independent layers give operators
precise control over what gets reprocessed and when.

**Violation:**
Tightly coupled layers turn a simple fix into a full
pipeline rerun. A rerun that should take minutes takes
hours and carries the risk of corrupting data in layers
that didn't need to change.

**Signal:**
For every layer, a Data Engineer asks: "If I had to
reprocess only this layer, what is the minimum I need?"
If the answer involves touching another layer — the
design has a coupling problem.

---

## Principle 6 — Schema Is a Contract, Not a Suggestion

**Statement:**
> Every data boundary must have an explicit, enforced
> schema. Schema changes are treated as breaking
> changes — versioned, communicated, and never silent.

**Reasoning:**
In any data system, schema is the contract between
producer and consumer. A producer that changes its
schema without notice breaks every downstream consumer
that trusted that contract. Treating schema as a formal
contract forces intentionality around changes and
prevents the most common class of silent data pipeline
failures — structural changes that propagate undetected
through every downstream system.

**Violation:**
An unannounced schema change at the source silently
breaks every downstream system that trusted the
previous contract. The failure is discovered not at
ingestion but at the business layer — after corrupted
data has influenced decisions.

**Signal:**
A Data Engineer versions schemas alongside code. A schema
change triggers a review and a migration plan — not
a hotfix.

---

## Principle 7 — Idempotency Is Non-Negotiable

**Statement:**
> Every pipeline operation must produce identical
> results when executed multiple times with the same
> input. Re-execution must always be safe.

**Reasoning:**
Pipelines fail and get rerun. Orchestrators retry
automatically. Engineers manually rerun failed jobs.
These are not edge cases — they are normal operational
events. A pipeline that is not idempotent is a pipeline
that cannot be safely operated. Non-idempotent
operations turn routine retries into data corruption
incidents.

**Violation:**
A non-idempotent write operation on retry produces
duplicate data. Duplicate data in analytical systems
produces double-counted metrics. Double-counted metrics
produce wrong business decisions. What began as a
routine retry becomes a data integrity incident.

**Signal:**
A Data Engineer asks about every write operation: "What
happens if this runs twice with the same input?" If
the answer is anything other than "exactly the same
result" — it is not production-ready.

---

## Principle 8 — Observability Is Designed In, Not Added Later

**Statement:**
> Logging, monitoring, and alerting are architectural
> requirements designed into every system from the
> start — not features added after something breaks
> in production.

**Reasoning:**
A system without observability is a black box. Problems
in black boxes are discovered by users — not by
operators. The later a problem is discovered, the more
data it has corrupted, the more users it has affected,
and the harder it is to diagnose. Observability
designed in from the start means problems are detected
by the system before users notice them.

**The Three Layers:**

| Layer | Purpose |
|---|---|
| Logging | What happened, when, with what data |
| Monitoring | Is the system behaving as expected |
| Alerting | Notify the right people at the right time |

**Violation:**
A system without observability forces post-mortem
debugging from memory and circumstantial evidence.
Every investigation starts from zero. Every incident
takes longer to resolve than it should.

**Signal:**
A Data Engineer first question when any system fails is:
"What do the logs say?" If the answer is "we don't
have good logs" — that is a higher priority fix than
the immediate bug.

---

## Principle 9 — Test Behaviour, Not Implementation

**Statement:**
> Tests must verify what a system does to its inputs
> — not how it is internally implemented. A valid
> refactor must never break a passing test.

**Reasoning:**
Tests that verify internal implementation couple the
test suite to the code structure. Any refactor — even
one that improves the code without changing its
behaviour — breaks these tests. This trains engineers
to avoid refactoring, leading to increasingly
unmaintainable systems. Tests that verify behaviour
survive any internal change and only break when the
system genuinely stops doing what it should.

**The Distinction:**

| Type | Question |
|---|---|
| Implementation test | Did the code call function X? |
| Behaviour test | Did the output satisfy condition Y? |

**Violation:**
An implementation-coupled test suite breaks on every
refactor regardless of whether behaviour changed.
Engineers stop refactoring to avoid breaking tests.
Code quality degrades. Technical debt compounds.

**Signal:**
A Data Engineer asks about every test: "Would this test
still pass if I rewrote the implementation completely
but kept the same input-output behaviour?" If no —
it is testing the wrong thing.

---

## Principle 10 — Document Decisions at the Moment They Are Made

**Statement:**
> Architecture decisions, design rationale, and system
> contracts are documented at the time they are made
> — not reconstructed from memory after the fact.

**Reasoning:**
Code describes what a system does. Documentation
describes why it was built that way. Without the why,
future engineers — including the original author months
later — make changes that violate assumptions,
reintroduce solved problems, or break implicit
contracts. The most dangerous decisions are the ones
nobody remembers making. The best time to document a
decision is when the reasoning is clearest — at the
moment of decision.

**What Must Be Documented:**

| Type | Contents |
|---|---|
| Architecture decisions | Why this approach over alternatives |
| Data contracts | What each system guarantees to consumers |
| Operational procedures | How to operate, reprocess, and recover |

**Violation:**
Undocumented decisions get reversed by future engineers
who don't know why they were made. Each reversal risks
reintroducing the exact problem the original decision
solved — with no record of why it was a problem in
the first place.

**Signal:**
A Data Engineer writes the decision record at the moment
of decision — not at the end of the sprint. The record
captures not just what was decided but what alternatives
were considered and why they were rejected.

---

## Applying These Standards

These principles are applied across every project at
[github.com/vatsalya-glitch](https://github.com/vatsalya-glitch).

Each project references these standards and documents
deviations with explicit reasoning in its own ADR.
