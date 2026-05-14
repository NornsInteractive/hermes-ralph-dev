# Ralph Fix Plan

## High Priority
- [ ] Implement `POST /api/auth/login`, validate email and password, return a JWT access token, and follow `docs/api/endpoints/auth.md`
- [ ] Implement `GET /api/tasks` with filters for status and assignee, following `docs/api/endpoints/tasks.md`
- [ ] Implement `POST /api/tasks` to create a task record in the `tasks` table, following `docs/database/tables/tasks.md`
- [ ] Implement `PATCH /api/tasks/:id` to update title, description, status, and assignee

## Medium Priority
- [ ] Add unit tests and integration tests for the authentication and task APIs
- [ ] Update the README with API usage examples

## Low Priority
- [ ] Clean up duplicated validation logic

## Completed
- [x] Ralph enabled for this project

## Notes
- Update the checkbox state immediately when a task is completed
- After all tasks are done, write the completion time and summary to `.ralph/DONE`
