# Table: tasks

## Fields
- `id`: UUID, primary key
- `title`: varchar, required
- `description`: text, nullable
- `status`: varchar, required, default `todo`
- `assignee_id`: UUID, nullable, references `users.id`
- `created_at`: timestamp, required
- `updated_at`: timestamp, required

## Constraints
- `status` may only be `todo`, `in_progress`, or `done`
- If `assignee_id` is present, it must reference a valid user

## Purpose
Stores the core task state and assignee information.
