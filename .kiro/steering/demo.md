# Demo Steering

## Demo intent
The demo exists to make the value of `llm-finops-gateway` immediately legible.

It is not the product itself.
It is not a separate frontend application.
It is not a design exercise.
It is not a BI platform.

The demo is a controlled visual surface that reveals the gateway’s core behavior:
- business-context-aware request intake
- automatic policy-driven routing
- provider execution result
- cost and latency metadata
- strategic interpretation of recent runs

The purpose of the demo is to compress understanding time for reviewers.

A reviewer should be able to interact with the demo and quickly understand:
- this is a choke point for production LLM traffic
- the router decides automatically
- business context influences routing
- the gateway emits useful operational and FinOps data
- the product is relevant to enterprise/fintech-like AI usage

## Demo philosophy
The demo must support the thesis of the product, not compete with it.

Therefore, the demo must be:
- thin
- direct
- operational in tone
- visually clear
- fast to understand
- small in implementation surface

The demo must not become:
- a general-purpose LLM playground
- a frontend-heavy subproject
- a productized dashboard platform
- a justification for backend sprawl
- a visual distraction from the gateway’s real logic

## Demo architecture rule
The demo is subordinate to the gateway.

This means:
- the API remains the primary product
- the demo consumes stable API outputs
- the demo must not dictate deep backend design
- the demo should live as simple static assets or minimal template-driven UI
- no heavy frontend framework is required or preferred

For this first artifact, plain HTML/CSS/JS is the preferred default.

## Demo audience
The demo is primarily for:
- technical recruiters
- hiring managers
- staff/principal engineers
- engineering leaders
- technical buyers in fintech / enterprise SaaS contexts

The demo should therefore optimize for:
- fast comprehension
- visible decision rationale
- visible cost and latency trade-offs
- professional tone
- operational clarity

It should not optimize for:
- playful exploration
- broad customization
- consumer-style UI patterns
- visual maximalism

## Demo surface
The demo should be small and focused.

The preferred initial structure is:
- one main inference/decision surface
- one lightweight strategic summary surface

These may be:
- two tabs
- two panels
- two sections of the same page

No more than that is needed in the first artifact.

## Primary demo surface: decision console
The primary demo surface should show the request-to-decision path.

It must allow a reviewer to:
- enter a request
- attach business context
- submit it to the gateway
- observe the automatic route decision
- see the result and key metadata

This surface must make it obvious that:
- the gateway is making the decision
- the decision is policy-driven
- the product is not a manual model picker

## Automatic routing rule
The default demo path must use automatic routing.

The user should not primarily choose the model manually.

Instead, the request should carry business context and the router should decide.

If manual controls exist, they should exist only as controlled execution modes such as:
- `auto`
- `force_cheap`
- `force_quality`

These modes exist only to:
- compare policy outcomes
- demonstrate trade-offs
- validate the routing logic

The default and emphasized path must be `auto`.

## Business context in the demo
The demo must visibly encode enterprise and fintech-like relevance through the fields it exposes.

Examples of useful request fields:
- workflow_type
- business_unit
- risk_tier
- customer_tier
- cost_center
- regulatory_sensitivity
- requested_sla
- execution_mode

The demo should not look like a generic text box plus “choose a model.”

## Suggested workflow examples
The demo should feel aligned with cost-sensitive, regulated, or enterprise internal workflows.

Examples of request scenarios that should feel natural:
- transaction_explanation
- charge_dispute_summary
- fraud_triage
- policy_lookup
- compliance_assistant
- internal_ops_assistant
- customer_support_summary
- risk_review_note

These examples make the product’s enterprise/fintech alignment visible without requiring domain-specific overengineering.

## Required response visibility
After submitting a request, the demo must visibly show:
- selected route
- selected model
- decision reason
- status
- latency
- estimated input/output tokens
- estimated cost
- output text

The response area should emphasize interpretation, not just raw output text.

