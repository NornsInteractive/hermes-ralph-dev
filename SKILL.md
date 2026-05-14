---
name: ralph-dev
description: Automate software delivery with ralph-claude-code. Give me a feature request or bug report, I will prepare the required files, start Ralph, and notify you when it finishes. I do not write business code myself.
version: 3.2.0
platforms: [macos, linux]
metadata:
  hermes:
    tags: [development, automation, claude-code, ralph]
    category: devops
    requires_toolsets: [terminal]
---

# Ralph Automated Development Skill

## Core constraints

- Hermes does exactly four things in this skill: gather requirements, prepare files, run commands, and send notifications
- Hermes never writes business code and never edits application source files
- All code writing is done by `ralph` + Claude Code
- If the user asks Hermes to write code directly, reply with "That part should be handled by Ralph" and write the requirement into `fix_plan.md`

---

## Mode detection

After receiving a message, determine the mode first and then follow the matching flow:

- Contains words like `bug`, `error`, `failed`, `failure`, `wrong`, `broken`, `fix` -> **Fix mode**
- Contains words like `progress`, `done yet`, `finished`, `status`, `how is it going` -> **Status query mode**
- Otherwise -> **New development mode**

---

## New development mode

### Step 1: Collect information

If the user was not specific enough, ask these questions in one message:

- Project path? Default to the current directory
- What feature should be built?
- What is the tech stack? Language, framework, database. If the project already has a useful `CLAUDE.md`, this can be skipped

### Step 2: Check whether Ralph is installed

```bash
which ralph
```

If it is not installed, ask the user to run the installation manually or confirm whether Hermes should guide the installation:

```bash
git clone https://github.com/frankbria/ralph-claude-code.git
cd ralph-claude-code && ./install.sh
# On macOS you also need:
brew install tmux coreutils jq
```

### Step 3: Check whether the project is initialized

```bash
ls {project_path}/.ralph/ 2>/dev/null
```

- If it does not exist, initialize:

  ```bash
  cd {project_path} && ralph-enable
  ```

- If it already exists, skip initialization and continue to Step 3b

### Step 3b: Check `.ralphrc` tool-permission settings

Before starting Ralph, inspect whether `{project_path}/.ralphrc` contains both of these lines:

```bash
grep '^ALLOWED_TOOLS="\\*"' {project_path}/.ralphrc 2>/dev/null
grep '^CLAUDE_ALLOWED_TOOLS="\\*"' {project_path}/.ralphrc 2>/dev/null
```

**Decision rules**

- If both values are already `*`, continue to Step 4
- If either value is missing or not `*`, Hermes must prompt the user first:

  ```text
  I detected that {project_path}/.ralphrc does not contain:
  - ALLOWED_TOOLS="*"
  - CLAUDE_ALLOWED_TOOLS="*"

  If these are not set to `*`, Ralph / Claude Code may be interrupted by tool-permission restrictions during execution.
  Do you want me to change them to `*` before continuing?
  ```

Do not start Ralph until the user confirms.

### Step 4: Update `CLAUDE.md`

**Important `CLAUDE.md` size limit**

Linux has a hard command-line argument limit of about 128 KB. When Claude Code starts, Ralph may pass the content of `CLAUDE.md` as an argument. If the file is too large, Ralph can crash with `Argument list too long`.

Because of that:

- Keep `CLAUDE.md` under 50 KB
- Put detailed feature descriptions, schema notes, and API definitions under `docs/`

**Recommended `docs/` structure**

```text
docs/
├── project.md          # Project overview, background, goals
├── tech-stack.md       # Tech-stack notes
│
├── modules/            # Detailed module specs
│   └── {module}.md
│
├── database/
│   ├── schema.md       # Schema overview
│   └── tables/
│       └── {table}.md  # Per-table details
│
├── api/
│   ├── overview.md     # Shared API conventions
│   └── endpoints/
│       └── {module}.md
│
├── deploy/
│   ├── setup.md        # Environment setup
│   └── services.md     # Service startup notes
│
├── skills/             # Stage-specific guidance files
│   ├── design.md
│   ├── development.md
│   ├── testing.md
│   ├── review.md
│   ├── deploy.md
│   └── refactor.md
│
└── changelog/
    ├── done.md
    └── {YYYY-MM}/
        └── {MMDD}.md
```

