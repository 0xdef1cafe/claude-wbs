---
description: Break a leaf task into subtasks. Usage: /wbs-breakout <query>
---

# Break Out WBS Task

Decompose a leaf task into a subfolder with its own WBS.

## Arguments

- `$ARGUMENTS`: Search query to find the task to break out

## Instructions

1. **Find the task:**
   - Search `wbs.md` files for task matching `$ARGUMENTS`
   - Handle ambiguity same as `/wbs-work` (list matches if multiple)

2. **Validate it's a leaf:**
   - Check that task line does NOT have `→ [[path/]]` link
   - If already broken out, inform user and show existing breakout path

3. **Determine folder name:**
   - Scan current folder for highest N in `{N}-{slug}/` pattern
   - Increment N for new folder
   - Generate slug from task name (kebab-case, POSIX-compliant)
   - Confirm slug with user or let them override

4. **Create breakout structure:**
   - Create folder `{N}-{slug}/`
   - Create `{N}-{slug}/wbs.md` with template

5. **Update parent:**
   - Find the task line in parent's `## Tasks`
   - Replace: `- [ ] Task name` → `- [ ] Task name → [[{N}-{slug}/]]`

6. **Populate new WBS:**
   - Ask user for:
     - Objective (what does "done" look like for this subtask?)
     - Verification criteria
     - Sub-tasks (or offer to help decompose)
   - Or infer from context if user says "defaults"

7. **Commit:**
   - `git add` the new folder and updated parent
   - `git commit -m "wbs: break out {slug}"`

## Template for breakout `wbs.md`

```markdown
# {N}-{slug}: {Title}

Parent: [[../wbs.md]]
Status: not-started
Objective: {objective}

## Verification

- [ ] {testable criterion}

## Tasks

- [ ] {subtask 1}
- [ ] {subtask 2}

## State

Last: none
Progress: Broken out from parent
Blockers: none
Next: {first subtask or "Define subtasks"}
```

## Example

User: `/wbs-breakout "google oauth"`

Claude: Found leaf task "Implement Google OAuth" in `wbs/1-auth/wbs.md`.

Creating breakout:
- Folder: `1-google-oauth/` (N=1, first in 1-auth/)
- Will update parent task line with link

What subtasks should this include? For Google OAuth, typically:
1. Set up OAuth credentials in Google Console
2. Implement OAuth redirect endpoint
3. Handle OAuth callback and token exchange
4. Store user session

Want me to use these, or specify your own?
