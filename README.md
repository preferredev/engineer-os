# EngineerOS

**Open-source AI agent skills that make general AI agents behave like
disciplined, production-grade engineers.**

EngineerOS is a library of [Agent Skills](https://www.anthropic.com/news/skills)
— portable, model-agnostic skill folders that drop into AI coding tools (Claude
Code, Cursor, and other agents that support the skills format). Each skill is a
serious engineering *behavior module*, not a vague prompt: concrete rules,
checklists, good/bad examples, and eval tasks that make the agent perform better
on the first try.

> Design goal: optimize for **correctness, verification, maintainability, and
> avoiding AI slop**. No generic advice; every rule is concrete and checkable.

## Core (the shared foundation)

Every skill is built on a shared **core ruleset** — the non-negotiable
engineering discipline that applies no matter the task (read before writing,
verify don't recall, minimal diff, prove it works, no slop). This is *not* a
separate skill; it's the baseline every skill inherits.

To keep each skill fully self-contained and portable, the core is **copied into
every skill** at `<skill>/core/`:

- `core/principles.md` — role baseline, operating principles, validation
  baseline, and default response shape.
- `core/workflow.md` — the required understand → investigate → plan → implement
  → verify → report loop.
- `core/anti-slop-rules.md` — hard bans + the operational definition of
  "clean code".
- `core/quality-checklist.md` — pre-flight, in-flight, and pre-submit checklists.

A skill's own `SKILL.md` and `references/` extend the core for a specific
domain; they never override it. If guidance conflicts, core wins unless the
skill explicitly says otherwise.

## Skills

| Skill | What it does | Trigger it when you... |
|-------|--------------|------------------------|
| [`software-engineer`](skills/software-engineer/) | Makes the agent implement, modify, debug, and review code like a careful senior engineer: read first, change little, break nothing, prove it works. | implement, change code, fix a bug, refactor, write tests, review a diff |
| [`ai-engineer`](skills/ai-engineer/) | Makes the agent design, build, and improve production AI/LLM applications with eval-first, simplest-architecture-that-passes discipline. | build an AI feature, design an agent, decide on RAG, write prompts, set up evals, pick a model |

Each skill folder contains a `SKILL.md` (the entry point with YAML
frontmatter), a `core/` directory (the shared foundation), and a `references/`
directory with detailed domain docs that the agent loads only when needed
(progressive disclosure).

## Repository structure

```
engineer-os/
├── README.md
├── LICENSE
├── .gitignore
└── skills/
    ├── software-engineer/
    │   ├── SKILL.md
    │   ├── core/
    │   │   ├── principles.md
    │   │   ├── workflow.md
    │   │   ├── anti-slop-rules.md
    │   │   └── quality-checklist.md
    │   └── references/
    │       ├── implementation-checklist.md
    │       ├── anti-slop-rules.md
    │       ├── code-quality-rubric.md
    │       ├── validation-workflow.md
    │       ├── examples.md
    │       └── eval-tasks.md
    └── ai-engineer/
        ├── SKILL.md
        ├── core/
        │   ├── principles.md
        │   ├── workflow.md
        │   ├── anti-slop-rules.md
        │   └── quality-checklist.md
        └── references/
            ├── ai-system-quality-checklist.md
            ├── architecture-decision-guide.md
            ├── eval-first-workflow.md
            ├── prompt-system-checklist.md
            ├── rag-decision-checklist.md
            ├── agent-design-checklist.md
            ├── ai-observability-checklist.md
            ├── anti-ai-slop-rules.md
            ├── examples.md
            └── eval-tasks.md
```

## Installation & usage

Skills are plain folders — install by placing the skill directory where your
agent looks for skills. The `core/` folder travels with the skill, so each one
is self-contained.

### Claude Code
Copy a skill into your skills directory (personal or project), e.g.:
```bash
# project-scoped
mkdir -p .claude/skills
cp -r skills/software-engineer .claude/skills/
```
The agent reads `SKILL.md` frontmatter and activates the skill when the
description matches your request.

### Cursor / other agents
Point your agent's rules/skills configuration at the skill folder, or paste the
relevant `SKILL.md` into your project rules. The `core/` and `references/` files
are loaded on demand by the skill itself.

### Use the whole library
Copy the entire `skills/` directory so every skill is available; the agent picks
the right one based on the task.

## Design principles

- **Shared core, self-contained skills.** One foundational ruleset, copied into
  every skill so each folder is portable on its own.
- **Behavior modules, not prompts.** Each skill defines a role, a required
  workflow, hard rules, and validation requirements.
- **Eval-driven.** Every skill ships trigger tests and functional tests so you
  can verify it activates correctly and behaves correctly.
- **Anti-slop by construction.** Explicit bans on placeholders, invented APIs,
  unverified claims, broad rewrites, and vibe-based work.
- **Progressive disclosure.** `SKILL.md` stays focused; depth lives in `core/`
  and `references/`.

## Contributing

Contributions welcome. When adding or changing a skill:

1. Keep `SKILL.md` focused; put depth in `core/` (shared) and `references/`
   (domain-specific).
2. Include the `core/` folder so the skill is self-contained — copy it from an
   existing skill and keep it identical across skills.
3. Frontmatter `name` must be kebab-case and match the folder name; no XML
   angle brackets in frontmatter; keep `description` under 1024 characters and
   include both what it does and when to use it (with trigger phrases).
4. Add/extend `eval-tasks.md` with trigger and functional tests, and make sure
   the skill passes its own release gate.
5. No generic advice — every rule must be concrete and checkable.

## License

[MIT](LICENSE) © 2026 preferredev
