# 1-endpoints: REST Endpoints

Parent: [[../wbs.md]]
Status: blocked
Objective: CRUD endpoints for users and resources

## Verification

- [ ] run: npm test -- --grep "endpoints"
- [ ] Manual: Can create, read, update, delete resources

## Tasks

- [ ] GET /users
- [ ] POST /users
- [ ] GET /users/:id
- [ ] PUT /users/:id
- [ ] DELETE /users/:id

## State

Last: none
Progress: Not started - blocked by auth
Blockers: [[../../1-auth/]] endpoints need auth middleware
Next: Implement GET /users once auth available
