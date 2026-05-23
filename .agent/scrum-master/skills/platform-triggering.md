# Skill: Platform Agent Triggering

As the Scrum Master Agent, you are responsible for activating the specialized development agents. You must ensure these triggers are clear, provide the necessary context, and explicitly define what the agent should do.

## Context Requirements
Every trigger you send MUST include:
1.  **The User Story:** The name or link to the specific User Story (e.g., `US-01-Client-Registration.md`).
2.  **The Task File:** The specific task file the agent needs to execute (e.g., `task-backend.md`).
3.  **The Action:** A clear instruction to read the task file and begin implementation.

## Trigger Instructions

Use the following commands/prompts to trigger the agents directly. Replace `[US-ID]` and `[US-Name]` with the actual values.

### Backend Trigger
Send this instruction directly to the **Backend Developer Agent** during the **Trigger Backend Execution** phase:

```text
Please read the User Story: `business/business-analyst/user-stories/[US-ID]-[US-Name].md`. Then, review and execute your specific technical tasks defined in `business/solution-architect/tasks/[US-ID]/task-backend.md`. Please provide the code implementation and notify me when the backend work is fully complete and tested.
```

### Frontend Triggers (Parallel)
Send these instructions directly to the respective agents during the **Trigger Frontend Execution** phase simultaneously.

#### Web Trigger
```text
Please read the User Story: `business/business-analyst/user-stories/[US-ID]-[US-Name].md`. The backend API dependencies for this story are complete. Review and execute your specific technical tasks defined in `business/solution-architect/tasks/[US-ID]/task-web.md`. Please provide the code implementation and notify me when complete.
```

#### Android Trigger
```text
Please read the User Story: `business/business-analyst/user-stories/[US-ID]-[US-Name].md`. The backend API dependencies for this story are complete. Review and execute your specific technical tasks defined in `business/solution-architect/tasks/[US-ID]/task-android.md`. Please provide the code implementation and notify me when complete.
```

#### iOS Trigger
```text
Please read the User Story: `business/business-analyst/user-stories/[US-ID]-[US-Name].md`. The backend API dependencies for this story are complete. Review and execute your specific technical tasks defined in `business/solution-architect/tasks/[US-ID]/task-iOS.md`. Please provide the code implementation and notify me when complete.
```