If a `fix_plan.md` task depends on detailed docs, include the file paths explicitly so Claude can load them on demand:

```markdown
- [ ] Implement the user registration API. See docs/api/endpoints/auth.md and docs/database/tables/users.md
```

Read the existing `CLAUDE.md`, if any, and inspect the size:

```bash
cat {project_path}/CLAUDE.md 2>/dev/null
du -sh {project_path}/CLAUDE.md 2>/dev/null
```

**Decision rules**

- If `CLAUDE.md` does not exist, create it with only the project basics:

  ```markdown
  # Project: {project_name}

  ## Project description
  {Short summary from the user, just a few sentences}

  ## Tech stack
  {Tech stack}

  ## Directory layout
  src/            # Source code
  docs/           # Detailed documentation
  tests/          # Tests

  ## Development rules
  - Ensure tests pass before committing
  - Read detailed feature notes from the matching files under docs/
  - After each completed code change, commit immediately if the project is a git repository
  - After each completed task, append a record to docs/changelog/done.md in the format `- [date] task description`

  ## Skill usage rules
  Before each stage, check whether the matching file exists under docs/skills/ and follow it if present:
  - Design stage: docs/skills/design.md
  - Development stage: docs/skills/development.md
  - Testing stage: docs/skills/testing.md
  - Review stage: docs/skills/review.md
  - Deploy stage: docs/skills/deploy.md
  - Refactor stage: docs/skills/refactor.md
  ```

- If `CLAUDE.md` already exists and contains real content, check the size. If it is over 50 KB, tell the user to move detailed content into `docs/`. Only append a short background note for the new feature:

  ```markdown

  ## New feature background ({date})
  {One or two short sentences. Put details in docs/modules/{module}.md}
  ```

- If `CLAUDE.md` exists but is empty or only contains placeholder content, overwrite it

If the user provides detailed module notes, schema details, or API specs, route them into the matching `docs/` subdirectories instead of `CLAUDE.md`.

### Step 5: Update `fix_plan.md`

Read the existing file:

```bash
cat {project_path}/.ralph/fix_plan.md
```

**Decision rules**

- If it is still the default template and contains placeholders such as `Implement core features` or `Add test coverage`, overwrite it while preserving the section layout and the `Completed` block:

  ```markdown
  # Ralph Fix Plan

  ## High Priority
  - [ ] {specific task 1}
  - [ ] {specific task 2}

  ## Medium Priority
  - [ ] Add unit tests and edge-case coverage for the feature above
  - [ ] Update the README

  ## Low Priority
  - [ ] Code cleanup

  ## Completed
  - [x] Ralph enabled for this project

  ## Notes
  - Update the checkbox state immediately when a task is completed
  - After all tasks are done, write the completion time and summary to .ralph/DONE
  ```

- If it already contains real work items, append the new tasks at the end of the `High Priority` block without changing existing content:

  ```bash
  # macOS / Linux compatible
  if [[ "$OSTYPE" == "darwin"* ]]; then
    sed -i '' '/^## Medium Priority/i\\
  - [ ] {new_task_1}\\
  - [ ] {new_task_2}\\
  ' {project_path}/.ralph/fix_plan.md
  else
    sed -i '/^## Medium Priority/i - [ ] {new_task_1}\n- [ ] {new_task_2}' {project_path}/.ralph/fix_plan.md
  fi
  ```

**Task wording must be specific**

- Good: `Implement POST /api/register to accept email and password, write to the users table, and return 201`
- Bad: `Implement registration`

### Step 5b: Ensure the completion-signal instructions exist in `PROMPT.md`

Check whether the signal is already present and avoid appending duplicates:

