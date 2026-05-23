# US-01: Client Intake & Data Extraction

**Epic:** Client Onboarding & Profiling
**Priority:** High

## Description
As a new client user,
I want to provide my company website, LinkedIn profile, and optionally my CRM data,
So that the system can automatically extract my company's industry, services, positioning, target buyers, size, and growth stage.

## Context / Background
*This onboarding step follows client registration on the platform. Before we can find leads, the system needs a deep understanding of the client's own business. Instead of a long manual questionnaire, we leverage AI agents to extract this context directly from their existing digital footprint and past sales data.*

## Acceptance Criteria

**Scenario 1: Basic Intake via Website and LinkedIn**
*   **Given** a new client is on the onboarding screen
*   **When** they enter their company website URL and LinkedIn URL and submit
*   **Then** the system should successfully scrape both sources
*   **And** extract the following data points: Industry, Services offered, Positioning/Value Proposition, Target buyers, Company size, and Growth stage.

**Scenario 2: Optional CRM Data Ingestion**
*   **Given** the client is providing intake information
*   **When** they choose to connect their CRM or upload a CSV export of successful/unsuccessful sales
*   **Then** the system should ingest this historical data
*   **And** use it to refine the extracted target buyers and positioning.

**Scenario 3: Missing or Unreachable URLs**
*   **Given** the client submits a website or LinkedIn URL
*   **When** the URL is invalid, unreachable, or blocks scraping
*   **Then** the system should gracefully handle the error and prompt the user to manually enter the missing details or provide an alternative link.

## Technical Notes & Considerations
*   *Data sources to be used:* Web scraper (e.g., Firecrawl, Puppeteer) for the website; a specialized API or scraper for LinkedIn profiles; CSV parsing or basic OAuth for CRM data (HubSpot/Salesforce).
*   *Potential edge cases or error states:* Incomplete LinkedIn profiles, heavily JavaScript-rendered websites, or CRM data that is too messy to extract meaningful insights from. Rate limits on external APIs.
*   *Any specific UI/UX requirements:* A simple, clean intake form. A loading state with a progress indicator (e.g., "Analyzing your company...") is necessary since scraping and LLM extraction might take 10-30 seconds.
