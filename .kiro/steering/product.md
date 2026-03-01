# Product Steering

## Product name
llm-finops-gateway

## One-line definition
An observability-first, policy-driven choke point for production LLM traffic in cost-sensitive and regulated enterprise environments.

## Product thesis
As organizations embed LLM calls into internal workflows, copilots, support operations, and business-unit-specific automation, the primary challenge quickly stops being “can the model respond?” and becomes “can inference be governed?”

Without a control layer, LLM traffic becomes economically opaque, operationally fragile, and strategically difficult to manage. Teams lose visibility into where cost is coming from, which workflows should use higher-quality models, where latency accumulates, which business units are driving spend, and how to explain routing decisions to technical and non-technical stakeholders.

`llm-finops-gateway` exists to solve that problem by introducing a small but high-leverage control point: every LLM request passes through a gateway that inspects business context, applies routing policy, executes inference, emits observability signals, and produces data suitable for both operational debugging and strategic FinOps reporting.

This product is not a chatbot, not a generic OpenAI wrapper, and not a dashboard-first analytics app. It is an infrastructure-oriented decision layer for LLM traffic.

## Target positioning
The product is technically reusable across industries, but it is intentionally positioned toward:
- fintech
- financial infrastructure
- regulated enterprise back-office workflows
- enterprise B2B SaaS with AI in production
- internal copilots and workflow assistants where cost, latency, and risk matter

The product should feel clearly relevant to environments where:
- cost attribution matters
- routing decisions should be explainable
- different workflows have different quality/cost requirements
- business context should influence inference policy
- observability is required for governance, not only debugging

## Core user story
A company has multiple teams or workflows calling LLM APIs directly. Spend is rising, quality expectations vary by workflow, and leadership cannot clearly answer:
- which business units are driving spend
- which workflow types should use cheap vs premium models
- where latency and failures are happening
- whether routing decisions are economically rational
- what savings are possible under policy-driven routing

The company inserts `llm-finops-gateway` as a choke point in front of LLM traffic.

The gateway receives the request, builds request context, evaluates a routing policy, selects an execution route, calls the model provider, emits tracing and cost metadata, stores run data for local reporting, and exposes strategic summaries.

The product makes LLM traffic observable, explainable, and governable.

## Product promise
The gateway should make the following statement true:

“We can explain, with evidence, why this LLM request took this route, how much it cost, how long it took, which business context influenced the decision, and what that means at both the operational and management level.”

## What makes this product valuable
The product is valuable because it compresses several hard production questions into one inspectable control point:
- routing policy
- cost attribution
- request observability
- latency awareness
- failure visibility
- workflow-level reporting
- strategic decision support for LLM operations

The value is not in adding “more AI,” but in making existing AI traffic legible and governable.

## Product scope for the first artifact
The first artifact must remain intentionally small.

It must implement:
- a minimal inference gateway API
- a policy-driven router with automatic route selection
- explicit business-context-aware request modeling
- OpenTelemetry-based tracing
- estimated cost attribution
- lightweight local reporting
- a minimal visual demo console
- Dockerized execution
- architecture that is Langfuse-ready, but without Langfuse as a required runtime dependency

It must not implement:
- a full platform
- multi-user auth
- tenant billing systems
- persistence-heavy production storage
- complex admin UI
- model hosting
- local small language models for routing
- workflow orchestration across many providers
- enterprise-grade RBAC
- advanced analytics infrastructure
- generalized compliance engine

## Product bias: fintech and enterprise operations
The product must feel naturally aligned with fintech and enterprise AI operations.

This alignment should appear in:
- request fields
- workflow examples
- routing policy inputs
- strategic report categories
- demo language
- README framing

The product must not rely on superficial “fintech” branding alone.
Its affinity with fintech should emerge from the kinds of business problems it models.

Examples of workflow types that should feel natural in the product:
- transaction_explanation
- charge_dispute_summary
- fraud_triage
- policy_lookup
- compliance_assistant
- internal_ops_assistant
- customer_support_summary
- risk_review_note

Examples of business context fields that reinforce this positioning:
- business_unit
- workflow_type
- risk_tier
- customer_tier
- cost_center
- regulatory_sensitivity
- requested_sla

## Product principle: the router decides
The gateway is not primarily a manual model picker.

The default product behavior must be:
- request arrives with business context
- router decides automatically
- response explains what was chosen and why

Manual override modes may exist only for:
- controlled comparison
- benchmarking
- demonstration of trade-offs
- policy validation

The router must remain the primary decision-maker.

## Product principle: FinOps decision-making, not passive telemetry
This product must not stop at “request telemetry.”

It must support decision-making by making it possible to answer questions such as:
- which workflows are overusing premium inference
- where policy-driven routing reduces cost
- which business units concentrate spend
- which request classes justify quality-first routing
- where latency and quality trade-offs should be reconsidered

The reporting layer should therefore be framed as strategic FinOps reporting, not merely technical logs.

## Product principle: observability-first, Langfuse-ready
OpenTelemetry is the canonical observability layer for the first artifact.

The codebase must be structured so that:
- telemetry concerns are centralized
- telemetry attributes are stable and explicit
- provider execution and instrumentation boundaries are clean
- Langfuse can be added later without transversal refactoring

The first version must not depend on Langfuse to be complete.

## Product principle: demo supports the thesis
The demo exists to make the gateway’s value immediately legible.

The visual demo must show:
- request input
- business context
- automatic routing decision
- selected model/route
- cost estimate
- latency
- response status
- strategic/aggregated view of gateway activity

The demo must not become a frontend-heavy product in its own right.

## Non-goals for the first artifact
The first artifact is not trying to prove:
- full enterprise platform maturity
- advanced frontend engineering
- local model serving
- multi-provider orchestration depth
- production-hard persistence
- complete cost-accounting accuracy

The first artifact is trying to prove:
- good product judgment
- policy-driven LLM routing
- observability discipline
- explainable FinOps-aware decision-making
- strategic reporting mindset
- clear alignment with enterprise AI in production

## Success criteria
The first artifact is successful if a technically literate reviewer can quickly understand that:
1. this is a choke point for production LLM traffic
2. it applies policy rather than acting as a thin pass-through
3. it makes cost, latency, and decision rationale visible
4. it feels relevant to fintech / regulated enterprise workflows
5. it demonstrates infrastructure thinking, not toy-app thinking

## Failure modes to avoid
This product fails strategically if it turns into:
- a generic LLM playground
- a thin wrapper around a model API
- a telemetry-only demo with no decision value
- a dashboard with no meaningful policy engine
- a frontend-heavy artifact with weak infrastructure substance
- a technically correct but commercially ambiguous project

## Tone and design intent
The product should feel:
- precise
- restrained
- infrastructure-minded
- explainable
- enterprise-relevant
- strategically useful

It should not feel:
- playful
- consumer-oriented
- dashboard-gimmicky
- overdesigned
- framework-heavy
- architecture-theatre