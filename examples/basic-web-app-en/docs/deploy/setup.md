# Environment setup

## Local development
- Install Node.js 20+
- Install PostgreSQL 15+
- Configure `.env`, including at least the database connection string and JWT secret
- Run database migrations before starting the application

## CI / production constraints
- The service runs inside Linux containers
- CI must run tests before deployment
