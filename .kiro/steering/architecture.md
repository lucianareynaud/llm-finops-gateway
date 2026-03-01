# Architecture Steering

## Architectural intent
`llm-finops-gateway` is a small, infrastructure-oriented system that sits in front of production LLM calls and turns them into governed, observable events.

Its architecture must reflect that purpose.

The codebase should be organized around a clean execution path:
1. request enters the API
2. request context is built from payload plus defaults
3. routing policy evaluates the context
4. a route decision is produced
5. a provider client executes inference
6. telemetry captures the run
7. a normalized run record is stored for reporting and demo display
8. lightweight strategic summaries are built from those run records

The system must be easy to understand end-to-end by one engineer reading the code.

## Core architectural principle
This product is a choke point, not an application ecosystem.

Therefore:
- the gateway is the center of the system
- the demo exists to expose the gateway
- reporting exists to interpret gateway activity
- telemetry exists to explain gateway behavior
- provider logic exists to execute selected routes
- routing logic exists to apply policy
- no surrounding layer may overshadow the gateway itself

## Architectural stance on OpenTelemetry in Python
The project must inherit the solid OpenTelemetry plus Python decisions already validated in the previous `llm-otel` work.

The architecture must therefore explicitly separate:
- OpenTelemetry API usage in application code
- OpenTelemetry SDK bootstrap and exporter configuration
- project-specific semantic attributes and helper functions
- normalized run records used for product reporting

This separation is non-negotiable.

Application code must not carry SDK wiring details.
Application code must not hardcode semantic-convention strings.
Application reporting must not parse traces directly.

## Primary runtime components
The first artifact consists of the following runtime components.

### 1. API layer
Responsible for:
- HTTP entrypoints
- request validation
- response shaping
- invoking the application services in the correct order

The API layer must stay thin.
Route handlers must not contain business policy, pricing math, provider-specific logic, or telemetry sprawl.

### 2. Request context builder
Responsible for:
- normalizing incoming request fields
- filling defaults where appropriate
- shaping a coherent internal context object for routing and reporting

This context object is critical because the gateway is not routing on raw prompts alone.
It is routing on business context.

### 3. FinOps policy router
Responsible for:
- evaluating the request context
- selecting a route automatically
- producing an explicit decision object
- exposing a clear decision reason
- making routing auditable and testable

This router must be deterministic in the first artifact.
It must not use a local LLM or a second model to route requests.

### 4. Provider client
Responsible for:
- executing the selected inference path
- isolating provider-specific logic
- returning a normalized provider result to the rest of the system

The provider client must be small and replaceable.
The gateway must not depend on provider-specific logic in the API layer.

### 5. Pricing and cost estimation
Responsible for:
- mapping selected route or model to price assumptions
- estimating input or output token usage when exact counts are unavailable
- producing cost metadata
- exposing data suitable for both per-request response metadata and aggregate reporting

Pricing logic must be centralized and isolated.

### 6. Telemetry layer
Responsible for:
- creating traces or spans around request execution
- attaching stable attributes
- recording route decisions, latency, status, and cost metadata
- remaining cleanly isolated from route handlers

The telemetry layer must be OpenTelemetry-first and Langfuse-ready.

### 7. Run store
Responsible for:
- holding recent run data needed for local reporting and demo display
- exposing a small interface for report building

For the first artifact, this may be an in-memory store.
Persistence-heavy architecture is explicitly out of scope.

### 8. Strategic reporting layer
Responsible for:
- aggregating run data into operational and strategic summaries
- producing data that is meaningful for FinOps-oriented interpretation
- supporting the demo console dashboard view

This layer must not become a full analytics platform.
It should remain lightweight and opinionated.

### 9. Demo console
Responsible for:
- making the gateway’s behavior immediately visible
- exposing a minimal request form and a minimal observability or reporting view
- helping a reviewer understand the gateway quickly

The demo must be thin and subordinate to the API.

## Code layout and responsibility boundaries

### `app/main.py`
Application bootstrap only.
Responsible for creating the FastAPI app, mounting routes, and attaching demo static assets.

Must not contain business logic.

