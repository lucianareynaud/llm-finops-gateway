# Quality Steering

## Quality intent
The purpose of quality in `llm-finops-gateway` is not generic engineering hygiene alone.

Quality exists here to protect:
- the product thesis
- the architecture
- the interpretability of decisions
- the credibility of observability
- the stability of public contracts
- the efficient use of agent credits

This repository must not optimize for surface complexity.
It must optimize for small, sharp, trustworthy execution.

## Quality philosophy
A feature is not considered high quality merely because it works once.

A feature is only high quality if it is:
- aligned with product intent
- implemented in the correct architectural layer
- testable
- understandable
- bounded in scope
- safe to build upon

The first artifact is intentionally small.
Its quality bar must therefore be high relative to its size.

## Quality priorities
Quality should be evaluated in the following order:

1. Product coherence
2. Architectural integrity
3. Public contract stability
4. Observability credibility
5. Deterministic decision logic
6. Testability
7. Developer ergonomics
8. Presentation polish

Presentation polish must not outrank system truth.

## Definition of done
No spec, feature, or work unit is complete unless all of the following are true:
- the relevant tasks are complete
- acceptance criteria are satisfied
- the implementation matches the steering documents
- tests appropriate to the work pass
- no unrelated architectural drift has been introduced
- the result remains understandable to a human reviewer
- the product thesis is strengthened rather than diluted

Code existing in the repository is not sufficient for completion.

## Product-quality rules
The repository must continue to communicate the same product story after every change.

That means a change is low quality if it causes the product to feel like:
- a generic model wrapper
- a generic prompt playground
- a dashboard demo without decision logic
- a frontend-heavy artifact with weak infrastructure substance
- a telemetry showcase with no FinOps meaning

Every feature must strengthen the identity of the product as:
- a policy-driven LLM gateway
- a choke point for production LLM traffic
- an observability-first system
- a FinOps-aware decision layer
- a gateway relevant to enterprise and fintech-like workflows

## Architectural quality rules
The following are required architectural quality properties:

### Thin routes
Route handlers must remain thin.
They may validate input, call services, and shape responses.
They may not absorb business logic over time.

### Isolated router
Routing policy must live in the router layer.
It must remain explicit, deterministic, and testable.

### Isolated provider boundary
Provider-specific logic must remain behind a stable boundary.

### Isolated pricing logic
Pricing and cost-estimation logic must remain in dedicated modules.

### Centralized telemetry
Telemetry setup, attribute definitions, and tracing helpers must remain centralized.

### Reporting separate from tracing
Strategic reporting must consume normalized run records or equivalent product-shaped data, not parse tracing output directly.

### Thin demo
The demo must remain a thin presentation layer over the gateway.

Any implementation that weakens these architectural properties fails quality review.

## OpenTelemetry + Python quality rules
Because this product depends heavily on observability credibility, the OpenTelemetry + Python architecture must be treated as a quality-critical foundation.

The following rules are mandatory:

### API/SDK separation
The OpenTelemetry SDK bootstrap/configuration must live in a dedicated setup module.
Application code should depend on OTel API usage and project telemetry helpers, not on scattered SDK setup logic.

### Centralized attribute definitions
Telemetry attribute names must not be hardcoded across routes and services.
They must be centralized in telemetry-specific modules.

### Clean lifecycle boundaries
Instrumentation must occur at explicit execution boundaries:
- request lifecycle
- route decision
- provider execution
- cost attachment
- run capture

### Provider execution as client work
Outbound provider execution should be represented as client-side operational work in the tracing model.

### Controlled success/error semantics
Error paths must be explicitly instrumented.
Success paths must remain semantically disciplined and not overstate status unnecessarily.

### Vendor-neutral foundation
The first artifact must remain vendor-neutral at the observability foundation layer.
Langfuse readiness is required.
Langfuse runtime dependence is not.

If a change weakens these properties, it fails quality review even if tests still pass.

## Public contract quality rules
The following are frozen public contracts once introduced:
- inference request schema
- inference response schema
- report schema
- route decision structure
- demo-facing API shape
- key metadata field model

These contracts may only change deliberately and visibly.

A high-quality change does not casually alter public shapes.

If a public contract changes, the following must also change in the same unit of work:
- relevant spec docs
- tests
- example payloads
- demo assumptions where applicable

## Decision-quality rules
The route decision is one of the core outputs of the product.

Therefore, route decision quality requires:
- deterministic behavior
- explicit reasoning
- stable structure
- inspectable inputs
- test coverage

A route decision must never become:
- hidden
- implicit
- opaque
- difficult to explain
- encoded in a way that the response and reports cannot expose

The system must be able to say not only what route was chosen, but why.

## FinOps-quality rules
Because this gateway claims FinOps value, the cost-related logic must be honest and structurally useful.

This means:
- any approximation must be explicitly labeled as an estimate
- pricing logic must be centralized
- route-aware cost metadata must be preserved
- aggregate outputs must support interpretation, not just raw totals
- savings-style comparisons must be simple but defensible

