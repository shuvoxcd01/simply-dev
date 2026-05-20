# simply-dev

Assemble a full AI development team for your Claude Code project — one `/simply-dev` command scans your codebase and generates a domain-aware Orchestrator, specialist agents, and routing rules tailored to your stack.

---

## What it does

Most AI coding setups use a single agent that does everything. `simply-dev` builds a **team** instead:

- Scans your codebase — manifests, directory structure, CI, infra, tests, docs
- Infers your stack, architecture, and domain
- Proposes a set of specialist agents matched to what it actually finds
- Waits for your approval before writing anything
- Generates agent definitions, skill files, and a `CLAUDE.md` routing table

The result is an Orchestrator that coordinates specialists — a frontend dev, backend dev, QA engineer, ML researcher, cloud architect, domain scientist, and more — each scoped to their domain with explicit tool permissions and routing rules.

---

## What it generates

```
.claude/
├── agents/
│   ├── orchestrator.md       ← mandatory entry point, never writes code
│   ├── [role-slug].md        ← one per specialist
│   └── ...
└── skills/
    ├── base/
    │   ├── software-engineering-standards.md
    │   ├── code-review-standards.md
    │   ├── git-standards.md
    │   ├── python-best-practices.md
    │   └── testing-standards.md
    └── [skill-slug].md       ← domain-specific skills per agent
CLAUDE.md                     ← Team section appended with routing table
```

Base skills are copied once on first run and shared across all agents. Domain-specific skills are generated per project.

---

## Agents it can create

| Role | When included |
|---|---|
| 🧭 Orchestrator | Always — mandatory entry point for every task |
| 🧪 QA / Test Engineer | Always — mandatory if coverage is low or absent |
| 🎨 Frontend Developer | React, Vue, Svelte, Angular, or any UI framework |
| 🔀 Fullstack Developer | Small project where frontend/backend aren't clearly split |
| ⚙️ Backend Developer | Server, REST/GraphQL API, or service layer |
| 🗄️ Database Engineer | Complex schema, migrations, query optimization, multiple DB types |
| 🔌 API / Integration Engineer | Heavy third-party API integration, webhooks, SDK development |
| 📱 Mobile Developer | iOS, Android, React Native, Flutter, Expo |
| 🖼️ Design Systems Engineer | Storybook, component library, design tokens, or `ui/` package |
| 🚀 DevOps / Platform Engineer | Docker, CI/CD, Kubernetes, infra-as-code |
| ☁️ Cloud Architect | AWS/GCP/Azure SDK or cloud resource config |
| 🔒 Security Engineer | Auth, cryptography, PII, compliance signals |
| 🔧 Data Engineer | ETL, Spark, Airflow, dbt, Kafka |
| 🤖 ML Engineer | PyTorch, TensorFlow, scikit-learn, ONNX |
| 🔬 Research Engineer | Jupyter notebooks, experimental code, novel algorithms |
| 🧬 Domain Scientist | Scientific libraries — bio, physics, quant, climate |
| 📝 Technical Writer | Public docs, SDK, developer-facing API |

Team size scales with codebase complexity: 2–3 agents for small projects, up to 8 for large monorepos.

---

## How it works

### Invocation modes

**Bare (`/simply-dev`):**
- No agents exist → full flow: scan, propose, write
- Agents already exist → diff mode: compare codebase against existing agents, propose only what changed

**With arguments (`/simply-dev <instruction>`):**

| Intent | Examples |
|---|---|
| Create agent | `create a researcher agent`, `add a devops agent` |
| Update agent | `update the rl-algorithm-engineer`, `add skills to X` |
| Delete agent | `remove the technical writer agent` |

### Phases

```
Phase 1 — Scan      Read manifests, directory structure, CI, infra, docs, tests
Phase 2 — Propose   Design team roster, present for your approval, wait
Phase 3 — Write     Create agent + skill files after approval
Phase 4 — Update    Append Team section to CLAUDE.md
```

Phases 3 and 4 only run after you explicitly approve. Nothing is written speculatively.

Diff mode (when agents already exist) runs the same flow but scoped to only what changed: new coverage gaps, stale ownership, outdated base skills.

### Three interaction modes

The generated `CLAUDE.md` documents three ways to use the team — the team is opt-in, not mandatory:

