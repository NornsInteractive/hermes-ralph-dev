# Module: authentication

## Goal
Provide user login and issue verifiable JWTs for protected endpoints.

## Inputs and outputs
- Input: email and password
- Output: access token and basic user info

## Rules
- Failed login should return a unified error response without revealing whether the account exists
- Tokens expire after 7 days by default
- Only users with `active` status can log in
