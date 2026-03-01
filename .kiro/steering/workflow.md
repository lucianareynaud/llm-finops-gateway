# Workflow Steering

## Workflow intent
This repository must be developed as a tightly governed, credit-efficient sequence of bounded work units.

The purpose of this workflow is not merely to keep the project organized. Its purpose is to protect architectural coherence, preserve the product thesis, and prevent wasteful agent behavior.

This project is being built under explicit resource constraints:
- limited Kiro credits
- limited Cursor API usage
- limited tolerance for exploratory rewrites
- high need for fast portfolio-grade completion

Therefore, the workflow must optimize for:
- low ambiguity
- low rework
- high interpretability
- strict sequencing
- bounded agent execution
- explicit acceptance criteria

## Primary workflow principle
Kiro must not be used to discover what the product is.

Kiro must only be used to implement already-decided work.

That means:
- product direction is decided before implementation
- architecture is decided before implementation
- spec boundaries are decided before implementation
- interfaces are decided before implementation
- acceptance criteria are decided before implementation

The repository must make architectural improvisation difficult.

## Implementation order rule
All work must happen in a strict sequence.

The required order for this project is:

1. steering
2. repo foundation
3. API contracts and schemas
4. request context and policy router
5. provider boundary
6. OpenTelemetry tracing
7. cost estimation
8. strategic reporting
9. demo API support
10. demo console
11. Docker and developer experience
12. portfolio polish

No step may be treated as “optional cleanup later” if it is required to stabilize the next step.

## Steering-first rule
No implementation work may begin before the steering layer is complete enough to constrain the implementation.

At minimum, the following steering documents must exist before Kiro writes meaningful code:
- `product.md`
- `architecture.md`
- `observability.md`
- `workflow.md`
- `quality.md`
- `demo.md`

If these documents are incomplete or contradictory, the next implementation step is not ready.

## Spec-first rule
Each meaningful module or milestone must have:
- `spec.md`
- `design.md`
- `tasks.md`

Kiro must not be asked to “build the module” unless those three files already exist and are internally coherent.

The spec layer is the operational contract for agent execution.

## Work unit rule
All implementation must be broken into bounded work units.

A valid work unit must:
- map to exactly one spec or sub-spec
- have a clearly stated scope
- identify allowed files to change
- identify forbidden areas of change
- define observable acceptance criteria
- end with a concrete validation step

A work unit must be small enough that:
- the expected diff is understandable
- the review burden stays low
- the failure surface remains narrow
- the result can be validated before proceeding

## Work unit anti-pattern
The following requests are disallowed because they waste credits and increase rework:
- “implement the whole project”
- “make the app production-ready”
- “build the frontend and backend”
- “improve architecture as needed”
- “finish the remaining specs”
- “clean up while you’re there”
- “make it more robust everywhere”

These requests are too open-ended and invite agent improvisation.

## One-spec-at-a-time rule
Only one spec may be active at a time.

A later spec may not begin until:
- the current spec’s tasks are complete
- the current spec’s acceptance criteria are satisfied
- local validation passes
- the repository still conforms to steering documents

This rule exists to prevent partial completion across multiple fronts.

## Human role in the workflow
The human owner of the repository is responsible for:
- product thesis
- architecture decisions
- naming
- scope control
- reviewing Kiro output
- deciding whether a work unit is accepted
- deciding whether to proceed to the next spec

The human owner must not abdicate these responsibilities to Kiro.

## Kiro role in the workflow
Kiro is an execution engine for bounded work.

Kiro may:
- implement code for a defined spec
- create files already implied by a defined design
- write tests required by the spec
- update tasks within the current spec when completed
- make local, necessary adjustments inside the current work boundary

Kiro must not:
- redefine product scope
- invent new modules outside the spec
- refactor unrelated areas “for cleanliness”
- introduce speculative platform features
- broaden the demo into a full application
- add runtime dependencies not justified by the current spec
- silently change frozen public contracts
- implement future specs early because placeholders exist

## Cursor role in the workflow
Cursor is not the main implementation engine for this project.

Cursor should be reserved for:
- highly localized corrections
- quick inline edits
- short code inspections
- small bug fixes
- type/lint/test cleanup
- final polishing of already implemented areas

Cursor should not be used for:
- architectural exploration
- large refactors
- broad repo-wide initiative
- product discovery
- vague iterative design discussions

## Credit-efficiency rule
Every agent invocation must be economically justified.

Before invoking Kiro, the current task must already answer:
1. What exactly is being built?
2. Which files are allowed to change?
3. Which files are not allowed to change?
4. What behavior is expected at the end?
5. How will success be validated?

If these five questions are not answered, the work is not ready for Kiro.

## Pre-invocation checklist
Before sending any implementation task to Kiro, confirm all of the following:
- the relevant steering docs exist
- the target spec exists
- the design exists
- the task list exists
- acceptance criteria are written
- the work is limited in scope
- the files to change are known
- the next validation command is known

