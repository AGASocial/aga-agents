# US-06: Insight Generation

**Epic:** Evaluation & Insights
**Priority:** High

## Description
As the system (acting on behalf of the client),
I want to synthesize the gathered signals and company data to generate actionable insights (business hypothesis, pain points, and a consulting angle),
So that the client has a clear, strategic playbook on exactly how to pitch their services to this specific prospect.

## Context / Background
*Signals tell us "what" is happening (e.g., they are hiring). Insights tell us "why it matters" (e.g., they might be struggling with onboarding, creating a perfect opportunity for our client's training services). This story focuses on the AI reasoning required to turn raw data into a strategic entry point before any outreach is drafted.*

## Acceptance Criteria

**Scenario 1: Generating the Business Hypothesis**
*   **Given** a scored prospect with attached signals
*   **When** the Insight Generation engine runs
*   **Then** it must output a brief "Business Hypothesis" explaining the current state of the prospect's business based on available data.

**Scenario 2: Identifying Likely Pain Points**
*   **Given** the business hypothesis and signals
*   **When** predicting challenges
*   **Then** the engine must list 2 to 3 likely pain points the prospect is currently experiencing that specifically align with the client's core services.

**Scenario 3: Crafting the Consulting Angle**
*   **Given** the identified pain points
*   **When** finalizing the insight report
*   **Then** the system must generate a "Consulting Angle"—a specific, actionable recommendation on how the client should position their first outreach or pitch to resonate deeply with the prospect.

## Technical Notes & Considerations
*   *Data sources to be used:* The full prospect profile, signals (US-04), and the client's own services/positioning extracted during onboarding (US-01).
*   *Potential edge cases or error states:* LLM hallucinations. The system prompt must strictly ground the generated insights in the actual detected signals. If signals are sparse, the insights should be framed as generalized hypotheses rather than stated facts.
*   *Any specific UI/UX requirements:* These insights should be displayed prominently on the prospect's detail view in the dashboard, visually distinct from raw data to indicate they are AI-generated strategic conclusions.
