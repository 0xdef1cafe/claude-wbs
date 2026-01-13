---
description: Initialize a new WBS in the current repository. Creates wbs/wbs.md with project structure.
---

# Initialize WBS

Create a new Work Breakdown Structure in the current repository.

## Instructions

1. **Check preconditions:**
   - Verify `wbs/` directory does not already exist
   - If it exists, abort and inform user

2. **Gather information:**
   - Ask user for project name (or infer from repo/directory name)
   - Ask user for project objective (what does "done" look like?)
   - Ask for initial top-level tasks (or offer to help decompose)

3. **Create structure:**
   - Create `wbs/` directory
   - Create `wbs/wbs.md` using the template below

4. **Commit:**
   - `git add wbs/`
   - `git commit -m "wbs: initialize project structure"`

5. **Next steps:**
   - Prompt user to define/refine top-level tasks
   - Offer to help break down tasks with `/wbs-breakout`

## Template for `wbs/wbs.md`

```markdown
# Project: {name}

Status: not-started
Objective: {objective}

## Verification

- [ ] {project-level success criteria}

## Tasks

- [ ] {initial task 1}
- [ ] {initial task 2}

## State

Last: none
Progress: Initialized
Blockers: none
Next: Define and break down top-level tasks
```

## Example Interaction

User: `/wbs-init`

Claude: I'll initialize a WBS for this project. A few questions:

1. **Project name?** (I see the repo is called "acme-api")
2. **What does "done" look like?** (e.g., "API deployed to production with auth and CRUD endpoints")
3. **Initial high-level tasks?** (e.g., "Auth system", "Core API", "Deployment")

Or reply "defaults" and I'll infer from the codebase.
