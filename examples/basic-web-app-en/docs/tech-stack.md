# Tech stack notes

## Stack
- Language: TypeScript
- Service framework: Express
- Database: PostgreSQL
- ORM / data layer: SQL + migration tooling
- Testing: Vitest, Supertest

## Constraints
- All APIs return JSON
- Protected endpoints use Bearer Token authentication
- The first version does not need a complex permission system yet; only authenticated identity checks are required
