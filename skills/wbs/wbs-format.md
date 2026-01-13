# WBS File Format Reference

This document provides detailed format specifications for WBS files. Referenced by the main WBS skill when detailed format information is needed.

## Directory Structure

```
wbs/
├── wbs.md                      # Root - top-level items (no Parent line)
├── 1-auth/
│   ├── wbs.md                  # Auth breakdown
│   ├── 1-google-oauth/
│   │   └── wbs.md
│   └── 2-session-mgmt/
│       └── wbs.md
└── 2-api/
    └── wbs.md
```

**Naming conventions:**
- Folders: `{N}-{slug}/` where N is monotonically incrementing within parent
- Slugs: kebab-case, POSIX-compliant
- Files: always `wbs.md`

## File Template

### Standard Task (non-root)

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

### Root Task

```markdown
# Project: {name}

Status: not-started
Objective: <project goal>

## Verification

- [ ] <project-level success criteria>

## Tasks

- [ ] <top-level items>

## State

Last: none
Progress: Initialized
Blockers: none
Next: Define and break down top-level tasks
```

Root `wbs/wbs.md` has no Parent line. Title uses `# Project: {name}` format.

## Verification Syntax

Verification items should be automated where possible:

```markdown
## Verification

- [ ] run: tests/auth/**/*_test.*
- [ ] run: cargo test auth
- [ ] run: npm test -- --grep "auth"
- [ ] Manual: OAuth flow works in staging environment
```

The `run:` prefix indicates an executable command or glob pattern. Use whatever test runner the project uses.

## Task Line Formats

| Format | Meaning |
|--------|---------|
| `- [ ] Task name` | Not started leaf task |
| `- [x] ~~Task~~ (hash: outcome)` | Completed task |
| `- [ ] Task → [[1-subfolder/]]` | Task with breakout subfolder |
| `- [~] Task` | In-progress (used in visualization) |
| `- [!] Task` | Blocked (used in visualization) |

## Status Values

| Value | Meaning |
|-------|---------|
| `not-started` | Work has not begun |
| `in-progress` | Actively being worked on |
| `done` | All verification passed, work complete |
| `blocked` | Cannot proceed, see Blockers field |

## Blocker Syntax

Reference blocking tasks using wiki-link syntax:

```markdown
Blockers: [[../1-auth/]] need JWT tokens for API auth
```

Multiple blockers on separate lines:

```markdown
Blockers: [[../1-auth/]] waiting on token implementation
[[../2-database/]] schema migrations pending
```

## Link Syntax Reference

| Link | Purpose |
|------|---------|
| `[[../wbs.md]]` | Parent navigation |
| `[[1-subfolder/]]` | Child breakout |
| `[[path/to/task/]]` | Blocker reference |

## Commit References

All commit references use 7-character short hashes:
- `Last: a1b2c3d` in State section
- `(a1b2c3d: outcome)` in completed task lines
