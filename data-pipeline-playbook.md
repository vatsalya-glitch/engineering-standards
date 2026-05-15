# Data Pipeline Playbook

> A universal, stack-agnostic playbook for building
> production-grade data pipelines from scratch.
>
> This playbook defines the sequence, reasoning, and
> standards that govern every data pipeline project
> regardless of technology, company, or scale.
>
> Every step answers three questions:
> WHAT  → what is being built
> WHY   → why at this stage and not earlier or later
> HOW   → the execution standard

---

## The Governing Philosophy

An experienced data engineer does not just know how to build
things. They know what to build first, why in that
order, and what goes wrong when the order is violated.

This playbook encodes that sequencing intelligence.

Every phase exists because the phases before it make
it possible. Every step exists because skipping it
creates a specific, predictable failure downstream.

---

## The Universal Pipeline Phases

```
PHASE 0 → Understand & Design
PHASE 1 → Infrastructure
PHASE 2 → Ingestion
PHASE 3 → Processing
PHASE 4 → Serving
PHASE 5 → Orchestration
PHASE 6 → Validation & Hardening
PHASE 7 → Deployment
```

---

## PHASE 0 — Understand & Design

> Before writing a single line of code, understand
> the problem completely and design the solution
> explicitly.

### Why This Phase Exists

Every hour spent understanding the problem before
coding saves ten hours of rework after coding.
A pipeline built on misunderstood requirements is
not a pipeline that needs fixing — it is a pipeline
that needs rebuilding.

---

### Step 0.1 — Define the Problem

**WHAT:**
Document what business problem this pipeline solves,
who consumes the output, and what decisions it enables.

**WHY NOW:**
Every technical decision downstream is only as good
as the problem definition it serves. A technically
perfect pipeline that solves the wrong problem is
a failure.

**STANDARD:**
Answer these questions in writing before proceeding:
```
- What question does this pipeline answer?
- Who consumes the output and how?
- What does success look like?
- What is the acceptable latency of the data?
- What is the acceptable data quality threshold?
- What happens if this pipeline fails?
```

**SIGNAL:**
If you cannot answer all six questions — you do not
yet understand the problem well enough to build
a solution.

---

### Step 0.2 — Explore and Profile Source Data

**WHAT:**
Download or access a representative sample of source
data. Profile it. Document what you find.

**WHY NOW:**
Every assumption about data quality made without
looking at real data will be wrong in at least one
significant way. Schema definitions, cleaning rules,
quality thresholds, and edge case handling written
without data exploration are guesses. Guesses become
production bugs.

**STANDARD:**
For every source dataset document:
```
- Row count and column count
- Null rates per column
- Value distributions for key columns
- Min, max, mean for numeric columns
- Distinct value counts for categorical columns
- Date ranges for timestamp columns
- Identified edge cases and anomalies
- Data quality issues found
```

**SIGNAL:**
A Data Engineer never writes a schema definition or
cleaning rule without first looking at real data.

---

### Step 0.3 — Design the Architecture

**WHAT:**
Define the end-to-end architecture. Document every
layer, every system boundary, every data contract,
and every technology choice with its reasoning.

**WHY NOW:**
Architecture decisions made before coding are cheap.
Architecture decisions made during coding are
expensive. Architecture decisions made after coding
are catastrophic.

**STANDARD:**
Produce these artifacts before writing any code:
```
- Architecture diagram (layers, systems, data flow)
- Technology choices with ADR for each major decision
- Data contracts at every system boundary
- Schema definitions for every layer
- Estimated data volumes and growth projections
- Cost estimate for production infrastructure
```

**SIGNAL:**
Every technology choice has a documented ADR.
No choice is made because "it's what I know" without
also documenting why it is the right fit for this
problem.

---

## PHASE 1 — Infrastructure

> Define and provision all infrastructure before
> writing any application code.

### Why This Phase Exists

Application code needs somewhere to land. Storage
must exist before data can be written. Permissions
must exist before services can communicate. Networks
must exist before systems can connect. Infrastructure
defined after code is written means the code was
built on assumptions — and assumptions always fail
in integration.

---

### Step 1.1 — Define Infrastructure as Code

**WHAT:**
Write all infrastructure definitions in code — never
provision manually through a console or UI.

