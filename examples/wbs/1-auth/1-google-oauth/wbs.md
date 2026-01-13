# 1-google-oauth: Google OAuth

Parent: [[../wbs.md]]
Status: in-progress
Objective: Users can sign in with their Google account

## Verification

- [ ] run: npm test -- --grep "google oauth"
- [ ] Manual: OAuth flow redirects to Google and back

## Tasks

- [x] ~~Set up OAuth credentials~~ (c3d4e5f: added to .env.example)
- [ ] Implement redirect endpoint
- [ ] Handle callback and token exchange
- [ ] Create user session

## State

Last: c3d4e5f
Progress: Credentials configured, implementing redirect
Blockers: none
Next: Implement /auth/google redirect endpoint
