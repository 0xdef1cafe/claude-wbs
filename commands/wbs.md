---
description: Show WBS status as ASCII tree. Use when asked to show project status, "where are we", or just "/wbs".
---

# Show WBS Status

Display the current Work Breakdown Structure status as an ASCII tree.

## Instructions

1. Find all `wbs.md` files under `wbs/` directory
2. Build a tree structure by reading each file's Status field
3. Output ASCII tree with status indicators:
   - `[x]` done
   - `[ ]` not-started
   - `[~]` in-progress
   - `[!]` blocked

4. Mark the current active task (in-progress) with `← active`
5. For blocked items, show the blocker source in parentheses

## Output Format

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

## Algorithm

1. Start at `wbs/wbs.md` - this is the root
2. For each task line in the Tasks section:
   - If it has a `→ [[path/]]` link, recurse into that subfolder
   - Read the Status field from the linked `wbs.md`
3. Build tree output with proper box-drawing characters (├── └── │)
4. Include the legend at the bottom

## Edge Cases

- If `wbs/` directory doesn't exist, inform user to run `/wbs-init`
- If a linked subfolder is missing its `wbs.md`, mark as `[?]` with error note