**WHY NOW:**
Manual infrastructure cannot be reproduced reliably,
cannot be version controlled, cannot be reviewed,
and cannot be destroyed cleanly. Infrastructure as
code is reproducible, reviewable, and reversible.

**STANDARD:**
Every infrastructure resource must be:
```
- Defined in code (Terraform, Pulumi, CDK, etc.)
- Version controlled alongside application code
- Reviewed before provisioning
- Destroyable with a single command
- Documented with its purpose and dependencies
```

**SIGNAL:**
If a resource exists in production that does not
exist in infrastructure code — the infrastructure
code is wrong. Production is not the source of truth.
The code is.

---

### Step 1.2 — Provision Local Environment

**WHAT:**
Set up the complete local development environment
that mirrors production using local substitutes
for every cloud service.

**WHY NOW:**
Local environment must exist before any application
code is written so that every component can be
tested immediately as it is built. A local environment
set up after code is written means the code was built
and tested in isolation — not in context.

**STANDARD:**
Every cloud service used in production must have a
local substitute:
```
Cloud storage      → local filesystem
Cloud database     → local database (e.g. DuckDB)
Message queue      → local emulator
Orchestrator       → local Docker instance
Compute cluster    → local mode execution
```

**SIGNAL:**
`make run-local` executes the complete pipeline
with zero cloud dependencies from a fresh checkout.

---

### Step 1.3 — Provision Staging Environment

**WHAT:**
Provision a staging environment that is
architecturally identical to production but isolated
from it, using real cloud services with reduced
scale and cost controls.

**WHY NOW:**
Staging must exist before integration testing begins.
Integration testing cannot happen in local because
local substitutes do not replicate cloud behaviour
exactly. Staging catches the gap between local
assumptions and production reality.

**STANDARD:**
Staging environment must:
```
- Use real cloud services (not emulators)
- Be completely isolated from production
- Use a representative sample of real data
- Have identical schema and configuration to prod
- Have cost controls (budget alerts, auto-shutdown)
- Be provisionable and destroyable on demand
```

**SIGNAL:**
Staging is never manually configured to differ
from production. Any difference is a bug in the
staging definition.

---

## PHASE 2 — Ingestion

> Build the layer that moves raw data from source
> systems into your pipeline's storage boundary.

### Why This Phase Exists

Ingestion is the entry point of the pipeline. Every
downstream layer depends on ingestion being correct.
A bug in ingestion propagates to every downstream
layer. Ingestion built before infrastructure means
it has nowhere to write. Ingestion built before data
exploration means it makes wrong assumptions about
the data it will receive.

---

### Step 2.1 — Define and Enforce Source Schemas

**WHAT:**
Define the explicit schema expected from every source
system. Implement schema enforcement that fails
loudly on any violation.

**WHY NOW:**
Schema is the contract between source systems and
your pipeline. This contract must be defined before
ingestion code is written — not inferred from the
first successful run.

**STANDARD:**
For every source:
```
- Define required columns explicitly
- Define expected types for key columns
- Define acceptable null rates
- Define acceptable value ranges
- Implement validation that fails fast on violation
- Log specific violation details on failure
```

**SIGNAL:**
A schema violation at the source produces an
immediate, specific, actionable error message.
It never produces silent nulls or partial data
in the destination.

---

### Step 2.2 — Build the Ingestion Layer

**WHAT:**
Build the code that reads from source systems,
validates against the schema contract, adds
ingestion metadata, and writes to the raw storage
layer in the target format.

**WHY NOW:**
Ingestion is the foundation every other layer reads
from. It must be built and validated before
processing or serving layers are written.

**STANDARD:**
Every ingestion implementation must:
```
- Validate schema before writing anything
- Add standard metadata columns:
    _ingested_at  → ingestion timestamp
    _source       → source system identifier
    _run_date     → logical processing date
- Write in a columnar format (Parquet, ORC, Avro)
- Partition output by a time dimension
- Be idempotent — safe to rerun for the same date
- Log row counts at source and destination
- Fail loudly if row count is unexpectedly zero
```

**SIGNAL:**
Running ingestion twice for the same date produces
exactly the same output both times.

---

### Step 2.3 — Test the Ingestion Layer

**WHAT:**
Write and run tests that verify ingestion behaviour
— not implementation. Validate locally before
staging.

