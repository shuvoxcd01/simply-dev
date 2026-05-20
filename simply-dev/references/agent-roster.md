# Agent Roster

Selection criteria and emoji conventions for each possible agent role.
Only include agents with clear surface area in the scanned codebase.
When in doubt, omit — a focused small team beats a bloated one.

## Always Include

| Role | Emoji | Include when |
|---|---|---|
| Orchestrator | 🧭 | Always — every team has exactly one |
| QA / Test Engineer | 🧪 | Always — mandatory if test coverage is low or absent |

## Frontend

| Role | Emoji | Include when |
|---|---|---|
| Frontend Developer | 🎨 | React, Vue, Svelte, Angular, or any UI framework present |
| Mobile Developer | 📱 | iOS (Swift/ObjC), Android (Kotlin/Java), React Native, Flutter, Expo |
| Design Systems Engineer | 🖼️ | Storybook, component library, design tokens, or `ui/` package present |

## Backend

| Role | Emoji | Include when |
|---|---|---|
| Backend Developer | ⚙️ | Server, REST/GraphQL API, or service layer present |
| Fullstack Developer | 🔀 | Small project where frontend/backend aren't clearly split |
| Database Engineer | 🗄️ | Complex schema, migrations, query optimization, multiple DB types |
| API / Integration Engineer | 🔌 | Heavy third-party API integration, webhooks, SDK development |

## Infrastructure & Platform

| Role | Emoji | Include when |
|---|---|---|
| DevOps / Platform Engineer | 🚀 | Docker, CI/CD pipelines, Kubernetes, or infra-as-code present |
| Cloud Architect | ☁️ | AWS/GCP/Azure SDK usage, cloud resource config, or multi-service infra |
| Security Engineer | 🔒 | Auth systems, cryptography, financial data, PII handling, compliance signals |

## Data & ML

| Role | Emoji | Include when |
|---|---|---|
| Data Engineer | 🔧 | ETL pipelines, data warehouses, Spark, Airflow, dbt, Kafka present |
| ML Engineer | 🤖 | Model training/inference, PyTorch, TensorFlow, scikit-learn, ONNX present |
| Research Engineer | 🔬 | Experimental code, Jupyter notebooks, novel algorithm development |
| Domain Scientist | 🧬 | Scientific library with domain logic — bio, physics, quant finance, climate |

## Docs & Quality

| Role | Emoji | Include when |
|---|---|---|
| Technical Writer | 📝 | Public-facing docs folder, SDK, or developer-facing API present |

---

## Agent Sizing Guidelines

- **Small codebase** (1 lang, 1 concern): Orchestrator + 1-2 specialists
- **Medium codebase** (fullstack or single-domain service): 3-5 agents
- **Large codebase** (monorepo, multi-service, ML+infra): up to 8 agents

Never create an agent speculatively. If you're unsure whether a role has
enough surface area, skip it and note it in the proposal as "not included —
add via /simply-dev if needed."

---

## Layered Skill Model

Every agent, no matter how domain-specific, inherits canonical base skills
before applying domain-specific ones. Never omit base skills for specialist
agents — an RL Algorithm Engineer who doesn't follow Python best practices
or testing standards is a liability, not an asset.

```
Base layer (always):
  .claude/skills/base/software-engineering-standards.md  → all agents
  .claude/skills/base/code-review-standards.md           → all agents
  .claude/skills/base/git-standards.md                   → all agents
  .claude/skills/base/python-best-practices.md           → all Python agents
  .claude/skills/base/testing-standards.md               → all code-writing agents

Domain layer (project-specific):
  .claude/skills/[domain-skill].md                       → relevant agents only
```

A domain-specific agent without base skills is more capable in its domain
but less reliable as a software engineer. The layered model ensures both.

## Skill Inheritance by Role

Assign skills precisely — never give an agent skills for work outside its
responsibility. Testing-standards belongs to QA, not to implementation agents.
An RL engineer who is told to follow testing-standards may start writing tests
instead of delegating that to QA.

| Role | software-engineering-standards | code-review-standards | git-standards | python-best-practices | testing-standards |
|---|---|---|---|---|---|
| Orchestrator | ✅ | ✅ | ✅ | ❌ | ❌ |
| Implementation agents (backend, algorithm, ML, etc.) | ✅ | ✅ | ✅ | ✅ if Python | ❌ |
| QA / Test Engineer | ✅ | ✅ | ✅ | ✅ if Python | ✅ |
| Research / Science agents | ✅ | ❌ | ✅ | ✅ if Python | ❌ |
| DevOps / Platform / Cloud | ✅ | ✅ | ✅ | ✅ if scripting-heavy | ❌ |
| Technical Writer | ✅ | ❌ | ✅ | ❌ | ❌ |
| Domain Scientist | ✅ | ✅ | ✅ | ✅ if Python | ❌ |

Rule of thumb:
- `testing-standards` → QA agent only, never implementation agents
- `code-review-standards` → agents that review or produce code for review
- `python-best-practices` → only if Python is in the agent's stack
- Research agents skip `code-review-standards` — their code is exploratory,
  not production; reviewing it to production standards creates wrong incentives