The product must never imply billing-grade precision if it only provides deterministic estimates.

Honesty about estimation is part of quality.

## Reporting-quality rules
Strategic reporting exists to support decision-making, not ornamentation.

A reporting feature is high quality only if it helps answer product-relevant questions such as:
- where cost is concentrated
- how routes are being distributed
- which workflow types dominate spend
- what savings appear relative to a premium-only baseline
- where latency and failure concentrate

A report that is visually attractive but strategically empty is low quality.

## Demo-quality rules
The demo should make the product easier to understand within minutes.

Demo quality means:
- the gateway remains the center of attention
- the interface is clear and light
- the automatic decision path is visible
- the route decision and cost/latency metadata are legible
- the reporting view supports the product thesis
- the demo does not create unrealistic expectations about product scope

The demo must not:
- overshadow the backend
- justify unnecessary dependencies
- introduce complicated state machinery
- become a second project

For this repository, a static or near-static HTML/CSS/JS console is entirely acceptable and may be preferable.

## Testing philosophy
Tests are required because they preserve product truth under constrained, agent-assisted development.

Testing in this repository must focus on:
- contract stability
- routing behavior
- cost estimation behavior
- report structure
- request-to-response integrity
- observability-related metadata preservation

Tests do not need to maximize quantity.
They need to maximize protection of meaning.

## Required test categories
The repository should maintain tests in the following categories:

### Contract tests
Used to freeze:
- request schema shape
- response schema shape
- report schema shape
- demo-support API shape

### Unit tests
Used for:
- routing logic
- pricing logic
- provider normalization
- report building
- request context building

### Integration tests
Used for:
- end-to-end inference request path
- response metadata propagation
- tracing path sanity
- report endpoint behavior

The first artifact does not require heavyweight system testing beyond what is necessary to preserve trust.

## Validation requirements
A work unit should include whatever validation is relevant to its scope.
Across the repository, the quality bar should eventually include:
- unit tests
- contract tests
- integration tests
- lint
- typing
- local manual run
- Docker sanity check once Docker exists
- demo sanity check once demo surfaces exist

Validation is not optional ceremony.
It is the mechanism by which small increments remain safe.

## Linting and style rules
The codebase should prioritize readability over cleverness.

Quality style principles:
- explicit names over compressed names
- small functions over overloaded handlers
- predictable module boundaries
- no unnecessary abstraction
- limited magic
- no speculative extensibility theatre

Linting should help enforce clarity, not style gamesmanship.

## Typing rules
Type hints are encouraged where they materially improve clarity and safety.
They are especially valuable for:
- context objects
- route decision structures
- provider result structures
- report shapes
- telemetry-facing record objects

Typing should not become performative complexity.
Use typing to clarify key contracts, not to impress static-analysis purists.

## Dependency quality rules
A dependency must earn its place.

A dependency is acceptable only if it clearly improves one of the following:
- runtime clarity
- observability correctness
- API correctness
- testing ergonomics
- packaging
- demo legibility

Dependencies that mainly increase surface area, maintenance cost, or cognitive overhead should be rejected.

Framework-heavy frontend dependencies are explicitly disfavored for this artifact.

## Documentation quality rules
Documentation is part of product quality, not aftercare.

At minimum, the repository must keep the following truthful and current:
- steering docs
- active spec docs
- README
- example payloads
- demo assumptions
- environment variable examples

A repository whose docs no longer describe reality is low quality even if the code runs.

## Change-review checklist
Every meaningful change should be reviewable against the following questions:
1. Does this strengthen the product thesis?
2. Is the change in the correct architectural layer?
3. Did any frozen public contract move?
4. Is the route decision still explicit and explainable?
5. Is observability still centralized and credible?
6. Is pricing logic still isolated and honest?
7. Does reporting remain strategically meaningful?
8. Did the demo remain subordinate?
9. Did the implementation stay small and understandable?
10. Did this change introduce avoidable scope?

If the answer to several of these is unclear, quality is insufficient.

## Quality anti-patterns
The following are quality failures in this repository:
- feature sprawl
- speculative abstractions
- route logic hidden in handlers
- telemetry logic scattered across modules
- visually polished demo with weak core logic
- pseudo-precision in cost reporting
- public contract drift without spec updates
- docs drifting behind code
- adding dependencies to save minutes now while adding maintenance for months later
- code that technically works but weakens the product identity

## First-artifact quality ceiling
The first artifact should be:
- small
- coherent
- explainable
- observable
- strategically relevant
- portfolio-strong

It does not need to be:
- platform-complete
- enterprise-hardened in every dimension
- persistence-rich
- analytics-heavy
- UI-heavy
- perfect

Quality here means disciplined completeness, not maximal breadth.

## Quality success condition
Quality is successful in this repository if:
- each increment preserves trust
- the architecture remains sharp
- the gateway’s decision-making remains legible
- observability remains a product feature
- FinOps meaning remains visible
- the demo makes the value obvious without inflating scope
- the finished artifact feels serious, small, and strategically well-judged