**WHY NOW:**
Untested ingestion is the riskiest component in any
pipeline. Every downstream layer trusts ingestion.
A bug found in ingestion tests takes minutes to fix.
A bug found in production takes days and corrupts
data that may already be in use.

**STANDARD:**
Ingestion test suite must cover:
```
- Schema validation passes for valid input
- Schema validation fails for invalid input
- Metadata columns are added correctly
- Partitioning is correct
- Idempotency — second run produces same output
- Zero-row detection triggers appropriate error
- GCS/storage client calls behave correctly
  (use mocks — never real cloud in unit tests)
```

**SIGNAL:**
`make test` runs the full ingestion test suite
locally with zero cloud dependencies.

---

## PHASE 3 — Processing

> Build the layer that cleans, validates, enriches,
> and transforms raw ingested data into reliable,
> analysis-ready Silver data.

### Why This Phase Exists

Raw data is never ready for analysis. It contains
duplicates, wrong types, invalid values, missing
fields, and business logic that must be applied
before the data means anything. Processing exists
to make raw data trustworthy. It must read from
the ingestion layer — never directly from sources.

---

### Step 3.1 — Define Cleaning Rules

**WHAT:**
Document every cleaning rule before writing any
transformation code. Each rule must have a business
justification.

**WHY NOW:**
Cleaning rules written without documentation become
invisible business logic. Future engineers cannot
distinguish intentional transformations from
accidental ones. Documented rules are reviewable,
testable, and explainable.

**STANDARD:**
For every cleaning rule document:
```
- What the rule does
- Why this rule exists (business justification)
- What data it affects
- What happens to records that fail the rule
  (drop, flag, quarantine, dead-letter)
- What edge cases it handles
```

**SIGNAL:**
Every cleaning rule can be explained in one sentence
to a non-technical stakeholder.

---

### Step 3.2 — Build Transformations

**WHAT:**
Implement cleaning rules as explicit, testable,
documented transformation functions — one function
per logical concern.

**WHY NOW:**
Transformations are the business logic of the
pipeline. Business logic must be isolated,
documented, and testable. Monolithic transformation
scripts that do everything in one place are
untestable, unreviewed, and unmaintainable.

**STANDARD:**
Every transformation function must:
```
- Do one logical thing
- Have a docstring explaining what and why
- Have type hints on all parameters and return types
- Use structured logging not print statements
- Be independently testable without pipeline context
- Handle edge cases explicitly — never silently
```

**SIGNAL:**
Every transformation function can be imported and
tested in isolation without starting a cluster,
connecting to a database, or reading from storage.

---

### Step 3.3 — Build the Processing Orchestrator

**WHAT:**
Build the component that reads from the ingestion
layer, applies transformations in sequence, and
writes to the processed storage layer.

**WHY NOW:**
The orchestrator assembles tested transformations
into a complete processing job. It is built after
transformations are tested so it composes verified
components — not unverified ones.

**STANDARD:**
The processing orchestrator must:
```
- Read ONLY from the ingestion layer
  (never directly from sources)
- Apply transformations in documented sequence
- Log row counts before and after each transformation
- Write idempotently to the processed layer
- Report drop rates for every filtering operation
- Fail loudly if output row count is zero
```

**SIGNAL:**
The processing job can be rerun for any historical
date and produces identical output every time.

---

### Step 3.4 — Build Local Sink

**WHAT:**
Build a local substitute for the production storage
system that enables full local testing of the
processing layer without cloud dependencies.

**WHY NOW:**
Processing tests must run locally. Local tests that
depend on cloud storage are slow, costly, and
environment-dependent. A local sink with identical
interface to the production sink enables fast,
free, reliable local testing.

**STANDARD:**
The local sink must:
```
- Implement the identical interface as production sink
- Require zero cloud credentials or connections
- Support all operations used by the pipeline
- Produce queryable output for test verification
- Be swappable with the production sink via
  a single configuration flag
```

**SIGNAL:**
Switching between local and production sink requires
changing one configuration value — not rewriting code.

---

### Step 3.5 — Test the Processing Layer

**WHAT:**
Write and run tests that verify transformation
behaviour using real processing engine in local mode.

