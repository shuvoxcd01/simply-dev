---
name: simply-dev
version: 1.1.0
description: >
  Assembles a complete, codebase-aware AI development team for Claude Code
  projects. Use this skill whenever a user wants to set up AI agents for their
  codebase, scaffold a Claude Code team, create sub-agents, generate CLAUDE.md,
  or run /simply-dev. Triggers on phrases like "set up my AI team", "create
  agents for my project", "scaffold claude agents", "add a researcher agent",
  "update my agents", or any request to organize AI development roles around
  a codebase. Always use this skill — do not attempt to create agent files or
  CLAUDE.md team sections from scratch without it.
---

# Simply Dev

Scan a codebase, infer its stack and domain, and assemble a complete AI
development team: an Orchestrator plus domain-specific specialist agents, each
inheriting canonical base skills plus domain-specific skills, with tool
permissions and routing rules baked into CLAUDE.md.

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

## Step 0 — Parse Invocation Mode

Before anything else, determine how simply-dev was called:

### Mode A — Bare (`/simply-dev` with no arguments)
Check whether `.claude/agents/` already has agent files:
- **No agents exist** → run full flow: Phases 1 → 2 → 3 → 4
- **Agents exist** → run diff mode: Phases 1 → 2D → 3D → 4

### Mode B — With arguments (`/simply-dev <instruction>`)
Parse the instruction:

| Intent | Examples | Action |
|---|---|---|
| Create agent | "create a researcher agent", "add a devops agent" | → Phase C |
| Update agent | "update the rl-algorithm-engineer", "add skills to X" | → Phase U |
| Delete agent | "remove the technical writer agent" | → Phase Del |
| Unclear | "add something for experiments", "we need more coverage" | → ask clarifying questions |

**Clarifying questions for underspecified requests** — ask these before
proceeding, one at a time:
1. What is this agent's primary responsibility in one sentence?
2. Which directories or file types will it own?
3. Does it have a specific domain or technology focus? (e.g. PyTorch, AWS,
   bioinformatics)
4. Are there existing agents whose scope it overlaps? If so, how should
   ownership be divided?

Never proceed to write files until intent is clear.

---

## Phase 1 — Codebase Scan (full)

Read and analyze everything listed in `@references/scan-targets.md`

From the scan, determine:
1. **Primary language(s) and runtime**
2. **Architecture type** — frontend SPA, backend API, fullstack, monorepo,
   ML/data pipeline, scientific library, CLI, embedded, cloud-native, etc.
3. **Domain** — fintech, biotech, DevOps tooling, e-commerce, etc.
4. **Existing tooling** — test framework, linter, formatter, bundler, ORM,
   cloud provider, etc.
5. **Gaps** — missing tests, CI, type checking → signals which agents are
   most needed

---

## Phase 2 — Team Proposal (full, no existing agents)

Every team includes:
- **One Orchestrator** — mandatory entry point, coordinates all agents,
  owns CLAUDE.md, never writes code directly
- **Role-specific specialists** — only include roles with clear surface area

For agent roster and selection criteria, read: `@references/agent-roster.md`

For each selected agent, define:
- Role name and one-line purpose
- Owned areas (directories, file types, concerns)
- Base skills inherited (from `.claude/skills/base/`)
- Domain-specific skills (generated for this project)
- Tool permissions and scope boundaries

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
Base skills: [from .claude/skills/base/ — list which apply]
Domain skills: [project-specific skills to generate]
Permissions: ...
Never: ...

[repeat for each agent]

### What will be created
- .claude/skills/base/           ← canonical base skills (5 files)
- .claude/agents/orchestrator.md
- .claude/agents/[role-slug].md  × [N]
- .claude/skills/[skill-slug].md × [N]
- CLAUDE.md → ## Team section appended

Reply with:
  ✅  "approved"                         — write everything
  ✏️  "change [agent]: [what to change]" — tweak then write
  ❌  "cancel"                           — abort
```

---

## Phase 2D — Diff Proposal (agents already exist)

Compare the current codebase scan against existing agent definitions:

1. **New coverage gaps** — directories or modules with no owning agent
2. **Stale ownership** — agents that own paths that no longer exist
3. **New dependencies** — libraries in manifests not reflected in any agent's
   skills
4. **Outdated base skills** — `.claude/skills/base/` files older than bundled
   versions

Output a diff proposal:

```
## Team Update for [Project Name]

### What changed in the codebase
[bullet list of detected changes]

### Proposed updates

#### New agents needed
[list with rationale, or "none"]

#### Agents to update
[agent name] — [what needs to change and why]

#### Agents to retire
[agent name] — [reason: owned path removed, merged into another agent, etc.]

#### Base skills to update
[list outdated files, or "all up to date"]

Reply with:
  ✅  "approved" — apply all updates
  ✏️  "change [item]: [what to change]" — tweak then apply
  ❌  "cancel" — abort
