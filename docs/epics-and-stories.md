# Epics and Stories - TODO App Upgrade

## MVP

- Epic: Manage Task Due Dates
  - Story: Add an optional due date to a task
    - Acceptance Criteria: A user can create or edit a task without providing a due date.
    - Acceptance Criteria: A task with no due date remains valid and visible in the task list.
    - Technical Requirements: Extend the existing create and edit payload flow in `packages/frontend/src/TaskForm.js` so the task object continues to send `due_date` through `onSave` to the existing `POST /api/tasks` and `PUT /api/tasks/:id` requests in `packages/frontend/src/App.js`.
    - Technical Requirements: Preserve nullable `due_date` handling in the `tasks` table and the existing backend create and update statements in `packages/backend/src/app.js`.
  - Story: Accept task due dates in YYYY-MM-DD format
    - Acceptance Criteria: A task due date is stored in YYYY-MM-DD format.
    - Acceptance Criteria: A valid due date entered in YYYY-MM-DD format is preserved when the task is saved.
    - Technical Requirements: Keep the frontend date input in `packages/frontend/src/TaskForm.js` bound to a normalized `YYYY-MM-DD` string for both create and edit flows.
    - Technical Requirements: Validate and persist due dates in `packages/backend/src/app.js` using the existing `due_date` field name returned by the API so the list renderer in `packages/frontend/src/TaskList.js` can continue formatting the saved value.
  - Story: Ignore invalid task due date values
    - Acceptance Criteria: An invalid due date does not prevent the task from being saved.
    - Acceptance Criteria: An invalid due date is treated as if no due date was provided.
    - Technical Requirements: Add backend date validation in `packages/backend/src/app.js` so invalid `due_date` input is converted to `null` before insert or update.
    - Technical Requirements: Keep the frontend submit path in `packages/frontend/src/TaskForm.js` compatible with this behavior by allowing blank due dates and avoiding client-side coercion of invalid values into alternate formats.

- Epic: Manage Task Priorities
  - Story: Add a priority field to a task
    - Acceptance Criteria: Each task includes a priority value.
    - Acceptance Criteria: A task priority can be viewed after the task is saved.
    - Technical Requirements: Extend the current task object handled in `packages/frontend/src/TaskForm.js`, `packages/frontend/src/App.js`, and `packages/frontend/src/TaskList.js` to include a `priority` property.
    - Technical Requirements: Add a `priority` column to the `tasks` table and include it in the backend select, insert, and update logic in `packages/backend/src/app.js` so API responses expose the saved value.
  - Story: Default new tasks to P3 priority
    - Acceptance Criteria: A new task created without an explicit priority is assigned P3.
    - Acceptance Criteria: The saved task shows P3 when no other priority was selected.
    - Technical Requirements: Initialize new-task form state in `packages/frontend/src/TaskForm.js` with `priority: 'P3'` while continuing to populate edit state from the selected task.
    - Technical Requirements: Enforce the same default in `packages/backend/src/app.js` so direct API writes without a priority still persist `P3`.
  - Story: Restrict task priority values to P1, P2, and P3
    - Acceptance Criteria: Task priority values are limited to P1, P2, and P3.
    - Acceptance Criteria: A value outside P1, P2, and P3 is not accepted as a valid priority.
    - Technical Requirements: Add a constrained priority input in `packages/frontend/src/TaskForm.js` that only offers `P1`, `P2`, and `P3`.
    - Technical Requirements: Validate incoming `priority` values in `packages/backend/src/app.js` before insert or update and reject or normalize unsupported values.

- Epic: Filter Tasks by Time-Based Views
  - Story: Show all tasks in the All view
    - Acceptance Criteria: The All view shows tasks regardless of due date.
    - Acceptance Criteria: The All view includes completed tasks.
    - Technical Requirements: Add filter state above the list in `packages/frontend/src/App.js` or `packages/frontend/src/TaskList.js` and default it to `All`.
    - Technical Requirements: Keep the existing `GET /api/tasks` behavior in `packages/backend/src/app.js` compatible with the All view by returning both completed and incomplete tasks when no time-based filter is applied.
  - Story: Show incomplete tasks due today in the Today view
    - Acceptance Criteria: The Today view shows only incomplete tasks with a due date equal to today.
    - Acceptance Criteria: Completed tasks do not appear in the Today view.
    - Technical Requirements: Implement Today filtering against the fetched task collection in `packages/frontend/src/TaskList.js` using the existing `completed` and `due_date` fields returned by the API.
    - Technical Requirements: Reuse the current task refresh flow in `packages/frontend/src/App.js` and `packages/frontend/src/TaskList.js` so the Today view updates after create, edit, toggle, and delete actions.
  - Story: Show incomplete overdue tasks in the Overdue view
    - Acceptance Criteria: The Overdue view shows only incomplete tasks with a due date earlier than today.
    - Acceptance Criteria: Completed tasks do not appear in the Overdue view.
    - Technical Requirements: Implement Overdue filtering in `packages/frontend/src/TaskList.js` by comparing `due_date` values to the current local date and excluding completed tasks.
    - Technical Requirements: Keep backend task responses in `packages/backend/src/app.js` returning raw `due_date` and `completed` values needed for this client-side filter.

