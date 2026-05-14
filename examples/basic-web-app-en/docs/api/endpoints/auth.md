# API module: auth

## `POST /api/auth/login`

- Purpose: log in a user and return a token
- Request body:
  - `email`: string
  - `password`: string
- Success response: `200`

```json
{
  "token": "jwt-token",
  "user": {
    "id": "user_123",
    "email": "alice@example.com",
    "displayName": "Alice"
  }
}
```

- Failure responses:
  - `400`: missing parameters
  - `401`: invalid email or password
  - `403`: user is disabled
