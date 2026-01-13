# WBS - Work Breakdown Structure Plugin for Claude Code

A Claude Code plugin for hierarchical task decomposition using markdown files. Enables structured project planning, progress tracking, and context resumption across sessions.

## Why WBS?

1. **Context rot** - Models degrade in long sessions. WBS keeps tasks scoped and small.
2. **Roadmap in repo** - Plan and code co-evolve. No sync drift with external PM tools.
3. **Multi-agent coordination** - Multiple Claude instances can work on independent subtrees.
4. **Resumability** - Any fresh Claude can read the WBS and continue where you left off.

## Installation

```bash
# Add the marketplace (one-time)
/plugin marketplace add 0xdef1cafe/claude-wbs

# Install the plugin
/plugin install wbs@claude-wbs

# Or for local development
claude --plugin-dir /path/to/wbs
```

After installation, the WBS commands and agents will be available in any project.

## Commands

| Command | Description |
|---------|-------------|
| `/wbs` | Show project status as ASCII tree |
| `/wbs-init` | Initialize WBS in current repo |
| `/wbs-work <query>` | Find and start working on a task |
| `/wbs-breakout <query>` | Break a leaf task into subtasks |

## Quick Start

```bash
# Initialize in your project
/wbs-init

# View status
/wbs

# Start working
/wbs-work auth

# Decompose a complex task
/wbs-breakout "implement oauth"
```

## Example Output

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

## Directory Structure

```
wbs/
├── wbs.md                      # Root - project overview
├── 1-auth/
│   ├── wbs.md                  # Auth breakdown
│   ├── 1-google-oauth/
│   │   └── wbs.md              # Leaf task
│   └── 2-session-mgmt/
│       └── wbs.md
└── 2-api/
    └── wbs.md
```

## File Format

Each `wbs.md` follows this structure:

```markdown
# {N}-{slug}: {Title}

Parent: [[../wbs.md]]
Status: not-started | in-progress | done | blocked
Objective: What "done" looks like

## Verification

- [ ] run: npm test -- --grep "feature"
- [ ] Manual: Feature works in staging

## Tasks

- [ ] Leaf task (no breakout)
- [ ] Task with breakout → [[1-subfolder/]]
- [x] ~~Completed task~~ (a1b2c3d: outcome)

## State

Last: a1b2c3d
Progress: Current status
Blockers: none | [[path/]] reason
Next: What to pick up
```

## Key Conventions

- **MECE decomposition** - Each level covers all work without overlap
- **Folder naming** - `{N}-{slug}/` with monotonic N, kebab-case slug
- **Links** - `→ [[path/]]` for breakouts, `[[../wbs.md]]` for parent
- **Verification** - `run:` prefix for automated checks
- **Session discipline** - Always update State before ending session

## Agents

The plugin includes specialized agents:

| Agent | Description |
|-------|-------------|
| `wbs-planner` | Helps decompose projects into MECE hierarchies |
| `wbs-reviewer` | Audits WBS structure for completeness and quality |

Claude will automatically invoke these agents when appropriate, or you can request them directly.

## Troubleshooting

**Plugin not found after install:**
```bash
# Verify marketplace is added
/plugin marketplace list

# Reinstall
/plugin install wbs@claude-wbs
```

**Commands not recognized:**
- Ensure you're in a Claude Code session (not the API)
- Check plugin is installed: `/plugin list`

**WBS not detected:**
- The skill activates when a `wbs/` folder exists in the repo
- Run `/wbs-init` to create the initial structure

## Full Documentation

See [skills/wbs/SKILL.md](skills/wbs/SKILL.md) for complete conventions.

## License

MIT
