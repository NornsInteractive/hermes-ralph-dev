# Table: users

## Fields
- `id`: UUID, primary key
- `email`: varchar, unique, required
- `password_hash`: varchar, required
- `display_name`: varchar, required
- `status`: varchar, required, default `active`
- `created_at`: timestamp, required

## Constraints
- `email` must be unique
- `status` may only be `active` or `disabled`

## Purpose
Stores login-enabled user accounts for the system.
