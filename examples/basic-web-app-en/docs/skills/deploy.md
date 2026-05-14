# Deploy-stage rules

- Confirm JWT secrets and database connection settings before release
- Run smoke tests against the login and task-list endpoints after deployment
- If release fails, roll back to the previous image version first
