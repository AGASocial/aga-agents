# Technical Tasks for User Story: US-01a - Client Registration & Intake

## iOS Tasks
- **Task 1:** Create Registration View
  - **Description:** Build the registration UI using SwiftUI (or UIKit). Include text fields for basic account details, company website, and LinkedIn URL. Ensure the design is clean and clearly differentiates between mandatory and optional fields.
  - **Acceptance Criteria:** View looks correct across different iPhone screen sizes and respects Dark/Light modes. Mandatory fields are clearly indicated.
  - **Dependencies:** None

- **Task 2:** Implement Registration ViewModel
  - **Description:** Create a ViewModel (MVVM architecture) to manage form state and validation. Add logic to validate email formats, URL structures, and ensure mandatory fields are populated before submission is allowed.
  - **Acceptance Criteria:** Validation logic correctly identifies missing or malformed data and updates the view state to show errors.
  - **Dependencies:** Task 1

- **Task 3:** Integrate API Client & Network Call
  - **Description:** Define the network request and response models (using `Codable`) for the `POST /api/v1/clients/register` endpoint. Implement the API call within the ViewModel, handling loading states, success navigation, and error presentation.
  - **Acceptance Criteria:** The app successfully registers a user via the API and navigates to the next screen. Any network or validation errors from the backend are displayed to the user via alerts or inline text.
  - **Dependencies:** Task 2, Backend Task 2
