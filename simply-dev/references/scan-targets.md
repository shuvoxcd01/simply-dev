# Scan Targets

Everything to read during Phase 1. Work through each category. Skip gracefully
if a file or directory doesn't exist — absence is itself a signal.

## Dependency & Build Manifests
Read whichever are present:
- `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`
- `pyproject.toml`, `setup.py`, `requirements.txt`, `requirements/*.txt`
- `Cargo.toml`, `go.mod`, `go.sum`
- `Gemfile`, `pom.xml`, `build.gradle`, `build.gradle.kts`
- `*.csproj`, `*.sln` (C#/.NET)
- `composer.json` (PHP)
- `mix.exs` (Elixir)

## Directory Structure
- Top 3 levels of the project tree
- Pay attention to: `src/`, `lib/`, `app/`, `packages/`, `services/`,
  `modules/`, `internal/`, `cmd/`, `api/`, `web/`, `mobile/`
- Monorepo signals: `packages/*/package.json`, `apps/*/`, `workspace` fields

## Existing Claude Config
- `CLAUDE.md`, `CLAUDE.local.md`
- `.claude/agents/` (existing agents — avoid overwriting without asking)
- `.claude/settings.json` (tool permissions, MCP servers)
- `.claude/commands/` (existing slash commands)

## Documentation
- `README.md`, `readme.md`
- `ARCHITECTURE.md`, `CONTRIBUTING.md`, `DEVELOPMENT.md`
- `docs/`, `doc/`, `wiki/` (top level only — don't recurse deeply)

## CI/CD & DevOps
- `.github/workflows/*.yml`
- `.gitlab-ci.yml`, `.circleci/config.yml`, `Jenkinsfile`
- `Dockerfile`, `docker-compose.yml`, `docker-compose*.yml`
- `Makefile`, `Taskfile.yml`, `justfile`

## Infrastructure
- `terraform/`, `pulumi/`, `cdk/`, `infrastructure/`, `infra/`
- `k8s/`, `kubernetes/`, `helm/`, `charts/`
- `.env.example`, `.env.sample` (variable names only — never read actual `.env`)

## Tests
- `test/`, `tests/`, `spec/`, `__tests__/`, `e2e/`, `integration/`
- Top-level test config: `jest.config.*`, `pytest.ini`, `vitest.config.*`,
  `karma.conf.js`, `cypress.config.*`, `.rspec`

## Domain Signals (read selectively — look for patterns not full content)
- Import statements in 2-3 representative source files
- Folder names that imply domain: `trading/`, `genome/`, `billing/`,
  `recommendations/`, `simulation/`, `nlp/`, `vision/`
- Any `DOMAIN.md` or `SCIENCE.md` files
