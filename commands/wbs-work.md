---
description: Find and start working on a WBS task by query. Usage: /wbs-work <query>
---

# Work on WBS Task

Find a task by name/slug and begin working on it.

## Arguments

- `$ARGUMENTS`: Search query to match against task names and folder slugs

## Instructions

1. **Find matching tasks:**
   - List all `wbs.md` files under `wbs/`
   - Match `$ARGUMENTS` against:
     - Folder slugs (e.g., "auth" matches `1-auth/`)
     - Task titles in `# Title` lines
     - Task names in `## Tasks` sections
   - Matching is case-insensitive and partial

2. **Handle ambiguity:**
   - If no matches: inform user, suggest checking `/wbs` for available tasks
   - If multiple matches: list all matches with paths, ask user to pick
   - If single match: proceed to step 3

3. **Navigate to task:**
   - Read the matching `wbs.md` file
   - Display current state (Status, Progress, Blockers, Next)

4. **Check if workable:**
   - If Status is `blocked`: show blocker, ask if user wants to work on blocker instead
   - If Status is `done`: inform user task is complete
   - If task has breakout links (`→ [[path/]]`): suggest navigating to leaf tasks

5. **Activate task:**
   - Update Status to `in-progress` if not already
   - Display the task's Objective and Verification criteria
   - Show what "Next" action was planned

6. **Begin work:**
   - If task is clear, start executing
   - If task needs clarification, ask user before proceeding
   - Remember to update State section as work progresses

## Example

User: `/wbs-work auth`

Claude: Found task matching "auth":

**1-auth: Authentication System**
- Status: not-started
- Objective: Users can sign in with Google/GitHub OAuth
- Next: Implement Google OAuth flow

This task has breakouts:
- `1-google-oauth/` (not-started)
- `2-github-oauth/` (not-started)

Would you like to work on `1-google-oauth` (the first leaf task)?
