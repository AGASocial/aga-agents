# US-02: ICP Generation & Segmentation

**Epic:** Client Onboarding & Profiling
**Priority:** High

## Description
As a new client user,
I want the system to automatically generate and rank 1-3 target customer segments based on my extracted company data,
So that I can quickly review, edit, and approve the Ideal Customer Profile (ICP) without having to build it manually from scratch.

## Context / Background
*Once the system has extracted the client's business context (US-01), the next step is to define exactly who we are looking for. An AI agent synthesizes the extracted data to formulate specific target segments, ranking them by likelihood of conversion. The user must review and approve these segments before the autonomous discovery engine starts searching for actual prospects.*

## Acceptance Criteria

**Scenario 1: Automatic Generation and Ranking of Segments**
*   **Given** the system has successfully completed the Client Intake & Data Extraction process
*   **When** the AI agent processes the extracted data
*   **Then** the system should generate 1 to 3 distinct target customer segments (ICPs)
*   **And** rank these segments based on potential fit or conversion likelihood
*   **And** display them to the user for review.

**Scenario 2: Editing a Generated Segment**
*   **Given** the user is viewing the auto-generated customer segments
*   **When** they select a segment to edit
*   **Then** they can modify key parameters such as target industry, company size, geographic focus, or buyer personas
*   **And** save the changes to update the segment profile.

**Scenario 3: Approving the Final ICP**
*   **Given** the user has reviewed and/or edited the generated segments
*   **When** they approve one or more segments
*   **Then** the system should save the final Ideal Customer Profile (ICP)
*   **And** transition the onboarding state so the Prospect Discovery engine can begin its work.

## Technical Notes & Considerations
*   *Data sources to be used:* The output parameters from US-01 (client industry, services, target buyers, size, past sales data if available). An LLM (e.g., GPT-4o or Claude 3.5 Sonnet) will be prompted to generate the segments and ranking rationale.
*   *Potential edge cases or error states:* The LLM might generate overly broad, overlapping, or redundant segments; prompt engineering must enforce distinct, specific segments.
*   *Any specific UI/UX requirements:* An interactive review screen with visual cards for each segment, showing a "Fit Score" or ranking, and clear "Edit" / "Approve" actions.
