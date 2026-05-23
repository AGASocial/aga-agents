# US-05: Opportunity Scoring

**Epic:** Evaluation & Insights
**Priority:** High

## Description
As the system (acting on behalf of the client),
I want to automatically score and prioritize each discovered prospect based on their ICP fit and detected business signals,
So that the client can immediately focus their attention on the highest-value opportunities.

## Context / Background
*Once we have found a prospect (US-03) and extracted their signals (US-04), we need a way to filter the noise. Not all leads are equal. The Opportunity Scoring engine uses an AI model to weigh how well the company matches the ideal profile against the strength and urgency of the signals found. This determines if the lead is Hot, Warm, or Low priority.*

## Acceptance Criteria

**Scenario 1: Calculating the Opportunity Score**
*   **Given** a prospect profile complete with demographic data and detected business signals
*   **When** the Opportunity Scoring engine processes the profile
*   **Then** it must evaluate two main dimensions:
    1.  **ICP Fit:** How closely their industry, size, and type match the client's ICP.
    2.  **Signal Strength:** The relevance, urgency, and number of detected signals (e.g., actively hiring for a related role scores higher than just having an old blog).

**Scenario 2: Assigning Priority Tiers**
*   **Given** the evaluation from Scenario 1
*   **When** the final score is calculated
*   **Then** the system must assign the prospect to one of three priority tiers:
    *   **Hot:** Perfect ICP fit + strong/urgent signals.
    *   **Warm:** Good ICP fit + moderate signals OR perfect fit with weak signals.
    *   **Low:** Marginal fit or no compelling signals detected.

**Scenario 3: Updating the Prospect Record**
*   **Given** the priority tier has been assigned
*   **When** the record is saved to the database
*   **Then** the UI (Prospect Workspace) must immediately reflect this new status, allowing the client to sort/filter by "Hot" leads.

## Technical Notes & Considerations
*   *Data sources to be used:* The internal database record containing the prospect's data (from US-03) and signal array (from US-04).
*   *Potential edge cases or error states:* If signal extraction failed in US-04, the lead might default to "Low" or "Warm" depending solely on ICP fit. We need a clear fallback logic.
*   *Any specific UI/UX requirements:* The scoring should be visually represented in the dashboard using color coding (e.g., Red/Fire icon for Hot, Orange for Warm, Gray for Low).