**WHY NOW:**
Processing contains the most complex business logic
in the pipeline. Complex logic has the most edge
cases. Edge cases found in tests take minutes to fix.
Edge cases found in production corrupt data.

**STANDARD:**
Processing test suite must cover:
```
- Each transformation function independently
- Deduplication removes exact duplicates only
- Type casting handles valid and invalid values
- Filtering removes correct rows
- Enrichment adds correct derived columns
- Edge cases: nulls, empty strings, out-of-range
- Idempotency: same input always same output
- Drop rate logging fires correctly
```

**SIGNAL:**
Every transformation rule has at least one test
that would fail if the rule were removed.

---

## PHASE 4 — Serving

> Build the layer that makes processed data
> queryable and usable by analysts, dashboards,
> and downstream systems.

### Why This Phase Exists

Processed data in a Silver layer is reliable but
not directly useful. Analysts need business-framed
tables — not engineering-framed ones. The serving
layer applies the final business logic, creates
the right granularity, and exposes data in the form
that answers the questions the pipeline was built
to answer. It must read from the processing layer
— never from ingestion or sources.

---

### Step 4.1 — Define the Serving Contract

**WHAT:**
Define every table, view, or model that the serving
layer will expose. Document who uses each one,
what question it answers, and what grain it operates
at.

**WHY NOW:**
Serving models built without a defined contract
become a dumping ground — every analyst requests
a new column, every new use case requires a schema
change. A defined contract forces discipline and
makes the serving layer stable.

**STANDARD:**
For every serving model document:
```
- Name and description
- Who consumes it
- What business question it answers
- Grain (one row per what?)
- Key columns and their business meaning
- Update frequency
- Data quality guarantees
```

**SIGNAL:**
Every serving model can be described in one sentence:
"One row per [grain], showing [metric] for [consumer]."

---

### Step 4.2 — Build Staging Models

**WHAT:**
Build lightweight models that clean and standardise
processed data for use by downstream models —
one staging model per processed source table.

**WHY NOW:**
Staging models create a stable interface between
the processing layer and the serving layer. If the
processing layer schema changes, only staging models
need updating — not every downstream model.

**STANDARD:**
Staging models must:
```
- Read ONLY from the processing layer
- Rename columns to consistent naming convention
- Cast types to serving layer standards
- Apply no business logic (that belongs in marts)
- Be named stg_[source_entity]
- Have schema tests for not_null and unique
  on primary keys
```

**SIGNAL:**
Staging models contain zero business logic.
They are structural — not analytical.

---

### Step 4.3 — Build Intermediate Models

**WHAT:**
Build models that apply business logic, joins, and
enrichment that is shared across multiple mart models.

**WHY NOW:**
Business logic that is duplicated across mart models
is business logic that gets fixed in one place and
forgotten in another. Intermediate models centralise
shared logic so it is defined once and tested once.

**STANDARD:**
Intermediate models must:
```
- Read ONLY from staging models
- Contain business logic shared by 2+ mart models
- Be named int_[descriptive_name]
- Not be exposed directly to analysts
- Have tests on key business rules
```

**SIGNAL:**
If the same business logic appears in two mart
models — it belongs in an intermediate model.

---

### Step 4.4 — Build Mart Models

**WHAT:**
Build the analyst-facing models that answer specific
business questions at the correct grain.

**WHY NOW:**
Mart models are the final output of the pipeline.
They are built last because they depend on staging
and intermediate models being correct. Building
them last means they compose verified components.

**STANDARD:**
Mart models must:
```
- Read ONLY from staging or intermediate models
- Answer one specific business question
- Be named mart_[business_domain]
- Be partitioned appropriately for query performance
- Have comprehensive schema tests
- Be documented with business context
```

**SIGNAL:**
An analyst can use a mart model without understanding
the pipeline that produced it.

---

### Step 4.5 — Test the Serving Layer

**WHAT:**
Run schema tests, data quality tests, and
freshness tests on every serving model.

**WHY NOW:**
The serving layer is what analysts trust. Trust
is built through tested guarantees — not hope.
Untested serving models are serving models that
will eventually serve wrong data silently.

**STANDARD:**
Serving layer tests must cover:
```
- Primary key uniqueness on every model
- Not null on every required column
- Referential integrity across related models
- Value range checks on key metrics
- Freshness checks (data not older than X hours)
- Row count checks (not fewer than X rows)
- Custom business rule tests
```

