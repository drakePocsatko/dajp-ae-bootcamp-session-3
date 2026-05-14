# Product Requirements Document (PRD) - TODO App Upgrade

## 1. Overview

We are upgrading the basic TODO app to make task management more practical while keeping the implementation simple and teachable for the bootcamp. The product change focuses on adding lightweight planning features so users can understand urgency and organize work without introducing backend changes or advanced workflow complexity.

The MVP will add optional due dates, simple priority levels, and task filters for time-based views, while keeping all storage local. Additional visual polish and more advanced ordering logic are intentionally deferred until after MVP so the initial delivery stays focused.

---

## 2. MVP Scope

- Add a `dueDate` field to tasks.
- Store `dueDate` as an optional ISO date string in `YYYY-MM-DD` format.
- Add a `priority` field to tasks.
- Limit `priority` values to `P1`, `P2`, or `P3`.
- Default `priority` to `P3` when a new task is created.
- Keep `title` as a required field.
- Ignore invalid `dueDate` values and treat them as absent.
- Add task filters for `All`, `Today`, and `Overdue`.
- Ensure the `All` view continues to show completed tasks.
- Ensure the `Today` view shows only incomplete tasks due today.
- Ensure the `Overdue` view shows only incomplete tasks with due dates before today.
- Keep data storage local only.
- Do not introduce backend or external storage changes for MVP.

---

## 3. Post-MVP Scope

- Visually highlight overdue tasks so they stand out in the task list.
- Add task sorting with this precedence: overdue tasks first, then priority from `P1` to `P3`, then due date ascending, then tasks without a due date last.

---

## 4. Out of Scope

- Notifications or reminders.
- Recurring tasks.
- Multi-user support.
- Keyboard navigation enhancements.
- External or shared storage.
- Any backend feature expansion beyond supporting the current local-only MVP approach.