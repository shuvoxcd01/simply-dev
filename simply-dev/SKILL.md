---
name: simply-dev
version: 1.0.0
description: >
  Assembles a complete, codebase-aware AI development team for Claude Code
  projects. Use this skill whenever a user wants to set up AI agents for their
  codebase, scaffold a Claude Code team, create sub-agents, generate CLAUDE.md,
  or run /simply-dev. Triggers on phrases like "set up my AI team", "create
  agents for my project", "scaffold claude agents", or any request to organize
  AI development roles around a codebase. Always use this skill — do not
  attempt to create agent files or CLAUDE.md team sections from scratch without
  it.
---

# Simply Dev

Scan a codebase, infer its stack and domain, and assemble a complete AI
development team: an Orchestrator plus domain-specific specialist agents, each
with scoped skills, tool permissions, and routing rules baked into CLAUDE.md.

## Installation

**Project-level** (checked into git, shared with team):
```
.claude/commands/simply-dev.md   ← invoke with /simply-dev
```

**User-level** (applies to all your projects):
```
~/.claude/commands/simply-dev.md
```

Run with `/simply-dev` inside any Claude Code session.

---

## Workflow Overview

```
Phase 1 — Scan       Read manifests, structure, CI, infra, docs, tests
Phase 2 — Propose    Design team roster, present for approval, wait
Phase 3 — Write      Create agent + skill files after approval
Phase 4 — Update     Append Team section to CLAUDE.md
```

Phases 3 and 4 only run after explicit user approval. Never write files
speculatively.

---

## Phase 1 — Codebase Scan

Read and analyze everything listed in
`@references/scan-targets.md`

From the scan, determine:
1. **Primary language(s) and runtime**
2. **Architecture type** — frontend SPA, backend API, fullstack, monorepo,
   ML/data pipeline, scientific library, CLI, embedded, cloud-native, etc.
3. **Domain** — fintech, biotech, DevOps tooling, e-commerce, etc. Infer from
   library names, folder names, README language
4. **Existing tooling** — test framework, linter, formatter, bundler, ORM,
   cloud provider, etc.
5. **Gaps** — missing tests, CI, type checking → signals which agents are
   most needed

---

## Phase 2 — Team Proposal

Every team includes:
- **One Orchestrator** — mandatory entry point, coordinates all agents,
  owns CLAUDE.md, never writes code directly
- **Role-specific specialists** — only include roles with clear surface area
  in this codebase

For the full agent roster and selection criteria, read:
`@references/agent-roster.md`

For each selected agent, define:
- Role name and one-line purpose
- Owned areas (directories, file types, concerns)
- Key skills (specific to this codebase's actual dependencies)
- Tool permissions (explicit allowed commands)
- Scope boundaries (what they must never do)

### Proposal Format

Output exactly this and then STOP. Do not write any files yet.

```
## Proposed Team for [Project Name]

### Detected Stack
[2-3 line summary]

### Detected Domain
[Domain and subdomains]

### Team Roster

#### 🧭 Orchestrator
Purpose: ...
Coordinates: [list all agents below]

#### [Emoji] [Role Name]
Purpose: ...
Owns: ...
Skills: ...
Permissions: ...
Never: ...

[repeat for each agent]

### What will be created
- .claude/agents/orchestrator.md
- .claude/agents/[role-slug].md   × [N]
- .claude/skills/[skill-slug].md  × [N]
- CLAUDE.md → ## Team section appended

Reply with:
  ✅  "approved"                         — write everything
  ✏️  "change [agent]: [what to change]" — tweak then write
  ❌  "cancel"                           — abort
```

---

## Phase 3 — Write Files (after "approved" only)

For file templates and exact formats, read:
`@references/file-templates.md`

> **Critical — Orchestrator tools:**
> - Must include `Agent` — without it delegation is silently impossible
> - Must NOT include `Edit` — having edit access gives the LLM an easy path
>   to implement inline instead of delegating, undermining the whole team
> - Correct tools list: `Read, Glob, Grep, Agent`
>
> **Critical — Agent invocation:** When spawning a specialist, `subagent_type`
> must match the agent's frontmatter `name:` field exactly (e.g. "RL Algorithm
> Engineer"), NOT the file slug (e.g. `rl-algorithm-engineer`). Wrong value
> silently falls back to the general-purpose agent. Always derive the name
> from the agent's `name:` frontmatter field, not the filename.

Order of writes:
1. `.claude/agents/orchestrator.md`
2. `.claude/agents/[role-slug].md` for each specialist
3. `.claude/skills/[skill-slug].md` for each distinct skill
4. `CLAUDE.md` — append `## Team` section (see template in file-templates.md)

### Guardrails

- If `CLAUDE.md` already exists with content outside `## Team` → ask before
  touching anything outside that section
- If `.claude/agents/` already has files → list them, ask to overwrite/skip/merge
- Never invent dependencies — only reference libraries in scanned manifests
- Keep each agent file under 120 lines — split overflow into skill files
  referenced via `@.claude/skills/[name].md`
- If a framework version is ambiguous → add `# TODO: verify` in that agent's
  Stack & Skills section

---

## Changelog

### v1.0.0
- Initial release
- Phases 1–4: scan, propose, write, update CLAUDE.md
- Orchestrator with unconditional entry point rule and delegation gate
- Per-agent skill files
- Guardrails: no speculative writes, no invented dependencies
