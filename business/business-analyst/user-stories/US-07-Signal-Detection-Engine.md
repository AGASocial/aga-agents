# US-04: Signal Detection Engine

**Epic:** Prospect Discovery & Enrichment
**Priority:** High

## Description
As the system (acting on behalf of the client),
I want to scrape the digital presence of each discovered prospect to detect key business signals (e.g., hiring, scaling, content gaps),
So that I can identify the 3-5 most compelling reasons why this prospect might need the client's services right now.

## Context / Background
*Simply knowing a company fits the ICP is not enough. To do targeted outreach, we need to know what is happening inside the company. The Signal Detection Engine visits the prospect's website, blog, and social media (LinkedIn) to look for "triggers" or "signals" that indicate a problem the client can solve.*

## Acceptance Criteria

**Scenario 1: Scraping Prospect Digital Presence**
*   **Given** a raw prospect lead in the database
*   **When** the Signal Detection Engine is queued for that prospect
*   **Then** it should utilize a web scraping tool (e.g., Firecrawl) to read their main website, careers page, and latest social media posts.

**Scenario 2: Detecting Business Signals**
*   **Given** the raw scraped text data from a prospect
*   **When** an AI agent analyzes the text
*   **Then** it must identify specific business signals such as:
    *   Hiring activity (e.g., "Currently hiring 5 new engineers")
    *   Scaling indicators (e.g., "Recently secured Series A funding", "Opening a new office")
    *   Content gaps / digital maturity (e.g., "Blog hasn't been updated in 6 months", "No clear value prop on homepage").

**Scenario 3: Outputting Signal Data**
*   **Given** the signals have been detected
*   **When** saving the analysis to the prospect profile
*   **Then** the system must output exactly 3 to 5 distinct signals per company
*   **And** provide a short, one-sentence rationale for *why* each signal is relevant to the client's offering.

## Technical Notes & Considerations
*   *Data sources to be used:* Firecrawl for deep website scraping; potentially a LinkedIn scraping API for social posts and job listings. An LLM (e.g., GPT-4o) to process the large context window of scraped text and extract the signals.
*   *Potential edge cases or error states:* Websites with strong bot protection (Cloudflare) blocking the scraper. The agent needs a fallback mechanism (e.g., relying only on the home page or Tavily search results if deep scraping fails).
*   *Any specific UI/UX requirements:* These signals will be displayed as pill-tags or bullet points on the Prospect Workspace dashboard (US-09).