**SIGNAL:**
Every column that an analyst would trust has a
test that would fail if that trust were violated.

---

## PHASE 5 — Orchestration

> Build the layer that automates, schedules,
> monitors, and recovers the complete pipeline.

### Why This Phase Exists

A pipeline that requires manual execution is not
a production pipeline — it is a script. Orchestration
makes the pipeline autonomous, recoverable, and
observable. It is built after all pipeline components
are individually tested because orchestration
composes working components — it does not fix
broken ones.

---

### Step 5.1 — Define the Pipeline DAG

**WHAT:**
Define the complete directed acyclic graph of
pipeline tasks — every task, every dependency,
every trigger condition, and every failure mode.

**WHY NOW:**
The DAG is the specification for orchestration.
It must be defined before implementation so that
dependencies, retry logic, and alerting are designed
— not discovered during debugging.

**STANDARD:**
Every DAG must document:
```
- Every task and its purpose
- Every dependency between tasks
- Schedule and trigger conditions
- Retry policy per task
- Timeout per task
- Alerting on failure per task
- SLA expectations
```

**SIGNAL:**
The DAG can be explained to a non-technical
stakeholder as a sequence of steps with clear
checkpoints and recovery points.

---

### Step 5.2 — Implement the Orchestration Layer

**WHAT:**
Implement the DAG using the chosen orchestration
tool. Every task is a checkpoint. Every task is
independently rerunnable.

**WHY NOW:**
Orchestration is implemented after all components
are tested because it assembles verified components.
Orchestration built before components are tested
is orchestration that automates unverified code.

**STANDARD:**
Every orchestrated pipeline must:
```
- Have retries on every task (minimum 3)
- Have timeouts on every task
- Pass context between tasks explicitly
- Log task start, end, row counts, and duration
- Alert on failure with actionable context
- Be manually triggerable for any date range
- Support backfill without code changes
```

**SIGNAL:**
When a task fails, the pipeline retries
automatically, alerts the right person with
the right context, and can be manually rerun
from the exact point of failure.

---

## PHASE 6 — Validation & Hardening

> Validate the complete pipeline end-to-end and
> harden it against the failure modes identified
> during building.

### Why This Phase Exists

Individual components tested in isolation do not
guarantee the assembled pipeline works correctly.
End-to-end validation catches integration failures,
performance issues, and edge cases that only appear
when all components run together. Hardening addresses
the failure modes discovered during validation.

---

### Step 6.1 — End-to-End Local Validation

**WHAT:**
Run the complete pipeline locally from source to
serving layer and verify output correctness at
every layer.

**WHY NOW:**
Local end-to-end validation is the last checkpoint
before cloud deployment. Failures found here are
cheap to fix. Failures found in staging cost more.
Failures found in production cost most.

**STANDARD:**
End-to-end local validation must confirm:
```
- Source data flows correctly to ingestion layer
- Ingestion layer writes correct output to Bronze
- Processing layer reads Bronze correctly
- Processing layer writes correct output to Silver
- Serving layer reads Silver correctly
- Serving layer produces correct mart output
- Orchestrator triggers all tasks in correct order
- All tasks complete successfully
- Output data matches expected values
```

**SIGNAL:**
`make run-local` executes the complete pipeline
and produces verified output with zero manual steps.

---

### Step 6.2 — End-to-End Staging Validation

**WHAT:**
Run the complete pipeline on staging infrastructure
with real cloud services and a representative
sample of real data.

**WHY NOW:**
Staging validation catches the gap between local
assumptions and cloud reality — IAM permissions,
network latency, cloud service quotas, schema
compatibility, and real data edge cases.

**STANDARD:**
Staging validation must confirm:
```
- All IAM permissions are correctly configured
- All cloud services connect and authenticate
- Pipeline handles real data edge cases correctly
- Performance meets SLA requirements at sample scale
- Cost is within expected parameters
- Alerting fires correctly on injected failures
- Recovery from injected failures works correctly
```

**SIGNAL:**
Staging passes before any production deployment
is scheduled. No exceptions.

---

## PHASE 7 — Deployment

> Deploy the validated pipeline to production
> with full observability and a tested rollback plan.

### Why This Phase Exists