- **Solo** — use plain Claude directly, no agents involved
- **Direct** — invoke a specialist by name for focused, single-domain work
- **Team** — invoke the Orchestrator, which decomposes and delegates to specialists

The Orchestrator is only called when you use Team mode. Delegation is enforced within the Orchestrator's own definition, not as a session-wide gate.

### Layered skill model

Every agent inherits canonical base skills before applying domain-specific ones:

```
Base layer (from .claude/skills/base/):
  software-engineering-standards  → all agents
  git-standards                   → all agents
  code-review-standards           → implementation agents, QA, Orchestrator
  python-best-practices           → agents whose stack includes Python
  testing-standards               → QA agent only

Domain layer (project-specific):
  .claude/skills/[domain-skill].md → relevant agents only
```

Skills are assigned by responsibility — `testing-standards` goes to QA only; giving it to an implementation agent risks blurring ownership.

---

## Installation

Clone the repo:

```bash
git clone https://github.com/shuvoxcd01/simply-dev /tmp/simply-dev
```

**Project-level** (checked into git, shared with your team):

```bash
mkdir -p .claude/skills
cp -r /tmp/simply-dev/simply-dev .claude/skills/
```

**User-level** (applies to all your projects):

```bash
mkdir -p ~/.claude/skills
cp -r /tmp/simply-dev/simply-dev ~/.claude/skills/
```

---

## Usage

Start a Claude Code session in your project and either:

- Type naturally: `set up my AI team` or `run simply-dev`
- Or invoke directly: `/simply-dev`

simply-dev will scan your codebase, propose a team, and wait for your response:

- `approved` — write all files as proposed
- `change [agent]: [what to change]` — tweak before writing
- `cancel` — abort

To update an existing team after codebase changes, run `/simply-dev` again — it detects existing agents and runs in diff mode automatically.

---

## Skill structure

```
simply-dev/
├── SKILL.md                        ← entry point, always in context
└── references/
    ├── scan-targets.md             ← what to read during Phase 1
    ├── agent-roster.md             ← role selection criteria and skill assignment table
    ├── base-skills.md              ← canonical base skill content (copied on first run)
    └── file-templates.md           ← exact formats for generated files
```

Reference files are loaded progressively — only pulled into context during the phase that needs them, keeping the main prompt lean.

---

## Compatibility

| Tool | Status |
|---|---|
| Claude Code | ✅ Full support |
| OpenCode | 🔄 Partial — core logic works, agent frontmatter format differs |

OpenCode compatibility (native `mode:`, `permissions:`, and `AGENTS.md` format) is planned.

---

## Changelog

### v1.1.0
- **Canonical base skills**: 5 skill files bundled and copied to `.claude/skills/base/` on first run
- **Layered skill model**: base (canonical) → domain-specific (project)
- **Role-based skill assignment**: skills assigned by responsibility — `testing-standards` → QA only; research agents get a lighter set to preserve exploratory intent
- **Three interaction modes**: Solo, Direct, Team — simply-dev no longer hijacks every session; the team is opt-in
- **Routing guide**: CLAUDE.md routing section rewritten as a guide, not an enforcement policy; delegation gate moved into Orchestrator definition only
- **Diff mode**: subsequent runs compare codebase against existing agents and propose only what changed
- **Argument parsing**: `/simply-dev <instruction>` for create / update / delete
- **Clarifying questions**: asked for underspecified agent requests before writing anything
- **Drift detection**: Orchestrator surfaces coverage gaps at runtime
- **Phase Del**: clean agent deletion with ownership reassignment
- **New roles**: Design Systems Engineer, Fullstack Developer, Database Engineer, API / Integration Engineer

### v1.0.0
- Initial release
- Phases 1–4: scan, propose, write, update CLAUDE.md
- Orchestrator with delegation gate
- Per-agent domain skill files
- Guardrails: no speculative writes, no invented dependencies

---

## Contributing

Contributions welcome. Most useful areas:

- **New agent roles** — missing stack coverage (Rust, Elixir, embedded, etc.)
- **OpenCode compatibility** — native frontmatter format and `AGENTS.md` output
- **Stack detection** — better signals for domain inference
- **Real-world examples** — share your generated team in Discussions

Please open an issue before a large PR so we can align on approach.

---

## License

MIT
