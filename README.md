[English](./README.md) | [简体中文](./README.zh-CN.md)

# hermes-ralph-dev

A Hermes skill for orchestrating development through Ralph + Claude Code. Hermes collects requirements, prepares the project files Ralph needs, starts `ralph`, tracks progress, and reports results. Hermes does not write business code itself.

## Goals

- Standardize the workflow from requirement intake to Ralph configuration to Claude Code execution to status reporting
- Keep the responsibility boundary between Hermes and Ralph explicit
- Provide reusable templates and complete examples for open-source distribution or internal team reuse

## What Hermes does

- Collect feature requests or bug reports
- Check whether `ralph` is installed and whether the target project is initialized
- Create or update `CLAUDE.md`
- Create or update `.ralph/fix_plan.md`
- Append completion-signal rules to `.ralph/PROMPT.md` in an idempotent way
- Start `ralph --monitor`
- Watch for completion, timeout, or crashes and notify the user

## What Hermes does not do

- Write business code
- Modify application source files directly
- Diagnose the root cause of bugs or design the implementation plan on Ralph's behalf

## Repository layout

```text
.
├── ralph-dev.md                  # Chinese Hermes skill
├── ralph-dev.en.md               # English Hermes skill
├── templates/                    # Reusable project templates
├── examples/
│   ├── basic-web-app/            # Chinese example project
│   └── basic-web-app-en/         # English example project
├── README.md                     # English README
├── README.zh-CN.md               # Chinese README
└── LICENSE
```

## Quick start

1. Install `ralph-claude-code` and its dependencies.
2. Import [ralph-dev.en.md](./ralph-dev.en.md) into your Hermes skill system.
3. Send Hermes a request like this:

```text
Add a task management module to /Users/me/projects/todo-api:
- Node.js + Express + PostgreSQL
- Need task CRUD
- Need JWT login
```

4. Hermes checks the environment, prepares the docs and `.ralph/` files, then starts `ralph`.
5. Ralph invokes Claude Code to implement the work while Hermes reports progress and outcomes.

## Recommended setup

- Keep `CLAUDE.md` short and use it only for project summary, stack, conventions, and doc pointers
- Put module details, schema notes, and API contracts under `docs/`
- Keep `fix_plan.md` concrete and executable, not vague
- Add explicit completion rules to `PROMPT.md` so Hermes can reliably detect when Ralph is done

## Language variants

- Chinese skill: [ralph-dev.md](./ralph-dev.md)
- English skill: [ralph-dev.en.md](./ralph-dev.en.md)
- Chinese example: [examples/basic-web-app](./examples/basic-web-app)
- English example: [examples/basic-web-app-en](./examples/basic-web-app-en)

## Example projects

The example projects show what a Hermes-initialized Ralph workspace looks like, including:

- `CLAUDE.md`
- `.ralph/fix_plan.md`
- `.ralph/PROMPT.md`
- `docs/project.md`
- `docs/modules/*.md`
- `docs/database/*.md`
- `docs/api/*.md`
- `docs/skills/*.md`

They are intended as reference output, not as directly runnable applications.

## Why this repository is open-source friendly

- Clear behavioral boundary for the skill
- Complete examples that make the output structure easy to understand
- Templates and examples are separated, so users can both copy and inspect them
- MIT license for easy reuse

## Possible next enhancements

- Add bilingual templates
- Add multi-stack examples for Node, Python, and Go
- Add `.github` issue and PR templates
- Add a bootstrap script that copies `templates/` into a target project

## License

MIT