If any item above is missing, stop and prepare the spec layer first.

## Review-after-every-unit rule
After each work unit:
- inspect the diff
- compare the diff against the current spec
- run validation locally
- confirm no unrelated files were changed
- confirm the implementation still fits the product thesis
- only then move to the next work unit

No chained implementation should proceed on trust alone.

## Validation rule
No spec is considered complete without validation.

At each applicable stage, validation should include some subset of:
- unit tests
- contract tests
- integration tests
- lint
- typing
- local manual run
- demo sanity check

Validation commands must be explicit in the spec or tasks.
“Looks correct” is not a validation method.

## Definition of done rule
A spec is done only when:
- all required tasks are complete
- acceptance criteria are satisfied
- validation passes
- the result matches steering documents
- no unrelated architectural drift has been introduced

A spec is not done merely because code exists.

## Contract-freeze rule
The following elements are considered frozen public contracts once introduced:
- request schema
- response schema
- report schema
- demo-facing API shape
- route decision structure
- core telemetry field model

These may change only with deliberate spec updates, not casually during implementation.

## Architecture preservation rule
The following architectural properties are non-negotiable:
- route handlers remain thin
- routing policy remains explicit and deterministic
- provider logic remains isolated
- pricing logic remains isolated
- telemetry remains centralized
- reporting remains separate from raw tracing interpretation
- demo remains thin and subordinate
- Langfuse readiness does not become premature Langfuse coupling

Any implementation that erodes these properties must be rejected or corrected immediately.

## Demo control rule
The demo is not allowed to dictate the backend architecture.

The demo may require:
- stable response fields
- lightweight reporting endpoints
- simple static assets
- understandable run summaries

The demo may not justify:
- frontend-heavy framework adoption
- backend sprawl
- auth
- persistence complexity
- complex state management
- analytics platform scope

## Reporting control rule
Strategic reporting is required, but must remain lightweight in the first artifact.

It should answer product-relevant FinOps questions without expanding into:
- full BI tooling
- warehouse design
- long-term analytics infrastructure
- generalized cost platform behavior

The reporting layer must remain focused on recent runs and immediate decision support.

## Dependency discipline rule
Every new dependency must justify itself against the product thesis.

A dependency is acceptable only if it clearly strengthens:
- the gateway runtime
- observability quality
- testability
- cost/decision reporting
- demo clarity
- packaging/dev experience

A dependency is not justified by:
- convenience alone
- fashion
- future possibility
- “might be useful later”

## Refactoring rule
Refactoring is allowed only when one of the following is true:
- required by the current spec
- required to preserve architecture
- required to pass validation
- required to remove newly introduced duplication inside the current work unit

Refactoring is not allowed as open-ended beautification.

## Failure containment rule
If a work unit goes wrong:
- do not ask the agent to keep moving
- stop
- inspect the failure
- tighten the spec or design
- correct the current unit
- resume only when the unit is clean

Unresolved drift compounds quickly and burns credits.

## Spec progression for this repository
The intended progression for this repository is:

### Spec 001 — Repo foundation
Establish minimal repo structure, packaging baseline, and project constitution.

### Spec 002 — API contracts and schemas
Freeze inference and report-facing contracts.

### Spec 003 — FinOps policy router
Implement explicit, deterministic routing based on business context.

### Spec 004 — Provider boundary
Implement normalized provider execution behind a stable interface.

### Spec 005 — OTel tracing
Instrument the request lifecycle with OpenTelemetry in a Python-solid way.

### Spec 006 — Cost estimation
Attach deterministic cost estimation and route-aware FinOps metadata.

### Spec 007 — Strategic reporting
Produce lightweight aggregate outputs with management relevance.

### Spec 008 — Demo API support
Expose only the endpoints needed to support the demo console.

### Spec 009 — Demo console
Build a minimal static HTML/CSS/JS console that reveals the gateway’s value.

### Spec 010 — Docker and developer experience
Package the app cleanly for local and demo use.

## What must not happen
The following must not happen at any point:
- Kiro jumping to later specs early
- frontend work starting before the reporting/API layer is stable
- provider complexity growing before routing and telemetry are clear
- Langfuse integration appearing before the OTel foundation is stable
- persistence complexity being introduced before the product proves itself
- demo polish consuming budget before the gateway is believable
- portfolio prose being written before the product’s behavior is real

## Workflow tone
This repository should be developed with:
- restraint
- discipline
- bounded ambition
- interpretability
- credit efficiency
- architectural seriousness

It should not be developed with:
- speculative enthusiasm
- framework accumulation
- parallel chaos
- architectural theatre
- agent-led product discovery

## Workflow success condition
The workflow is successful if:
- the repository advances in coherent, inspectable increments
- Kiro behaves as an executor rather than a co-founder
- architectural drift is prevented early
- credits are spent on implementation, not ambiguity
- the resulting artifact is small, sharp, and strategically legible
