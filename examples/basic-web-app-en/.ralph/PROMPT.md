## Development rules

Before writing code:
1. Read `CLAUDE.md`
2. Read the `docs/` files related to the current task
3. If a matching stage file exists under `docs/skills/`, follow it first

## Completion signal

After each completed task:
1. Update the matching task state in `fix_plan.md`
2. Append the completion record to `docs/changelog/done.md`

After all `fix_plan.md` tasks are completed:
1. Include `EXIT_SIGNAL: true` in the status output
2. Write the completion time, completed tasks, and a short test summary to `.ralph/DONE`