```bash
if ! grep -q "EXIT_SIGNAL" {project_path}/.ralph/PROMPT.md 2>/dev/null; then
  cat >> {project_path}/.ralph/PROMPT.md << 'EOF'

## Exit conditions (must be followed)
- Output `EXIT_SIGNAL: true` only when one of the following is true:
  1. All `- [ ]` tasks in fix_plan.md are completed
  2. All remaining `- [ ]` tasks require manual user action before work can continue
- If any `- [ ]` task can still be completed autonomously, STATUS must remain `IN_PROGRESS`, not `COMPLETE`
- If the next step requires the user, set STATUS to `BLOCKED` and explain the required action in `RECOMMENDATION`

## Completion signal
After each task is completed:
1. Update the matching item in fix_plan.md from `- [ ]` to `- [x]`
2. Append the completed item to docs/changelog/done.md

After all tasks are completed:
1. Output `EXIT_SIGNAL: true` in the `RALPH_STATUS` block
2. Write the completion time and completed tasks to `.ralph/DONE`
EOF
fi
```

### Step 6: Start Ralph and monitor completion

```bash
cd {project_path}
ralph --monitor &

(
  PROJECT_PATH="{project_path}"
  MAX_WAIT=14400
  ELAPSED=0
  EXIT_REASON=""

  while true; do
    if [ -f "$PROJECT_PATH/.ralph/DONE" ]; then
      EXIT_REASON="RALPH_DONE"
      break
    fi

    if ! tmux has-session -t ralph 2>/dev/null; then
      if [ -f "$PROJECT_PATH/.ralph/DONE" ]; then
        EXIT_REASON="RALPH_DONE"
      else
        EXIT_REASON="RALPH_CRASHED"
      fi
      break
    fi

    if [ "$ELAPSED" -ge "$MAX_WAIT" ]; then
      EXIT_REASON="RALPH_TIMEOUT"
      break
    fi

    sleep 30
    ELAPSED=$((ELAPSED + 30))
  done

  echo "$EXIT_REASON"
) &
```

Tell the user:

- Ralph has started and development is running automatically
- No action is required right now
- To inspect live progress, run `tmux attach -t ralph` and detach with `Ctrl+B D`

### Step 7: Notify the user when Ralph finishes

After the monitor exits, send the appropriate notification based on `EXIT_REASON`.

**Normal completion: `RALPH_DONE`**

```bash
SUMMARY=$(cat {project_path}/.ralph/DONE 2>/dev/null || echo "Tasks completed")
DONE_TASKS=$(grep '^\- \[x\]' {project_path}/.ralph/fix_plan.md)
```

Notification:

```text
✅ Ralph has completed the development work.

{SUMMARY}

Completed tasks:
{DONE_TASKS}

Suggested tests to run now:
{test suggestions inferred from the tasks, one per line}

If anything looks wrong, send me the error output and I will queue a fix.
```

**Crash: `RALPH_CRASHED`**

```bash
LAST_LOG=$(tail -20 {project_path}/.ralph/logs/ralph.log 2>/dev/null || echo "No log available")
DONE=$(grep -c '^\- \[x\]' {project_path}/.ralph/fix_plan.md 2>/dev/null || echo 0)
TODO=$(grep -c '^\- \[ \]' {project_path}/.ralph/fix_plan.md 2>/dev/null || echo 0)
```

Notification:

```text
⚠️ Ralph exited unexpectedly before finishing.

Completed: {DONE}
Remaining: {TODO}

Last log lines:
{LAST_LOG}

Send me the log above and I will help investigate the failure.
```

**Timeout: `RALPH_TIMEOUT`**

```bash
DONE=$(grep -c '^\- \[x\]' {project_path}/.ralph/fix_plan.md 2>/dev/null || echo 0)
TODO=$(grep -c '^\- \[ \]' {project_path}/.ralph/fix_plan.md 2>/dev/null || echo 0)
```

Notification:

```text
⏰ Ralph has been running for more than 4 hours without finishing.

Current progress: completed {DONE}, remaining {TODO}

Suggested next step: run `tmux attach -t ralph` to see where it is stuck,
or send me the current state and I will help.
```

---

## Fix mode

### Step 1: Collect bug details

If the user did not provide enough detail, ask in one message:

- Project path?
- Full error output or observed behavior?
- Expected behavior?

### Step 2: Environment checks

Before killing Ralph, verify the environment:

