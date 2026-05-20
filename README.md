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

The result is an Orchestrator that coordinates specialists — a frontend dev, backend dev, QA engineer, ML researcher, cloud architect, domain scientist, and more — each scoped to their domain with explicit tool permissions and routing rules that enforce delegation.

---

## What it generates

```
.claude/
├── agents/
│   ├── orchestrator.md       ← mandatory entry point, never writes code
│   ├── [role-slug].md        ← one per specialist
│   └── ...
└── skills/
    ├── [skill-slug].md       ← focused skill references per agent
    └── ...
CLAUDE.md                     ← Team section appended with routing table
```

---

## Agents it can create

| Role | When included |
|---|---|
| 🧭 Orchestrator | Always — mandatory entry point for every task |
| 🧪 QA / Test Engineer | Always — mandatory if coverage is low |
| 🎨 Frontend Developer | React, Vue, Svelte, Angular, or any UI framework |
| ⚙️ Backend Developer | Server, REST/GraphQL API, or service layer |
| 📱 Mobile Developer | iOS, Android, React Native, Flutter |
| 🚀 DevOps / Platform Engineer | Docker, CI/CD, Kubernetes, infra-as-code |
| ☁️ Cloud Architect | AWS/GCP/Azure SDK or cloud resource config |
| 🔒 Security Engineer | Auth, cryptography, PII, compliance signals |
| 🔧 Data Engineer | ETL, Spark, Airflow, dbt, Kafka |
| 🤖 ML Engineer | PyTorch, TensorFlow, scikit-learn, ONNX |
| 🔬 Research Engineer | Jupyter notebooks, experimental code, novel algorithms |
| 🧬 Domain Scientist | Scientific libraries — bio, physics, quant, climate |
| 📝 Technical Writer | Public docs, SDK, developer-facing API |

---

## How it works

```
Phase 1 — Scan      Read manifests, directory structure, CI, infra, docs, tests
Phase 2 — Propose   Design team roster, present for your approval, wait
Phase 3 — Write     Create agent + skill files after approval
Phase 4 — Update    Append Team section to CLAUDE.md
```

Phases 3 and 4 only run after you explicitly approve. Nothing is written speculatively.

### Delegation enforcement

The generated `CLAUDE.md` includes an unconditional routing policy:

- The **Orchestrator is always called first** for any implement/fix/refactor/test task — no exceptions, even when the domain mapping is obvious
- **Specialists handle execution** — the Orchestrator decomposes and delegates, never implements directly
- **Inline only for read-only tasks** — explain, summarize, review bypass the Orchestrator entirely
- If no specialist exists for a domain, the model says so explicitly rather than silently self-assigning

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

---

## Skill structure

```
simply-dev/
├── SKILL.md                        ← entry point, always in context
└── references/
    ├── scan-targets.md             ← what to read during Phase 1
    ├── agent-roster.md             ← role selection criteria
    └── file-templates.md           ← exact formats for generated files
```

Reference files are loaded progressively — only pulled into context during the phase that needs them, keeping the main prompt lean.

---

## Compatibility

| Tool | Status |
|---|---|
| Claude Code | ✅ Full support |
| OpenCode | 🔄 Partial — core logic works, agent frontmatter format differs |

OpenCode compatibility (native `mode:`, `permissions:`, and `AGENTS.md` format) is planned for v1.1.0.

---

## Roadmap

- **v1.1.0** — OpenCode-native output format
- **v1.2.0** — Per-agent memory files with Orchestrator-confirmed writes
- **v1.3.0** — MCP-aware tool assignment from `.claude/settings.json`

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
