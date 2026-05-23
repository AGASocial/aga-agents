# US-03: Autonomous Prospect Discovery

**Epic:** Prospect Discovery & Enrichment
**Priority:** High

## Description
As the system (acting on behalf of the client),
I want to autonomously search the web to find companies that match the approved Ideal Customer Profile (ICP),
So that I can build a targeted, deduplicated list of potential prospects without manual research.

## Context / Background
*This is the core engine for finding net-new leads. Using the ICP defined in US-02, an AI agent utilizes advanced search APIs (like Exa or Tavily) to scan the web and compile a list of companies that fit the criteria. The output is a raw lead list that is then passed downstream to the Signal Detection Engine.*

## Acceptance Criteria

**Scenario 1: Executing an AI Search Query**
*   **Given** an approved ICP exists for the client
*   **When** the prospect discovery agent is triggered (either manually or via schedule)
*   **Then** it should translate the ICP into effective semantic search queries for tools like Exa or Tavily
*   **And** retrieve a raw list of matching companies.

**Scenario 2: Data Extraction per Prospect**
*   **Given** a list of search results
*   **When** the agent processes each result
*   **Then** it must extract at minimum: Company Name, Website URL, LinkedIn Profile URL (if available), Estimated Size, and Industry.

**Scenario 3: Deduplication**
*   **Given** the agent has extracted a list of prospects
*   **When** adding them to the client's database
*   **Then** it must check against existing leads
*   **And** discard any duplicates (e.g., matching by exact domain URL or normalized company name) to ensure a clean lead list.

## Technical Notes & Considerations
*   *Data sources to be used:* Exa or Tavily APIs for semantic web search. 
*   *Potential edge cases or error states:* API rate limits, API timeouts, or empty search results if the ICP is too narrow. The agent should handle retries and perhaps broaden the search parameters slightly if 0 results are returned.
*   *Any specific UI/UX requirements:* This runs entirely in the background (backend). However, the client dashboard should show a status indicator during the initial run (e.g., "Finding prospects... 45 found so far").
