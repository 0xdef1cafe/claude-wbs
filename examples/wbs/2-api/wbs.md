# 2-api: API Layer

Parent: [[../wbs.md]]
Status: blocked
Objective: REST API with CRUD endpoints for core resources

## Verification

- [ ] run: npm test -- --grep "api"
- [ ] Manual: API responds to authenticated requests

## Tasks

- [ ] REST endpoints → [[1-endpoints/]]
- [ ] Rate limiting
- [ ] Error handling

## State

Last: none
Progress: Waiting on auth completion
Blockers: [[../1-auth/]] need JWT tokens for API authentication
Next: Implement endpoints once auth provides tokens