### `app/config.py`
Central settings and environment-based configuration.

Must not become a dumping ground for business rules.

### `app/schemas/`
Public request or response models and any API-facing schema definitions.

These schemas are part of the frozen external contract and must change carefully.

### `app/routes/`
Thin route handlers.
Route handlers may:
- accept validated input
- invoke service-layer logic
- shape response objects

Route handlers must not:
- implement routing policy
- perform provider-specific logic inline
- compute pricing inline
- hardcode telemetry attributes
- initialize OpenTelemetry SDK components

### `app/services/request_context.py`
Builds the internal request context object.

This module is where incoming API fields are translated into routing and reporting context.

### `app/services/finops_router.py`
Implements the deterministic policy router.

This is the heart of the gateway’s intelligence.

### `app/services/provider.py`
Defines and implements the provider execution boundary.

This module must hide provider-specific details from the rest of the app.

### `app/services/pricing.py`
Implements pricing lookup and deterministic token or cost estimation.

### `app/services/report_builder.py`
Builds lightweight strategic summaries from recent runs.

### `app/services/run_store.py`
Stores recent run data for local reporting and demo purposes.

The first version should prefer simplicity over durability.

### `app/domain/`
Contains small internal domain models that clarify meaning:
- routing concepts
- route decision shape
- pricing meaning
- reporting structures

The domain layer exists to keep the service layer explicit, not abstract for its own sake.

### `telemetry/`
Contains all tracing-related concerns.

This layer must explicitly separate:
- SDK bootstrap
- API usage helpers
- attribute constants and semconv mapping
- span-building helpers
- exporter configuration

Telemetry keys, span helpers, and exporter configuration must stay here as much as practical.

### `telemetry/bootstrap.py` or equivalent
A single module must own OpenTelemetry SDK setup.
It should configure the tracer provider, meter provider where needed, processors, and exporters.

The rest of the application must use only OTel API access and project telemetry helpers.

### `telemetry/attributes.py` and `telemetry/semconv.py` or equivalent
All telemetry attribute names must be centralized.
No route, service, or provider module may hardcode semantic-convention attribute names.

This centralization is mandatory because:
- GenAI semantic conventions evolve
- project-specific gateway attributes must coexist with them
- later Langfuse integration must not require transversal edits

### `app/static/` and `app/templates/`
Contain the demo console assets.

The frontend is intentionally simple and should not become a separate frontend application.

### `tests/`
Organized by purpose:
- contract tests for API and public schema shape
- unit tests for routing, pricing, and provider behavior
- integration tests for request-to-response paths and tracing or report outputs

## Internal execution flow
The expected request flow is:

1. Client sends inference request
2. API validates request schema
3. Request context builder creates normalized context
4. FinOps policy router returns a `RouteDecision`
5. Telemetry layer opens the request-level execution span
6. Provider client executes the chosen route or model under a provider call span
7. Pricing layer computes estimated cost
8. Telemetry layer attaches final attributes and marks outcome
9. Run store captures a normalized run record
10. API returns response with inference result plus decision metadata

This flow must remain legible in code.

## OpenTelemetry execution model
The project should follow this model:

- the app bootstrap initializes global providers once
- modules are allowed to obtain tracer handles at module scope through the OTel API
- application logic uses helper functions or wrappers from the project telemetry layer
- provider execution is represented as a `CLIENT` span or equivalent client-side outbound operation span
- success and failure semantics must be handled deliberately rather than casually

This reflects the solid Python plus OpenTelemetry pattern already validated in the previous project.

## Decision objects are first-class
The routing result must not be reduced to a bare string.

The system should model an explicit route decision object that contains at least:
- selected route
- selected model
- decision reason
- policy version or rule identifier
- any relevant override notes

This is essential because the product is about explainable decision-making.

## Business context is first-class
Requests must not be treated as prompt-only events.

The first artifact should already model business context fields such as:
- business_unit
- workflow_type
- risk_tier
- customer_tier
- cost_center
- regulatory_sensitivity
- requested_sla
- execution_mode

Not all of these must drive routing immediately, but the architecture must support them clearly.

## Routing philosophy
The router is policy-based, not model-based.

