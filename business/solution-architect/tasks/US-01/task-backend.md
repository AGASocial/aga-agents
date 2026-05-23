# Technical Tasks for User Story: US-01a - Client Registration & Intake

## Backend (BE) Tasks
- **Task 1:** Define Database Schema for Client Registration
  - **Description:** Create or update the database schema (e.g., `Client` or `User` table) to store core account details, `company_website`, and `linkedin_url`. Create corresponding migration scripts.
  - **Acceptance Criteria:** Migration runs successfully and tables/columns are created in the database.
  - **Dependencies:** None

- **Task 2:** Implement Client Registration API Endpoint
  - **Description:** Develop a POST endpoint (e.g., `/api/v1/clients/register`) to accept the registration payload (company website, LinkedIn URL, basic account details). Implement strict validation to ensure mandatory fields are present and URLs are well-formed.
  - **Acceptance Criteria:** 
    - Returns `201 Created` on successful registration and creates the account.
    - Returns `400 Bad Request` with clear validation error messages if mandatory fields are missing or URLs are invalid.
  - **Dependencies:** Task 1

- **Task 3 [PARKED / DEFERRED]:** Setup Data Storage for Extraction Processes
  - **Description:** [Parked/Deferred for a later iteration] Ensure the captured URLs are properly queued or stored in a way that the background profiling/extraction processes can pick them up (e.g., publishing an event to a message broker or saving to an extraction queue table).
  - **Acceptance Criteria:** Upon successful registration, the URLs are available for subsequent async extraction services.
  - **Dependencies:** Task 2
