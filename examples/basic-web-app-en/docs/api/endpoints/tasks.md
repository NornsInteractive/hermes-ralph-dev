# API module: tasks

## `GET /api/tasks`

- Purpose: return the task list
- Query parameters:
  - `status`: `todo | in_progress | done`
  - `assigneeId`: string
- Success response: `200`

## `POST /api/tasks`

- Purpose: create a task
- Request body:
  - `title`: string
  - `description`: string
  - `assigneeId`: string, optional
- Success response: `201`

## `PATCH /api/tasks/:id`

- Purpose: update a task
- Request body:
  - `title`: string, optional
  - `description`: string, optional
  - `status`: string, optional
  - `assigneeId`: string, optional
- Failure responses:
  - `400`: invalid parameters
  - `404`: task not found
