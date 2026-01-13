---
name: wbs
description: Work Breakdown Structure for project planning and execution. Use when working in a repo with a wbs/ folder, when asked to plan work, show project status, or work on tasks. Enables structured task decomposition, progress tracking, and context resumption for long-running work.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Work Breakdown Structure (WBS)

A WBS decomposes project work into a hierarchy of tasks. Each level should be MECE (mutually exclusive, collectively exhaustive). When all leaf tasks are done, the project is done.

This skill assumes git. All commit references are short hashes (7 chars).

## Directory Structure

```
wbs/
├── wbs.md                      # Root - top-level items
├── 1-auth/
│   ├── wbs.md                  # Auth breakdown
│   ├── 1-google-oauth/
│   │   └── wbs.md
│   └── 2-session-mgmt/
│       └── wbs.md
└── 2-api/
    └── wbs.md
```

Folder naming: `{N}-{slug}/` where N is monotonically incrementing within the parent, slug is kebab-case, POSIX-compliant.

## File Template

Each `wbs.md` follows this structure:

```markdown
# {N}-{slug}: {Title}

Parent: [[../wbs.md]]
Status: not-started | in-progress | done | blocked
Objective: <what "done" looks like for this subtree>

## Verification

- [ ] <testable criterion - automated where possible>
- [ ] run: <command or glob pattern for tests>
- [ ] <prose criterion if not automatable>

## Tasks

- [ ] Leaf task (no breakout)
- [ ] Task with breakout → [[1-subfolder/]]
- [x] ~~Completed task~~ (a1b2c3d: brief outcome)

## State

Last: a1b2c3d
Progress: <current status in plain terms>
Blockers: none | [[path/to/blocking-task/]] <reason>
Next: <what to pick up>
```

Root `wbs/wbs.md` has no Parent line. Its title is `# Project: {name}`.

## Verification Syntax

Verification items should be automated where possible:

```markdown
## Verification

- [ ] run: tests/auth/**/*_test.*
- [ ] run: cargo test auth
- [ ] run: npm test -- --grep "auth"
- [ ] Manual: OAuth flow works in staging environment
```

The `run:` prefix indicates an executable command or glob pattern. Claude should execute these to verify completion. Language-agnostic - use whatever test runner the project uses.

## Reading the WBS

1. Start at `wbs/wbs.md`
2. To find work: traverse Tasks looking for first non-done, non-blocked item
3. When item has `→ [[path/]]`, navigate there and recurse
4. When item has no link, it's a leaf - this is executable work
5. To understand context: read up the tree via Parent links
6. Blocked items: check if blocker path is done, if so, unblock

## Working a Task

1. Update Status to `in-progress`
2. If task lacks clarity, prompt user for clarification before proceeding
3. Do the work
4. Update State section as you progress (overwrite, don't append)
5. When complete, **execute all verification**:
   - Run each `run:` item and record pass/fail
   - Check manual items with user if needed
   - **All must pass to mark done**
6. If all verification passes:
   - Mark task done: `- [x] ~~Task name~~ (a1b2c3d: outcome)`
   - Update Status to `done`
7. If verification partially passes:
   - Status stays `in-progress`
   - Update State with what passed/failed
   - Do not shortcut to done - fix failures first
8. Commit changes including wbs.md updates

## Breaking Out a Task

When a leaf task needs decomposition:

1. Scan current folder for highest N, increment
2. Create folder `{N}-{slug}/`
3. Create `{N}-{slug}/wbs.md` using template
4. Replace leaf line with: `- [ ] Task name → [[{N}-{slug}/]]`
5. Populate new wbs.md with user or derive from context

## Blocked Tasks

Use `[[path/]]` syntax to reference blocking tasks:

```markdown
## State

Last: a1b2c3d
Progress: Waiting on auth to complete
Blockers: [[../1-auth/]] need JWT tokens for API auth
Next: Implement endpoint auth middleware once tokens available
```

When checking what to work on:

1. If Status is `blocked`, check if blocker path's Status is `done`
2. If blocker is done, update Status to `not-started` or `in-progress`
3. Skip blocked items when looking for next work

Multiple blockers: list each on its own line under Blockers.

## Completion Rollup

When all Tasks in a file are done AND all Verification passes:

1. Set Status to `done`
2. Navigate to Parent via `[[../wbs.md]]`
3. Find corresponding task line, mark done: `- [x] ~~Name~~ (a1b2c3d: summary)`
4. Check if parent now has all tasks done
5. Recurse up until a parent has incomplete tasks

## Session Discipline

**Mandatory before ending any session, including interruption:**

1. Commit current work with descriptive message
2. Update State section in the active wbs.md:

```markdown
## State

Last: a1b2c3d
Progress: Google OAuth callback implemented, error handling partial
Blockers: none
Next: Complete error handling for denied/expired states, then tests
```

1. If stopping mid-task, Status stays `in-progress`
2. This is mandatory - context dies without it

## ASCII Visualization

When asked to show WBS status (`/wbs` or "show wbs" or "where are we"):

```
wbs/
├── [x] 1-auth: Auth System (a1b2c3d)
│   ├── [x] 1-google-oauth: Google OAuth
│   ├── [~] 2-github-oauth: GitHub OAuth ← active
│   └── [ ] 3-token-refresh: Token Refresh
├── [ ] 2-api: API Layer
│   └── [!] 1-endpoints: REST Endpoints (blocked: 1-auth)
└── [ ] 3-payments: Payments

Legend: [x] done  [ ] not-started  [~] in-progress  [!] blocked
```

Generate by walking tree recursively, reading Status from each wbs.md.
Mark current working item with `← active`.
Show blocker source in parentheses for blocked items.

## Navigation

**Find task by name** (e.g., "work on auth"):

1. `find wbs/ -name "wbs.md"` to list all
2. Match query against folder slugs and `# Title` lines
3. Navigate to most specific match
4. If ambiguous, list matches and ask user

**Get parent**: follow `Parent: [[path]]` link
**Get siblings**: `ls` folders in same directory matching `N-*/`
**Get children**: `ls` subfolders containing `wbs.md`

## Init

When initializing (`/wbs init` or "create wbs" or "initialize project plan"):

1. Create `wbs/` folder at repo root
2. Create `wbs/wbs.md`:

```markdown
# Project: {name}

Status: not-started
Objective: <project goal - ask user if not clear>

## Verification

- [ ] <project-level success criteria>

## Tasks

- [ ] <initial high-level items>

## State

Last: none
Progress: Initialized
Blockers: none
Next: Define and break down top-level tasks
```

1. Commit: `git add wbs/ && git commit -m "wbs: initialize project structure"`
2. Prompt user to define top-level tasks or discuss scope

## Multi-Agent Considerations

When multiple Claude instances work in parallel (e.g., git worktrees):

1. Each instance should work on **different subtrees** - coordinate with user
2. Avoid editing the same wbs.md file from multiple instances
3. Parent rollup may cause merge conflicts - resolve by:
   - Taking the more complete state
   - Re-running verification to confirm
4. If you detect your wbs.md has been modified externally (git status), re-read before updating
5. Keep commits atomic and frequent to minimize conflict surface

This is inherently racy. User is responsible for assigning non-overlapping work. If conflicts occur, they're git conflicts - resolve normally.
