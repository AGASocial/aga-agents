# Web Testing Workflow

Follow this step-by-step workflow to verify web features.

## 1. Automated Unit & Component Testing
- Run test command: `npm run test` (or the project's specific command)
- Target: Ensure all tests pass.
- Verify coverage reports to meet the 80% line coverage threshold on service files.

## 2. Visual & Layout Inspections
- Check components in the browser to ensure responsive designs (mobile, tablet, desktop).
- Verify color contrast ratios are ≥4.5:1.
- Confirm styling, spacing, and typography conform to the design system.

## 3. Keyboard & Screen Reader Pass
- Unplug the mouse: navigate the entire feature using only `Tab`, `Shift+Tab`, `Space`, `Enter`, and `Escape`.
- Confirm visual focus is always visible and clear.
- Verify that interactive elements have descriptive screen-reader labels.
