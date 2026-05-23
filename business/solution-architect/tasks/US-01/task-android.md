# Technical Tasks for User Story: US-01a - Client Registration & Intake

## Android Tasks
- **Task 1:** Create Registration UI Layout
  - **Description:** Develop the registration screen using Jetpack Compose (or XML layouts). Include input fields for company website, LinkedIn URL, and core account details. Ensure the design is clean, welcoming, and clearly marks mandatory vs. optional inputs.
  - **Acceptance Criteria:** The UI renders correctly on various device sizes and orientations. Required fields are visually distinct.
  - **Dependencies:** None

- **Task 2:** Implement Registration ViewModel
  - **Description:** Create a ViewModel (MVVM architecture) to handle the registration screen's state. Implement local validation logic for mandatory fields and URL formats to provide immediate feedback.
  - **Acceptance Criteria:** Form state is preserved during configuration changes. Invalid inputs trigger appropriate UI error states.
  - **Dependencies:** Task 1

- **Task 3:** Implement Network Call & Integration
  - **Description:** Define the Retrofit interface for the `POST /api/v1/clients/register` endpoint. Update the repository layer to execute the call. Connect the ViewModel to observe the network result, handle loading indicators, and process success/error responses.
  - **Acceptance Criteria:** Valid forms are submitted to the API. Network errors and backend validation errors are surfaced to the user. Success triggers navigation to the next onboarding screen.
  - **Dependencies:** Task 2, Backend Task 2
