# Module: tasks

## Goal
Provide task listing, creation, and update operations with simple filtering.

## Inputs and outputs
- Input: task title, description, status, assignee
- Output: task details, task list, updated task object

## Rules
- Task title cannot be empty
- Status may only be `todo`, `in_progress`, or `done`
- Updating a missing task returns `404`
