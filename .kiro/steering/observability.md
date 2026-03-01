# Observability Steering

## Observability intent
`llm-finops-gateway` exists to make production LLM traffic observable, explainable, and governable.

Observability is therefore a first-class product concern, not a post-hoc debugging aid.

The observability layer must support two kinds of understanding:
1. operational understanding of individual requests
2. strategic understanding of aggregate traffic patterns

This means observability in this project is not limited to “did the request succeed?”
It must explain:
- what happened
- why that route was chosen
- how long it took
- what it likely cost
- how business context influenced execution
- how repeated runs aggregate into strategic insight

## Canonical observability layer
The first artifact uses OpenTelemetry as the canonical observability foundation.

This means:
- traces are the primary instrumentation signal
- telemetry concerns must be centralized
- request lifecycle instrumentation must be explicit
- the codebase must remain vendor-neutral at the observability foundation level

Langfuse is not a required runtime dependency in the first artifact.

However, the architecture must remain Langfuse-ready, meaning:
- telemetry concerns are not scattered across the app
- request lifecycle boundaries are explicit
- metadata naming is stable and deliberate
- later Langfuse integration can be added without transversal refactoring

## OpenTelemetry plus Python stance
The project must adopt the mature OpenTelemetry plus Python posture already resolved in the previous `llm-otel` work.

That means:
- SDK configuration belongs to one bootstrap module
- application code uses the OTel API and project telemetry helpers
- module-level tracer acquisition is acceptable and expected
- semantic attribute names are centralized in one project module
- provider calls are traced as outbound client operations
- traces are for operational visibility, while normalized run records support product reporting
- test runs must be able to disable the SDK cleanly when needed

The project must not re-litigate these architectural decisions in ad hoc ways.

## What observability must answer
The observability system must make it possible to answer questions such as:
- which route did this request take
- which model was selected
- why was that route selected
- how long did the request take
- what did the request likely cost
- which business context fields influenced the decision
- which workflow types are concentrated in premium routes
- which business units are driving more spend
- where routing policy appears to save cost relative to a premium baseline
- where errors or latency cluster

If the system only shows low-level telemetry without enabling these questions, the observability layer is insufficient.

## Observability signal hierarchy
The first artifact should prioritize signals in this order.

### 1. Traces
Traces are the primary signal.
Every inference request must create a request-level trace or span path.

### 2. Response metadata
The API response must carry enough metadata for an immediate understanding of the routing outcome.

### 3. Run store records
A normalized record of recent runs must support lightweight aggregation and reporting.

### 4. Strategic summaries
Reports derived from recent runs must help explain aggregate behavior.

### 5. Logs
Logs are allowed only as a supporting mechanism.
Logs must not become the primary product signal.

## Required observability dimensions
The first artifact must make the following dimensions visible.

### Request identity
- request_id

### Business context
- business_unit
- workflow_type
- risk_tier
- customer_tier
- cost_center
- regulatory_sensitivity
- requested_sla

Not all dimensions must always be populated, but the system must support them cleanly.

### Policy and routing
- execution_mode
- selected_route
- selected_model
- decision_reason
- policy_version or equivalent rule identifier

### Runtime outcome
- status
- error_type where applicable
- latency_ms

### FinOps and usage
- estimated_input_tokens
- estimated_output_tokens
- estimated_cost_usd

### Aggregate interpretation
- request volume by workflow or business unit
- cost concentration by workflow or business unit
- route distribution
- estimated savings relative to a premium-only baseline

## Trace design philosophy
The request lifecycle must be traceable as a coherent unit.

At minimum, the system should instrument the inference lifecycle around:
- request accepted
- routing decision produced
- provider execution completed or failed
- cost metadata attached
- run captured for reporting

The first artifact does not need a deeply nested span tree.
A small and disciplined tracing structure is preferable to instrumentation noise.

## Span model requirements
The tracing model must explicitly include:
- a request-lifecycle span or main execution span for the gateway request
- an outbound provider call span represented as a client operation span

This distinction matters because the gateway is both:
- a local decision engine
- a client of an external model provider

Provider execution must not be flattened into undifferentiated request timing.

## Status and exception handling requirements
Tracing must follow disciplined success and failure semantics.

### Success path
Success instrumentation should be clear but not noisy.
The implementation should avoid careless over-assertion of success status when the default semantic state is already sufficient.

### Failure path
Failure instrumentation must:
- record the exception
- attach an explicit error type when available
- set explicit error status on the relevant span
- preserve enough context for operational diagnosis

This is mandatory.

## Telemetry centralization rule
All telemetry concerns must be centralized as much as practical.

The codebase should avoid:
- hardcoded telemetry attribute names scattered across the app
- ad hoc tracing in multiple route handlers
- duplicated span setup patterns
- direct semantic-convention strings inside business modules

Telemetry keys, semantic-convention mapping, and tracing helpers should live in dedicated modules under `telemetry/`.

This is required both for maintainability and for future Langfuse integration.

## Stable attribute philosophy
Telemetry attributes must be intentionally chosen and stable.

This project is not trying to mirror every possible OpenTelemetry semantic-convention surface.
It is trying to create a stable and readable product-specific observability model.

Attribute naming should therefore prioritize:
- clarity
- business meaning
- stability
- easy reuse in reporting

If external semantic-convention mapping evolves later, that should happen in a contained way.

## Semantic convention strategy
The project must support two semantic layers:

### 1. Project-specific gateway semantics
These capture the actual product value, such as:
- route decision
- policy version
- decision reason
- business context
- estimated savings
- gateway-specific cost metadata

