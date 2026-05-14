# API overview

## Conventions
- All responses use JSON
- Successful responses include the requested business data
- Error responses are shaped as `{ code, message }`
- Protected endpoints require `Authorization: Bearer <token>`

## Module entry points
- Auth: `docs/api/endpoints/auth.md`
- Tasks: `docs/api/endpoints/tasks.md`