The first artifact must:
- use explicit rules
- be deterministic
- be inspectable
- be easy to test

It must not:
- invoke an auxiliary LLM to decide routes
- use opaque routing heuristics
- hide policy reasoning from the caller

Manual override modes may exist for comparison, but automatic routing is the primary behavior.

## Observability philosophy
Observability is not an afterthought or a logging side effect.

The architecture must make telemetry a dedicated concern.
This means:
- telemetry keys must be stable
- tracing helpers must be centralized
- instrumentation must happen at clean boundaries
- telemetry must describe the request lifecycle, route decision, provider execution outcome, and cost metadata
- project-specific gateway semantics must coexist with GenAI or provider semantics

The architecture must allow later Langfuse integration without widespread refactoring.
That means the execution lifecycle and the instrumentation lifecycle must remain cleanly separable.

## Reporting philosophy
Reporting is not just “dump recent requests.”

The reporting layer must consume normalized run records rather than parse traces directly.

This separation is important because:
- traces are operational
- run records are product-shaped
- reports are interpretive
- demo dashboards should read from stable aggregates
- later changes to tracing details should not break reporting logic

The reporting layer should produce summaries that help answer strategic questions such as:
- where cost is concentrated
- how routing policy is distributing traffic
- where premium inference is being used
- what estimated savings appear relative to a premium-only baseline
- which workflow types dominate volume or cost

This should still remain lightweight in the first artifact.

## Demo philosophy
The demo must reveal the gateway’s decision-making.
It should not behave like a generic prompt playground.

The primary interaction should show:
- the input business context
- the router’s automatic choice
- the resulting output
- the observed latency or cost or status
- the strategic meaning of recent activity

The demo exists to support comprehension, not to introduce a second product.

## Persistence philosophy
The first artifact should prefer an in-memory run store.
This is sufficient for:
- local reporting
- demo visualizations
- request history within a single runtime session

Persistent storage is deferred to future artifacts.

## Provider philosophy
The first artifact should use an API-based provider path rather than local model hosting.

This keeps the scope aligned with the product thesis:
- routing and governance
- observability
- cost awareness
- explainable execution

Local model serving is explicitly out of scope.

## Docker philosophy
The application should be runnable locally both:
- directly in Python
- via Docker

Docker is part of the packaging story, not the center of the architecture.

The first artifact should keep containerization simple.

## Architectural invariants
The following invariants must be preserved:

1. Route handlers stay thin.
2. Routing policy stays outside route handlers.
3. Provider logic stays outside route handlers.
4. Pricing logic stays centralized.
5. Telemetry setup stays centralized in one bootstrap module.
6. Telemetry attribute names stay centralized in one module.
7. Application code depends on OTel API and project helpers, not on scattered SDK setup.
8. Provider execution is represented as an outbound client operation in tracing.
9. Decision objects remain explicit and inspectable.
10. Business context remains part of routing semantics.
11. Strategic reporting consumes run records, not raw trace parsing.
12. The demo remains thin and subordinate.
13. The first artifact remains understandable by one engineer.
14. The first artifact does not expand into platform sprawl.

## Anti-patterns to avoid
Avoid the following:
- putting routing policy directly in HTTP handlers
- mixing provider logic with telemetry setup logic
- spreading semantic attribute strings across the codebase
- parsing raw traces as the primary reporting source
- using the demo UI as a justification for backend complexity
- introducing premature persistence architecture
- creating unnecessary abstraction layers
- introducing framework-heavy frontend tooling
- hiding decision rationale
- implementing “smart routing” with opaque heuristics
- adding multiple providers before the single-provider path is solid
- treating reports as decorative instead of strategic
- treating OpenTelemetry bootstrap as an incidental side effect rather than a defined subsystem

## Definition of architectural success
The architecture is successful if:
- a reviewer can trace the request lifecycle quickly
- routing decisions are explicit and testable
- business context influences decision-making
- cost and observability are first-class, not bolted on
- Python plus OpenTelemetry integration looks deliberate and mature
- the system feels like a small enterprise infrastructure component
- the demo clarifies the system instead of distracting from it