### 2. External or standard semantics where useful
These include GenAI or provider-related concepts such as:
- selected model
- provider identity
- operation category

The project-specific semantic layer must not be sacrificed in an attempt to overfit everything to standard naming.

The two layers must coexist.

## Required trace attributes
The first artifact should expose, at minimum, request-level attributes equivalent to the following dimensions.

### Request identity and context
- `request.id`
- `business.unit`
- `workflow.type`
- `risk.tier`
- `customer.tier`
- `cost.center`
- `regulatory.sensitivity`
- `requested.sla`

### Routing and policy
- `gateway.execution_mode`
- `gateway.selected_route`
- `llm.model`
- `gateway.decision_reason`
- `gateway.policy_version`

### Runtime result
- `gateway.status`
- `gateway.error_type`
- `gateway.latency_ms`

### FinOps
- `gateway.estimated_input_tokens`
- `gateway.estimated_output_tokens`
- `gateway.estimated_cost_usd`

The exact names may evolve in implementation, but the dimensional model must remain intact.

## Response metadata philosophy
The API response must expose enough metadata to make the gateway’s behavior legible in real time.

The response should not return only model output.
It should return a decision-aware metadata block including:
- selected route
- selected model
- decision reason
- latency
- estimated token usage
- estimated cost
- status

This is important because the product promise is not just inference, but inference with explainable economic and operational context.

## Run record philosophy
Each completed request should produce a normalized run record suitable for:
- immediate UI display
- local strategic reporting
- future persistence if desired

The run record should preserve:
- input business context
- routing decision
- provider result metadata
- timing
- estimated cost
- final status

The run record is not just a debug artifact.
It is the raw material for the reporting layer.

## Strategic reporting philosophy
Strategic reporting must be built on top of normalized run records, not on direct trace parsing.

Traces explain execution.
Run records support product-facing aggregation.

This separation is important because:
- traces are operational
- reports are interpretive
- demo dashboards should read from stable, product-shaped aggregates
- later changes to tracing details should not break reporting logic

## Minimum strategic summaries
The first artifact should support at least the following aggregate views.

### 1. Cost by business context
Examples:
- cost by business_unit
- cost by workflow_type
- cost by selected_route

### 2. Route distribution
Examples:
- count by selected_route
- count by workflow_type and route
- premium-route concentration by risk tier

### 3. Basic performance visibility
Examples:
- average latency by route
- average latency by workflow_type
- error count by route

### 4. Savings-oriented interpretation
The reporting layer should estimate a simple comparative metric against a premium-only baseline, such as:
- estimated savings versus forced quality route

This is one of the most important FinOps-facing outputs of the first artifact.

## SDK bootstrap philosophy
The OpenTelemetry SDK must be configured in one place.
That bootstrap layer may:
- initialize tracer provider
- initialize meter provider if required
- configure processors
- configure exporters
- respect local development defaults and test-safe behavior

Application logic outside the telemetry bootstrap must not own these responsibilities.

## Local development and test philosophy
Local development must remain easy.
The product should support:
- no-op or disabled SDK behavior where appropriate
- console-friendly exporters for local inspection when useful
- environment-controlled exporter configuration
- reliable test execution without requiring a full observability backend

The observability architecture is only acceptable if it is pleasant enough to run in normal development conditions.

## Langfuse-ready definition
Langfuse-ready in this project means the following:

1. Request lifecycle boundaries are explicit.
2. Telemetry concerns are centralized.
3. Route decision metadata is preserved in a structured way.
4. Provider execution metadata is normalized.
5. Instrumentation is not entangled with HTTP handler code.
6. Semantic attribute naming is already intentional and centralized.
7. Future integration can enrich traces without re-architecting the system.

It does **not** mean:
- Langfuse must be installed now
- Langfuse-specific SDK code must exist now
- the product must mirror Langfuse’s model today

## What is out of scope in observability for the first artifact
The first artifact explicitly does not need:
- full distributed tracing across many services
- production-grade metrics backend
- alerting pipelines
- log aggregation platform integration
- full semantic-convention coverage
- perfect token accounting
- vendor-specific observability lock-in
- complex span hierarchies
- full retention strategy

The goal is not observability completeness.
The goal is observability credibility.

## Testability requirements
Observability is part of the product contract and must be testable.

The codebase should include tests that verify, at minimum:
- trace-related metadata is produced on the inference path
- decision metadata survives into response and run records
- cost metadata is attached deterministically
- key run-record fields are present for reporting
- strategic reporting aggregates remain structurally stable
- no critical semantic attribute names are being scattered outside the telemetry layer

The tests do not need to validate an external telemetry backend.
They need to validate that the product emits and preserves the right observability information.

## Failure modes to avoid
Observability fails strategically if:
- traces exist but decision rationale is missing
- cost is shown but not connected to business context
- reporting is disconnected from request-level events
- telemetry is scattered across the codebase
- the product only exposes debugging data and not decision-support data
- the system cannot compare policy outcomes economically
- observability becomes vendor-specific too early
- Python plus OpenTelemetry integration looks improvised or inconsistent

## Definition of success
The observability architecture is successful if:
- a single request can be inspected and understood operationally
- a set of recent requests can be aggregated and interpreted strategically
- cost, latency, route choice, and decision reason are all visible
- business context and FinOps logic appear in the observability model
- OpenTelemetry plus Python integration looks deliberate and mature
- the first artifact already looks like a serious control point for LLM traffic