```bash
if ! which ralph > /dev/null 2>&1; then
  # Ask the user to install Ralph first, using the same installation flow as in New development mode Step 2
fi

if [ ! -d "{project_path}/.ralph" ]; then
  # Tell the user this project has not been initialized with Ralph yet
  # Tell them to run the new development flow first
fi

grep '^ALLOWED_TOOLS="\\*"' "{project_path}/.ralphrc" 2>/dev/null
grep '^CLAUDE_ALLOWED_TOOLS="\\*"' "{project_path}/.ralphrc" 2>/dev/null
# If either value is missing or not *, ask the user whether Hermes should change both values to *
# Do not restart Ralph until the user confirms
```

### Step 3: Stop the current Ralph session

```bash
tmux kill-session -t ralph 2>/dev/null || true
```

### Step 4: Append the fix request to `fix_plan.md`

Append only. Never overwrite existing tasks. Insert at the top of the `High Priority` section:

```bash
if [[ "$OSTYPE" == "darwin"* ]]; then
  sed -i '' '/^## High Priority/a\\
- [ ] Fix: {observed_behavior}. Error: {error_message}. Expected: {expected_behavior}\\
- [ ] Re-run the full test suite after the fix and confirm it passes\\
' {project_path}/.ralph/fix_plan.md
else
  sed -i '/^## High Priority/a - [ ] Fix: {observed_behavior}. Error: {error_message}. Expected: {expected_behavior}\n- [ ] Re-run the full test suite after the fix and confirm it passes' {project_path}/.ralph/fix_plan.md
fi
```

Hermes does not diagnose the bug or write the solution plan. Hermes only records the user's report faithfully and lets Ralph investigate and fix it.

### Step 5: Restart Ralph

Use the same flow as New development mode Step 6.

Tell the user:

```text
The bug has been added to the task list. Ralph is working on the fix and I will notify you when it finishes.
```

---

## Status query mode

Confirm the project path first. Prefer conversation context if available. If not, ask the user.

```bash
DONE=$(grep -c '^\- \[x\]' "$PROJECT_PATH/.ralph/fix_plan.md" 2>/dev/null || echo 0)
TODO=$(grep -c '^\- \[ \]' "$PROJECT_PATH/.ralph/fix_plan.md" 2>/dev/null || echo 0)

tmux has-session -t ralph 2>/dev/null && STATUS="running" || STATUS="not running"

LATEST=$(tail -3 "$PROJECT_PATH/.ralph/logs/ralph.log" 2>/dev/null || echo "No log available")
```

Reply format:

```text
📊 Progress: {DONE} tasks completed, {TODO} remaining
⚙️ Ralph status: {STATUS}
📝 Latest activity: {LATEST}
```

---

## Pitfalls

- **`Argument list too long`**: `CLAUDE.md` is too large and Ralph crashes on startup. Check the size with `du -sh CLAUDE.md` and move detailed notes into `docs/`. Keep `CLAUDE.md` under 50 KB.
- **No `.ralph/DONE` after completion**: `PROMPT.md` is missing the completion-signal instruction. Re-apply Step 5b and restart Ralph.
- **Ralph loops because the task is vague**: if the log repeats the same action, stop Ralph, ask the user for more detail, update `fix_plan.md`, and restart.
- **Permission denied**: first check whether `.ralphrc` already contains `ALLOWED_TOOLS="*"` and `CLAUDE_ALLOWED_TOOLS="*"`. If not, ask the user whether Hermes should change both values to `*`; otherwise Ralph / Claude Code can easily be interrupted by permission restrictions. After updating `.ralphrc`, run `ralph --reset-session` and restart.
- **5-hour API limit**: Ralph usually waits automatically. Tell the user no action is needed and it should recover after about an hour.
- **`PROMPT.md` fails validation**: `validate_ralph_integrity()` requires `.ralph/PROMPT.md` to exist. Do not delete or rename it.
- **Multiple concurrent projects**: `tmux kill-session -t ralph` can kill the wrong session if multiple projects share the default `ralph` session name. Prefer running one project at a time, or rename the session manually with `tmux rename-session -t ralph ralph-{project-name}` and update the monitoring command accordingly.

## Verification

Signs that Ralph is working normally:

- `tmux list-sessions` shows a `ralph` session
- The number of `[x]` entries in `.ralph/fix_plan.md` keeps increasing
- `.ralph/logs/ralph.log` continues updating
- New business-source files appear in the target project. Hermes should only read status files under `.ralph/`, not application source files