## Strategic summary surface
The second demo surface should expose a lightweight strategic summary of recent runs.

Its purpose is to show that the gateway is not only observable per request, but also useful for aggregate interpretation.

This surface should help answer questions such as:
- where cost is concentrated
- which workflows are using premium routes
- how the policy distributes traffic
- what simple savings appear versus a premium-only baseline
- which business units dominate recent usage

This surface must remain intentionally small.

## Minimum strategic summary views
The demo should ideally include a small number of summary blocks such as:
- total requests
- average latency
- total estimated cost
- route distribution
- cost by business unit
- cost by workflow type
- estimated savings versus forced quality baseline

Not all of these need to be complex visualizations.
Simple summary cards, compact tables, or basic charts are acceptable.

## Visual style
The demo should feel:
- restrained
- technical
- infrastructure-oriented
- clear
- enterprise-relevant

It should not feel:
- playful
- consumer-app-like
- overly animated
- template-generic
- dashboard-gimmicky

The visual goal is trust, not spectacle.

## Frontend technology rule
The first artifact should prefer:
- plain HTML
- plain CSS
- plain JavaScript
- optional minimal server-side templating if genuinely useful

The first artifact should avoid:
- React
- Next.js
- Vue
- heavy component frameworks
- frontend build complexity
- state-management libraries

This is an explicit scope-control decision, not a technical limitation.

## Demo/backend relationship
The demo should consume backend outputs rather than recreate backend logic.

That means:
- decision reasoning comes from the backend
- selected route/model comes from the backend
- cost and latency metadata come from the backend
- aggregate summaries come from reporting endpoints or equivalent backend-produced structures

The frontend must not reimplement routing, pricing, or report calculation logic.

## Demo API expectations
The backend should expose only the minimum API support the demo needs.

This may include:
- inference request endpoint
- health endpoint
- lightweight report/summary endpoint
- optional recent-runs endpoint if useful

No more demo support should be added than necessary.

## Demo data philosophy
The demo should remain believable and truthful.

It may use:
- recent in-memory runs
- seeded example requests
- deterministic stub data where explicitly appropriate

It must not:
- fake strategic insight disconnected from gateway execution
- imply production-scale analytics where none exists
- hide the limits of local/in-memory reporting

The demo should be honest about what it is.

## Demo narrative
The demo should tell a concise story.

A reviewer should be able to follow this arc:
1. here is a business-context-rich request
2. the gateway decides automatically
3. the chosen route/model is explained
4. the request returns with latency and cost metadata
5. recent activity aggregates into strategic summaries

That sequence is the demo.
Anything beyond it is optional.

## Demo non-goals
The first artifact demo is not trying to prove:
- enterprise-grade frontend engineering
- full admin tooling
- complex filtering and drilldowns
- historical analytics
- auth and multi-user workflow
- polished design system maturity
- advanced charting sophistication

Its job is to make the gateway believable and legible.

## Demo anti-patterns
The following are demo failures:
- a UI that looks like a generic chatbot
- a model picker that hides the router’s role
- a dashboard with no visible decision logic
- frontend complexity that exceeds backend maturity
- visually impressive output that weakens the product thesis
- business context hidden or absent
- no visible explanation for automatic routing
- strategic summaries that do not connect to gateway runs

## Demo success criteria
The demo is successful if a reviewer can quickly infer that:
- this is a policy-aware LLM gateway
- it is making routing decisions automatically
- those decisions are observable and explainable
- cost and latency are first-class outputs
- the product is relevant to enterprise and fintech-like workflows
- the product is small but strategically serious

## Demo implementation restraint
The demo must remain lightweight enough that:
- it does not consume disproportionate implementation budget
- it does not pull the project into frontend-first priorities
- it remains understandable to a backend/infra-minded reviewer
- it can be completed without threatening the rest of the artifact

A minimal, elegant, static console is preferable to a more ambitious but fragile frontend.