Deployment to production is the transfer of a
validated system into the environment where it
serves real users. It is the last phase because
every previous phase exists to make this phase
safe. A deployment without validation is a
gamble. A deployment with full validation is
a controlled transition.

---

### Step 7.1 — Deploy Infrastructure to Production

**WHAT:**
Apply production infrastructure using the same
infrastructure code used in staging — with
production-scale configuration.

**WHY NOW:**
Infrastructure is deployed before application code
because application code needs infrastructure to
exist. This applies in production just as it did
in local and staging.

**STANDARD:**
Production infrastructure deployment must:
```
- Use the same IaC code as staging
- Be applied through CI/CD — never manually
- Be reviewed and approved before applying
- Have a documented rollback procedure
- Have cost monitoring and budget alerts active
```

**SIGNAL:**
Production infrastructure is never manually
configured. Every resource in production exists
in infrastructure code.

---

### Step 7.2 — Deploy Application Code to Production

**WHAT:**
Deploy the validated pipeline application code
to production through the CI/CD pipeline.

**WHY NOW:**
Application code is deployed after infrastructure
because it depends on infrastructure existing.
It is deployed through CI/CD because manual
deployments are unreliable and unauditable.

**STANDARD:**
Production code deployment must:
```
- Pass all automated tests in CI before deploying
- Be deployed through CI/CD — never manually
- Include a deployment verification step
- Have a documented and tested rollback procedure
- Generate a deployment record with version,
  timestamp, and deploying engineer
```

**SIGNAL:**
Every production deployment is traceable to a
specific commit, a specific CI run, and a specific
engineer. No deployment is unaudited.

---

### Step 7.3 — Verify Production Deployment

**WHAT:**
Verify that the production deployment is functioning
correctly by running a controlled first execution
and validating output.

**WHY NOW:**
Deployment verification is the final gate before
the pipeline is considered live. A deployment that
has not been verified is a deployment that might
be silently broken.

**STANDARD:**
Production verification must confirm:
```
- Pipeline triggers correctly on schedule
- All tasks complete successfully
- Output row counts are within expected range
- Output data quality tests pass
- Alerting is active and correctly configured
- Dashboards and consumers receive correct data
- Performance meets SLA requirements
```

**SIGNAL:**
The pipeline is not considered live until
verification is complete and documented.

---

## The Complete Playbook at a Glance

```
PHASE 0 — Understand & Design
  0.1 → Define the problem
  0.2 → Explore and profile source data
  0.3 → Design the architecture

PHASE 1 — Infrastructure
  1.1 → Define infrastructure as code
  1.2 → Provision local environment
  1.3 → Provision staging environment

PHASE 2 — Ingestion
  2.1 → Define and enforce source schemas
  2.2 → Build the ingestion layer
  2.3 → Test the ingestion layer

PHASE 3 — Processing
  3.1 → Define cleaning rules
  3.2 → Build transformations
  3.3 → Build the processing orchestrator
  3.4 → Build local sink
  3.5 → Test the processing layer

PHASE 4 — Serving
  4.1 → Define the serving contract
  4.2 → Build staging models
  4.3 → Build intermediate models
  4.4 → Build mart models
  4.5 → Test the serving layer

PHASE 5 — Orchestration
  5.1 → Define the pipeline DAG
  5.2 → Implement the orchestration layer

PHASE 6 — Validation & Hardening
  6.1 → End-to-end local validation
  6.2 → End-to-end staging validation

PHASE 7 — Deployment
  7.1 → Deploy infrastructure to production
  7.2 → Deploy application code to production
  7.3 → Verify production deployment
```

---

## Applying This Playbook

This playbook is applied to every data pipeline
project at
[github.com/vatsalya-glitch](https://github.com/vatsalya-glitch).

Each project documents deviations from this playbook
in its own ADR with explicit reasoning.

A deviation is acceptable when it is:
```
- Consciously chosen
- Explicitly documented
- Justified with specific reasoning
- Not the result of skipping a step carelessly
```

An undocumented deviation is not a deviation.
It is a mistake.

---

*This playbook follows the
[Engineering Standards](../ENGINEERING_STANDARDS.md)
maintained by vatsalya-glitch.*

*Version 1.0 — May 2026*
*Author: vatsalya-glitch*
