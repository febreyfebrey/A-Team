---
name: Output Structure
description: Specify directory configuration and naming rules for all generated team structures
---

# Output Structure

## Applicability

- Applies to: `team-architect`, `agent-writer`, `skill-writer`, `rule-writer`

## Rule Content

### Directory Structure

All generated team structures must follow this directory configuration:

```
teams/{team-name}/
├── CLAUDE.md                         ← Team-wide instructions all agents must follow
└── .claude/
    ├── agents/
    │   ├── {coordinator}.md          ← Coordinator, in agents/ root directory
    │   ├── {group-a}/                ← Grouped by function or workflow
    │   │   ├── {agent-1}.md
    │   │   └── {agent-2}.md
    │   └── {group-b}/
    │       └── {agent-3}.md
    ├── skills/
    │   ├── {skill-1}/                ← Each skill has its own folder
    │   │   └── SKILL.md              ← Fixed filename (uppercase)
    │   ├── {skill-2}/
    │   │   └── SKILL.md
    │   └── {skill-3}/
    │       └── SKILL.md
    └── rules/
        ├── {rule-1}.md
        └── {rule-2}.md
```

### Naming Conventions

- Team name: kebab-case, reflecting team purpose (e.g., `english-teaching-content`)
- Folder names: kebab-case
- File names: kebab-case, ending with `.md`
- Spaces, underscores, and uppercase letters are prohibited

### CLAUDE.md Content Guidelines

`CLAUDE.md` is placed at the team root (`teams/{team-name}/CLAUDE.md`), at the same level as `.claude/`. It contains instructions that **every agent in the team** must follow. When deployed, it becomes the project-level `CLAUDE.md` that Claude Code automatically loads for all agents and teammates.

Content to include in CLAUDE.md:
- Team objectives and scope summary
- Universal behavioral norms (e.g., communication language, output format)
- Project-wide technical constraints (e.g., tech stack, coding standards)
- Deployment mode instructions (subagent vs Agent Teams, see below)

Content NOT to include in CLAUDE.md (put these in `rules/` instead):
- Rules that apply to only a subset of agents
- Role-specific behavioral constraints
- Quality standards for specific deliverable types

### Deployment Mode Section in CLAUDE.md

Every generated CLAUDE.md must include a deployment mode section specifying how the team is intended to run:

- **Subagent mode**: Agents are invoked via the Task tool within a single session. Coordinator manages all delegation. Suitable for sequential workflows with clear handoffs.
- **Agent Teams mode** (experimental): Agents run as independent Claude Code instances with shared task lists and direct messaging. Suitable for parallel workflows where agents need peer-to-peer communication. Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` to be enabled.

### Placement Rules

- `CLAUDE.md` must be at the team root directory, at the same level as `.claude/`
- Coordinator .md must be in `agents/` root directory, cannot be placed in subfolders
- Non-coordinator agents must be in subfolders under `agents/`
- Each skill must have its own folder, containing `SKILL.md` (uppercase)
- All rules are in `rules/` root directory, rules do not have subfolders

## Violation Determination

- Team root directory missing `CLAUDE.md` → Violation
- `CLAUDE.md` placed inside `.claude/` instead of at team root → Violation
- `CLAUDE.md` missing deployment mode section → Violation
- Coordinator .md appears in a subfolder → Violation
- Non-coordinator agent placed directly in `agents/` root directory (same level as coordinator) → Violation
- Skill exists directly as `.md` file instead of `{skill-name}/SKILL.md` format → Violation
- File or folder name does not follow kebab-case → Violation

## Exceptions

- If the team has only 1 group (e.g., all agents belong to the same specialty), at least one subfolder must still be created; agents cannot be placed directly in `agents/` root directory