```

---

## Phase C — Create Single Agent

1. Confirm intent via clarifying questions (see Step 0)
2. Scan only the relevant directories for the new agent's scope
3. Propose the single agent definition (same format as Team Roster entry)
4. On approval: write agent file, generate domain skills, update CLAUDE.md
   routing table and Team section

---

## Phase U — Update Single Agent

1. Read the existing agent file
2. Scan its owned directories for changes
3. Propose a diff of what would change (added skills, updated scope, etc.)
4. On approval: update agent file and any affected skill files

---

## Phase Del — Delete Agent

1. Read the existing agent file and CLAUDE.md routing table
2. Check for ownership gaps: who takes over this agent's directories?
3. Propose: delete agent, reassign ownership to named agent(s), update CLAUDE.md
4. On approval: delete agent file, update CLAUDE.md

---

## Phase 3 — Write Files (after "approved" only)

For file templates and exact formats, read: `@references/file-templates.md`

> **Critical — Orchestrator tools:**
> - Must include `Agent` — without it delegation is silently impossible
> - Must NOT include `Edit` — having edit access gives the LLM an easy path
>   to implement inline instead of delegating
> - Correct tools list: `Read, Glob, Grep, Agent`
>
> **Critical — Agent invocation:** When spawning a specialist, `subagent_type`
> must match the agent's frontmatter `name:` field exactly (e.g. "RL Algorithm
> Engineer"), NOT the file slug (e.g. `rl-algorithm-engineer`). Wrong value
> silently falls back to the general-purpose agent.

Order of writes:
1. `.claude/skills/base/` — copy all 5 canonical base skills from
   `@references/base-skills.md`. Skip any file already present unless
   an update was approved in Phase 2D.
2. `.claude/agents/orchestrator.md`
3. `.claude/agents/[role-slug].md` for each specialist
4. `.claude/skills/[skill-slug].md` for each domain-specific skill
5. `CLAUDE.md` — append `## Team` section

### Base Skill Assignment

Assign base skills precisely by role. Never give an agent skills outside
its responsibility — it creates ambiguity about ownership and incentivizes
agents to do work that belongs to another agent.

The full assignment table is in `@references/agent-roster.md`. The critical
rules:

- `testing-standards` → **QA agent only**. Never implementation agents.
  An RL engineer assigned testing-standards may start writing tests instead
  of delegating to QA.
- `code-review-standards` → implementation agents + QA + Orchestrator.
  Not research agents — their code is exploratory, not production.
- `python-best-practices` → only if Python is in that agent's stack.
- `software-engineering-standards` + `git-standards` → every agent without
  exception.

Always read `@references/agent-roster.md` to determine the exact set for
each role before writing any agent file.

### Guardrails

- If `CLAUDE.md` already exists with content outside `## Team` → ask before
  touching anything outside that section
- If `.claude/agents/` already has files → list them, ask to overwrite/skip/merge
- Never invent dependencies — only reference libraries in scanned manifests
- Keep each agent file under 120 lines — split overflow into domain skill files
- If a framework version is ambiguous → add `# TODO: verify`

---

## Phase 3D — Apply Diff (diff mode approved)

Same as Phase 3 but scoped to only the changes proposed in Phase 2D:
- Write new agent files
- Update changed agent files (preserve unchanged sections)
- Delete retired agent files
- Update base skill files if approved
- Update CLAUDE.md routing table and Team section to reflect all changes

---

## Phase 4 — Update CLAUDE.md

Append (or update) the `## Team` section per the template in
`@references/file-templates.md`.

The CLAUDE.md Team section must include the drift detection rule so the
Orchestrator surfaces coverage gaps at runtime — see file-templates.md for
the exact wording.

---

## Changelog

### v1.1.0
- Canonical base skills: 5 skill files bundled, copied to `.claude/skills/base/`
  on first run
- Layered skill model: base (canonical) → domain-specific (project)
- Role-based skill assignment: skills assigned by responsibility, not blanket
  inheritance. testing-standards → QA only; code-review → implementation +
  QA + Orchestrator; research agents get a lighter set to preserve exploratory
  intent
- Three interaction modes: Solo (plain Claude), Direct (specialist by name),
  Team (Orchestrator-coordinated). simply-dev no longer hijacks every session —
  the team is opt-in, not mandatory
- CLAUDE.md routing section rewritten as a routing guide, not an enforcement
  policy. Delegation gate moved into Orchestrator definition only — fires when
  Orchestrator is invoked, not on every task
- Diff mode: subsequent runs compare codebase against existing agents and
  propose only what changed
- Argument parsing: `/simply-dev <instruction>` for create/update/delete
- Clarifying questions for underspecified agent requests
- Drift detection rule added to Orchestrator template and CLAUDE.md
- Phase Del: clean agent deletion with ownership reassignment

### v1.0.0
- Initial release
- Phases 1–4: scan, propose, write, update CLAUDE.md
- Orchestrator with unconditional entry point rule and delegation gate
- Per-agent skill files
- Guardrails: no speculative writes, no invented dependencies
