# US-01a: Client Registration & Intake

**Epic:** Client Onboarding & Profiling
**Priority:** High

## Description
As a new client user,
I want to register for the platform and provide my company website, LinkedIn profile, and optionally my CRM data,
So that I can create my account and begin the onboarding process.

## Context / Background
*This is the first step in the Moroccan Mint platform. The system needs to capture the client's core digital footprint directly to initiate the background profiling processes.*

## Acceptance Criteria

**Scenario 1: Basic Intake Registration**
*   **Given** a new client is on the registration screen
*   **When** they enter their company website URL, LinkedIn URL, and basic account details and submit
*   **Then** the system should validate the inputs and create the user account
*   **And** store the provided URLs for subsequent extraction processes.

**Scenario 2: Missing or Invalid Inputs**
*   **Given** the client submits the registration form
*   **When** mandatory fields are missing or URLs are malformed
*   **Then** the system should display a clear validation error prompting the user to correct the information.

## Technical Notes & Considerations
*   *UI/UX requirements:* A simple, clean, and welcoming registration form. Clear indication of optional vs. mandatory fields.
