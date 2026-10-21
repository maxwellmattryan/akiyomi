# 0002. Make the MVP "Assess + Scout" in one prefecture

Status: Proposed · Date: 2026-10-21

## Context

The original brief's v1 was "Assess one prefecture": listings, hazard checks and a paid property report. The product now also needs to demonstrate an agentic workflow with memory and a user model, multi-step planning, messy external APIs (flights, hotels, maps), and acting on the user's behalf with guardrails, on top of real geospatial work. There are roughly ten weeks to a public launch.

## Options

1. **Assess only (original v1).** Strong data foundation and a payment test. Uses geospatial work, but the agent is a batch pipeline: no planning toward a user goal, no travel APIs, no acting on anyone's behalf.
2. **Assess + Scout (scouting trip planner with approved inquiries).** Keeps the data foundation and adds the Visit stage: an agent that plans a viewing trip and sends approved inquiries. Exercises all four capabilities. Defers payments and the full report to 1.0.
3. **Scout only, on top of other sites' listings.** Fastest to the agent, but no assessment moat, and depends on sources we can't control.

## Decision

Option 2, as defined in [MVP](../../product/mvp.md). Payments and the full report are the first work in [1.0](../../product/v1.md).

## Consequences

- We measure willingness to pay with a landing page price test, not real payments, before 1.0.
- Email sending to real town halls needs the guardrails in [ADR 0003](0003-agent-actions-via-proposals.md) before launch.
- The MVP has a [cut list](../../product/mvp.md#cut-list-if-we-fall-behind) so the demo survives slippage.
- Revisit if proving revenue before the launch date becomes more important than demonstrating the agent: swap the paid single report in and keep inquiries as drafts only.
