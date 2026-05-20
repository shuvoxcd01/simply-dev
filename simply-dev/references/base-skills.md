# Base Skills

Canonical skill templates bundled with simply-dev. These are copied to
`.claude/skills/base/` on first run and `@`-imported by every agent regardless
of specialization. They encode industry best practices that apply universally —
every engineer on every team should follow them.

On subsequent `/simply-dev` runs, these are compared against the project copies
and an update is offered if the bundled versions are newer.

---

## python-best-practices.md

```markdown
# Skill: Python Best Practices

## When to apply
Any time you write, edit, or review Python code.

## Code Quality
- Follow PEP 8. Use a formatter (black, ruff) — never argue about style manually
- Type-annotate all function signatures and return types
- Use dataclasses or Pydantic models for structured data — never raw dicts for
  public interfaces
- Prefer explicit over implicit — avoid `*args/**kwargs` in public APIs unless
  genuinely variadic
- Keep functions under 30 lines. If it needs a comment to explain what it does,
  it should be a named function instead

## Imports
- Absolute imports only in library/package code
- Group: stdlib → third-party → local, separated by blank lines
- Never import * except in `__init__.py` re-exports

## Error Handling
- Catch specific exceptions, never bare `except:`
- Use custom exception classes for domain errors
- Always clean up resources with context managers (`with`) or `try/finally`
- Log exceptions with context before re-raising

## Dependencies
- Pin versions in lock files (`requirements.txt`, `poetry.lock`, `uv.lock`)
- Separate dev/test dependencies from runtime
- Never import optional dependencies at module level — guard with try/except

## Pitfalls
- Mutable default arguments (`def f(x=[])`) — always use `None` and assign inside
- Late binding in closures — use default argument capture when needed
- Modifying a list/dict while iterating over it
- Using `assert` for runtime validation — it's stripped with `-O` flag
```

---

## software-engineering-standards.md

```markdown
# Skill: Software Engineering Standards

## When to apply
Any implementation, fix, or refactor task regardless of language or domain.

## Design Principles
- **Single Responsibility** — each module, class, and function does one thing
- **Open/Closed** — extend behavior without modifying existing code
- **Dependency Inversion** — depend on abstractions, not concretions
- **DRY** — if you write it twice, extract it; if three times, it's a module
- **YAGNI** — don't build for imagined future requirements

## Code Structure
- New functionality → new file or module, not appended to an existing one
- Public interfaces (APIs, class methods) are stable contracts — breaking them
  requires a deprecation path
- Internal implementation details are private — prefix with `_` in Python,
  keep unexported in Go/Rust, etc.
- Configuration belongs in config files or environment variables, never
  hardcoded in source

## Naming
- Names should explain intent, not implementation: `compute_advantage()` not
  `calc_adv_v2_final()`
- Booleans: `is_`, `has_`, `should_`, `can_` prefixes
- Collections: plural nouns (`agents`, `rewards`, `layers`)
- Avoid abbreviations unless universally understood in the domain (e.g. `lr`
  for learning rate in ML is fine; `lr` for "left-right" is not)

## Changesets
- Each commit does one logical thing
- Never mix refactoring with feature changes in the same commit
- If a fix requires a refactor, do the refactor first in a separate commit

## Pitfalls
- God objects / god modules that accumulate unrelated responsibilities
- Premature optimization before profiling
- Magic numbers — always named constants with explanatory names
- Commented-out code — delete it, git has history
```

---

## testing-standards.md

```markdown
# Skill: Testing Standards

## When to apply
Any time you write, fix, or review tests.

## Test Structure
- **Unit tests** — one function/class, no I/O, no network, no filesystem
- **Integration tests** — two or more real components interacting
- **End-to-end tests** — full system path, sparingly
- Follow AAA: Arrange → Act → Assert. One assertion concept per test.

## Naming
- Test names describe behavior, not implementation:
  `test_advantage_is_zero_when_returns_equal_baseline` not `test_compute_adv`
- Test files mirror source structure: `src/agents/ppo.py` →
  `tests/agents/test_ppo.py`

## Coverage
- New code ships with tests — no exceptions for "it's just a helper"
- Bug fixes ship with a regression test that would have caught the bug
- Aim for behavioral coverage, not line coverage — 100% lines with trivial
  tests is worse than 70% with meaningful ones

## Test Quality
- Tests must be deterministic — seed all random number generators
- Tests must be independent — no shared mutable state between tests
- Fast tests (< 1s each) in the default suite; slow tests in a separate suite
- Never test implementation details — test behavior through public interfaces

## Mocking
- Mock at the boundary (network, filesystem, time), not inside business logic
- Prefer fakes (simple implementations) over mocks (behavior verification)
  for complex dependencies

## Pitfalls
- Tests that pass because they never actually assert anything
- Testing the mock instead of the real behavior
- Brittle tests that break on unrelated refactors
- Skipping tests with `pytest.mark.skip` without a linked issue
```

---

## code-review-standards.md

```markdown
# Skill: Code Review Standards

## When to apply
Before marking any implementation task complete. Self-review before
delegating back to the Orchestrator.

## Review Checklist
- [ ] Does the code do what was asked?
- [ ] Are edge cases handled (empty input, zero, None, large values)?
- [ ] Are errors handled and meaningful?
- [ ] Is there test coverage for the new behavior?
- [ ] Are there any hardcoded values that should be configurable?
- [ ] Does the naming clearly communicate intent?
- [ ] Is the diff minimal — does it change only what's necessary?
- [ ] Does it introduce any new dependencies? Are they justified?
- [ ] Any security concerns (user input, credentials, file paths)?

## What to flag
- Logic that's correct but will confuse the next reader → suggest rename/comment
- Missing tests for non-trivial paths → block until added
- Scope creep (changes unrelated to the task) → split into separate PR
- Performance concerns in hot paths → raise but don't block unless severe

## What NOT to flag
- Style preferences already handled by the formatter
- Theoretical future problems with no concrete risk
- Rewrites of working code with no behavioral improvement
```

---

## git-standards.md

```markdown
# Skill: Git Standards

## When to apply
Any time you create, stage, or describe a change to the codebase.

## Commits
- Atomic: one logical change per commit
- Never mix: refactor + feature, or fix + cleanup
- Message format: `<type>(<scope>): <short description>`
  Types: feat, fix, refactor, test, docs, chore, perf
  Example: `fix(ppo): correct advantage normalization when std is zero`

## Branches
- Never commit directly to main/master
- Branch names: `<type>/<short-description>` e.g. `fix/ppo-advantage-norm`

## What never goes in git
- Secrets, API keys, credentials — use environment variables
- Generated files that can be reproduced (build artifacts, `.pyc`, `__pycache__`)
- Large binary files — use git-lfs or external storage
- Personal config (`.env`, editor settings) — add to `.gitignore`

## Pitfalls
- `git add .` without reviewing the diff first
- Force-pushing to shared branches
- Committing debug prints, commented-out code, or TODO-only commits
```