- Epic: Preserve Local-Only Task Storage
  - Story: Save due dates and priorities in local-only task storage
    - Acceptance Criteria: A saved task retains its due date after the app is reloaded.
    - Acceptance Criteria: A saved task retains its priority after the app is reloaded.
    - Technical Requirements: Persist the complete task shape, including `due_date` and `priority`, through the existing API-backed in-memory data layer defined in `packages/backend/src/app.js` for the current runtime session.
    - Technical Requirements: Keep frontend reads in `packages/frontend/src/TaskList.js` and edit hydration in `packages/frontend/src/TaskForm.js` aligned with the API response shape so saved values round-trip correctly.
  - Story: Keep task enhancement changes out of backend storage
    - Acceptance Criteria: The task enhancement scope does not require external storage.
    - Acceptance Criteria: Due date and priority support work without introducing backend persistence changes.
    - Technical Requirements: Avoid adding any new external database, service, or browser storage mechanism beyond the current in-memory SQLite setup in `packages/backend/src/app.js`.
    - Technical Requirements: Limit backend changes to the existing `/api/tasks` contract and schema in `packages/backend/src/app.js` rather than introducing new persistence infrastructure.

- Epic: Protect Core Task Entry Rules
  - Story: Require a title when creating a task
    - Acceptance Criteria: A task cannot be created without a title.
    - Acceptance Criteria: A task with a title can still be created when due date and priority use valid allowed values.
    - Technical Requirements: Preserve the current required-title check in `packages/frontend/src/TaskForm.js` so blank titles are blocked before submit.
    - Technical Requirements: Preserve the existing backend title validation in `packages/backend/src/app.js` for create and update requests as the final enforcement layer.

## Post-MVP

- Epic: Highlight Overdue Tasks
  - Story: Visually highlight overdue tasks in the task list
    - Acceptance Criteria: An overdue incomplete task is visually distinct from non-overdue tasks.
    - Acceptance Criteria: A task that is not overdue is not shown with overdue highlighting.
    - Technical Requirements: Extend the conditional styling logic in `packages/frontend/src/TaskList.js` so overdue incomplete tasks render with a distinct visual treatment in addition to the current completed-state styling.
    - Technical Requirements: Base overdue styling on the existing `due_date` and `completed` fields already returned by `GET /api/tasks` from `packages/backend/src/app.js`.

- Epic: Sort Tasks by Urgency
  - Story: Show overdue tasks before other tasks
    - Acceptance Criteria: Overdue tasks appear before non-overdue tasks in the task list.
    - Technical Requirements: Replace or augment the current backend ordering in `packages/backend/src/app.js`, which sorts by `due_date IS NULL, due_date ASC, created_at ASC`, to support overdue-first behavior.
  - Story: Sort tasks by priority from P1 to P3
    - Acceptance Criteria: Among tasks at the same sorting stage, P1 appears before P2 and P2 appears before P3.
    - Technical Requirements: Incorporate `priority` into the task ordering logic in `packages/backend/src/app.js` once the field is added to the schema and API responses.
  - Story: Sort tasks with due dates by earliest due date first
    - Acceptance Criteria: Among comparable dated tasks, earlier due dates appear before later due dates.
    - Technical Requirements: Preserve ascending `due_date` ordering for dated tasks within the updated urgency sort path in `packages/backend/src/app.js`.
  - Story: Place tasks without due dates after tasks with due dates
    - Acceptance Criteria: Tasks without a due date appear after tasks that have a due date.
    - Technical Requirements: Preserve the current undated-last behavior already expressed in the backend query in `packages/backend/src/app.js` when the broader urgency sort is implemented.