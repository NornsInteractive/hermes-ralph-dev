---
name: ralph-dev
description: 用 ralph-claude-code 自动化开发流程。给我需求或 bug 描述，你负责配置文件、启动 ralph、汇报结果；Claude Code 只做审计、拆分任务和创建子 agent，真正的业务代码全部由子 agent 完成。适用于 Codex、Claude Code、Cursor 等常见 agent。
version: 3.2.0
platforms: [macos, linux]
metadata:
  tags: [development, automation, claude-code, ralph, codex, cursor, agent]
  category: devops
  requires_toolsets: [terminal]
  compatible_agents: [codex, claude-code, cursor, generic-agent]
---

# 通用 Agent Ralph 自动开发 Skill

适用于 `Codex`、`Claude Code`、`Cursor` 以及其他具备终端能力的常见 agent。全文中的“你”指当前执行该 skill 的 agent。

## 核心约束（任何情况下都必须遵守）

- 你在此 skill 中**只做四件事**：收集需求、配置文件、执行命令、发送通知
- 你**绝对不写任何业务代码**，不修改任何源码文件
- Claude Code 只负责审计、拆分任务、创建子 agent 和验收，不直接写业务代码
- 所有业务代码编写全部由 Claude Code 创建并调度的子 agent 完成
- 遇到用户要求你直接写代码的情况，回复"这部分交给 ralph 完成"，然后把需求写进 fix_plan.md

---

## 模式判断

收到消息后先判断模式，再执行对应流程：

- 含"bug"、"报错"、"失败"、"错误"、"不对"、"有问题"、"修复" → **修复模式**
- 含"进度"、"完成了吗"、"跑完了吗"、"状态"、"怎么样了" → **状态查询模式**
- 其他 → **新开发模式**

---

## 新开发模式

### Step 1：收集信息

用户没说清楚时依次询问（一次问完，不要分多次问）：
- 项目路径？（默认当前目录）
- 要开发什么功能？
- 技术栈？（语言/框架/数据库，如果是现成项目且有 CLAUDE.md 可跳过）

### Step 2：检查 ralph 是否已安装

```bash
which ralph
```

未安装则告知用户手动执行或让用户确认是否自动安装，等待确认后继续：
```bash
git clone https://github.com/frankbria/ralph-claude-code.git
cd ralph-claude-code && ./install.sh
# macOS 额外需要：
brew install tmux coreutils jq
```

### Step 3：检查是否已初始化

```bash
ls {项目路径}/.ralph/ 2>/dev/null
```

- **不存在** → 初始化：
  ```bash
  cd {项目路径} && ralph-enable
  ```

- **已存在** → 跳过初始化，直接进入 Step 3b

### Step 3b：检查 `.ralphrc` 权限配置

在真正启动 ralph 前，必须先检查 `{项目路径}/.ralphrc` 是否包含以下两项：

```bash
grep '^ALLOWED_TOOLS="\\*"' {项目路径}/.ralphrc 2>/dev/null
grep '^CLAUDE_ALLOWED_TOOLS="\\*"' {项目路径}/.ralphrc 2>/dev/null
```

**判断处理方式：**

- **两项都已是 `*`** → 继续进入 Step 4
- **任一项缺失或不是 `*`** → 必须先提示用户：

  ```text
  检测到 {项目路径}/.ralphrc 没有配置：
  - ALLOWED_TOOLS="*"
  - CLAUDE_ALLOWED_TOOLS="*"

  如果不改成 `*`，ralph / claude-code 在执行过程中可能因为工具权限不足而中断。
  是否要先帮你改成 `*` 再继续？
  ```

用户确认后再修改；未确认前**不要启动 ralph**。

### Step 4：更新 CLAUDE.md

**CLAUDE.md 大小限制（重要）：**
Linux 系统对命令行参数有 128KB 的硬限制，claude-code 启动时会把 CLAUDE.md 内容作为参数传入，超过限制会报 `Argument list too long` 导致 ralph 崩溃。因此：

- **CLAUDE.md 只存放项目基本介绍，严格控制在 50KB 以内**
- 具体功能模块说明、数据库字段、接口定义等详细内容全部放到 `docs/` 目录

**docs 目录结构规范：**
```
docs/
├── project.md          # 项目概述、背景、目标
├── tech-stack.md       # 技术栈选型说明
│
├── modules/            # 功能模块详细说明
│   └── {模块名}.md     # 每个模块一个文件
│
├── database/           # 数据库相关
│   ├── schema.md       # 表结构总览
│   └── tables/         # 每张表的字段详细说明
│       └── {表名}.md
│
├── api/                # 接口文档
│   ├── overview.md     # 接口规范、通用说明
│   └── endpoints/      # 每个模块的接口
│       └── {模块名}.md
│
├── deploy/             # 部署相关
│   ├── setup.md        # 环境搭建
│   └── services.md     # 各服务启动说明
│
├── skills/             # 各阶段开发准则（由用户自行填写，claude 按需读取）
│   ├── design.md       # 需求分析/设计阶段准则
│   ├── development.md  # 开发编码阶段准则
│   ├── testing.md      # 测试阶段准则
│   ├── review.md       # 代码审查阶段准则
│   ├── deploy.md       # 部署阶段准则
│   └── refactor.md     # 重构阶段准则
│
└── changelog/          # 开发记录
    ├── done.md         # 已完成功能汇总（claude 每次完成任务后自动追加）
    └── {YYYY-MM}/      # 按月归档
        └── {MMDD}.md
```

fix_plan.md 里的任务如果涉及详细文档，写明路径让 claude 按需读取：
```markdown
- [ ] 实现用户注册接口，详见 docs/api/endpoints/auth.md 和 docs/database/tables/users.md
```

读取现有 CLAUDE.md（如果有）并检查大小：
```bash
cat {项目路径}/CLAUDE.md 2>/dev/null
du -sh {项目路径}/CLAUDE.md 2>/dev/null
```

**判断处理方式：**

- **CLAUDE.md 不存在** → 创建，只写项目基本介绍：
  ```markdown
  # 项目：{项目名称}

  ## 项目描述
  {用户描述，简洁概括，不超过几句话}

  ## 技术栈
  {技术栈}

  ## 目录结构
  src/            # 源码
  docs/           # 详细文档（功能模块/数据库/接口定义等）
  tests/          # 测试

  ## 开发规范
  - 代码提交前确保测试通过
  - 详细功能说明见 docs/ 目录对应文档
  - 每次完成一次代码修改后立即提交 git（先检测是否有 git 仓库，没有则跳过）
  - 每次任务完成后，将完成记录追加到 docs/changelog/done.md，格式：`- [日期] 功能描述`

  ## Skill 使用规范
  在执行对应阶段前，检查 docs/skills/ 目录下是否存在对应 skill 文件，存在则调用，不存在则跳过：
  - 需求分析/设计阶段：调用 docs/skills/design.md
  - 开发编码阶段：调用 docs/skills/development.md
  - 测试阶段：调用 docs/skills/testing.md
  - 代码审查阶段：调用 docs/skills/review.md
  - 部署阶段：调用 docs/skills/deploy.md
  - 重构阶段：调用 docs/skills/refactor.md
  ```

- **CLAUDE.md 已存在且有实质内容** → 检查大小，超过 50KB 则提醒用户把详细内容迁移到 docs/ 目录；只追加新功能的简要背景：
  ```markdown

  ## 新增功能背景（{日期}）
  {一两句话概括，详细说明见 docs/modules/{模块名}.md}
  ```

- **CLAUDE.md 已存在但是空文件或只有占位内容** → 覆盖写入

如果用户提供了详细的功能说明、数据库字段、接口定义，引导写到 docs/ 对应子目录，不要放进 CLAUDE.md。

### Step 5：更新 fix_plan.md

读取现有 fix_plan.md：
```bash
cat {项目路径}/.ralph/fix_plan.md
```

**判断处理方式：**

- **是默认模板**（含 "Implement core features"、"Add test coverage" 等通用占位内容）→ **覆盖**，但保留结构和 Completed 区块：

  ```markdown
  # Ralph Fix Plan

  ## High Priority
  - [ ] {具体任务1}
  - [ ] {具体任务2}

  ## Medium Priority
  - [ ] 为上述功能编写单元测试，覆盖正常流程和边界情况
  - [ ] 更新 README

  ## Low Priority
  - [ ] 代码清理

  ## Completed
  - [x] 项目已启用 Ralph

  ## Notes
  - 每完成一项立即更新本文件的勾选状态
  - 完成所有任务后在 .ralph/DONE 写入完成时间和任务摘要
  ```

- **已有真实任务内容** → **追加**新任务到 High Priority 区块末尾，不动已有内容：

  ```bash
  # macOS/Linux 兼容写法
  if [[ "$OSTYPE" == "darwin"* ]]; then
    sed -i '' '/^## Medium Priority/i\\
  - [ ] {新任务1}\\
  - [ ] {新任务2}\\
  ' {项目路径}/.ralph/fix_plan.md
  else
    sed -i '/^## Medium Priority/i - [ ] {新任务1}\n- [ ] {新任务2}' {项目路径}/.ralph/fix_plan.md
  fi
  ```

**任务描述规范（必须具体）：**
- ✅ 好：`实现 POST /api/register，接收 email/password，写入 users 表，返回 201`
- ❌ 坏：`实现注册功能`

### Step 5b：确认 PROMPT.md 完成信号指令（幂等）

检查 PROMPT.md 是否已包含完成信号，**避免重复追加**：

```bash
if ! grep -q "EXIT_SIGNAL" {项目路径}/.ralph/PROMPT.md 2>/dev/null; then
  cat >> {项目路径}/.ralph/PROMPT.md << 'EOF'

## 退出条件（严格遵守）
- 满足以下任一条件才能输出 EXIT_SIGNAL: true：
  1. fix_plan.md 中所有 [ ] 任务全部完成
  2. 剩余 [ ] 任务全部需要用户手动操作才能继续（无法自主完成）
- 只要还有任何一个 [ ] 任务可以自主完成，STATUS 必须是 IN_PROGRESS，不能是 COMPLETE
- 需要用户手动确认的任务，STATUS 设为 BLOCKED，并在 RECOMMENDATION 里说明需要用户做什么

## 完成信号
每完成一个任务后：
1. 将 fix_plan.md 中对应任务从 `- [ ]` 更新为 `- [x]`
2. 将完成记录追加到 docs/changelog/done.md

所有任务完成后：
1. 在 RALPH_STATUS 块中输出 EXIT_SIGNAL: true
2. 在 .ralph/DONE 文件写入完成时间和已完成任务列表
EOF
fi
```

### Step 6：启动 ralph 并监控完成

```bash
cd {项目路径}
ralph --monitor &

# 后台监控，带超时和崩溃检测
(
  PROJECT_PATH="{项目路径}"
  MAX_WAIT=14400   # 最长等待 4 小时（秒）
  ELAPSED=0
  EXIT_REASON=""

  while true; do
    # 优先检查完成文件
    if [ -f "$PROJECT_PATH/.ralph/DONE" ]; then
      EXIT_REASON="RALPH_DONE"
      break
    fi

    # 进程已退出——区分正常完成 vs 崩溃
    if ! tmux has-session -t ralph 2>/dev/null; then
      if [ -f "$PROJECT_PATH/.ralph/DONE" ]; then
        EXIT_REASON="RALPH_DONE"
      else
        EXIT_REASON="RALPH_CRASHED"
      fi
      break
    fi

    # 超时检测
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

告知用户：
- ralph 已启动，正在自动开发，**无需任何操作**
- 完成后会主动通知
- 需要查看实时进度：`tmux attach -t ralph`（`Ctrl+B D` 挂起回到后台）

### Step 7：完成后主动通知

监控进程返回后，根据 `EXIT_REASON` 发送对应通知：

**正常完成（RALPH_DONE）：**
```bash
SUMMARY=$(cat {项目路径}/.ralph/DONE 2>/dev/null || echo "任务已完成")
DONE_TASKS=$(grep '^\- \[x\]' {项目路径}/.ralph/fix_plan.md)
```
通知内容：
```
✅ Ralph 开发完成！

{SUMMARY}

已完成的任务：
{DONE_TASKS}

建议现在测试：
{根据已完成任务推断的测试点，每条一行}

如果发现问题，把报错信息告诉我，我帮你安排修复。
```

**异常退出（RALPH_CRASHED）：**
```bash
LAST_LOG=$(tail -20 {项目路径}/.ralph/logs/ralph.log 2>/dev/null || echo "无日志")
DONE=$(grep -c '^\- \[x\]' {项目路径}/.ralph/fix_plan.md 2>/dev/null || echo 0)
TODO=$(grep -c '^\- \[ \]' {项目路径}/.ralph/fix_plan.md 2>/dev/null || echo 0)
```
通知内容：
```
⚠️ Ralph 异常退出，任务未完成

已完成 {DONE} 个，剩余 {TODO} 个

最后日志：
{LAST_LOG}

请把上面日志告诉我，我帮你排查原因。
```

**超时（RALPH_TIMEOUT）：**
```bash
DONE=$(grep -c '^\- \[x\]' {项目路径}/.ralph/fix_plan.md 2>/dev/null || echo 0)
TODO=$(grep -c '^\- \[ \]' {项目路径}/.ralph/fix_plan.md 2>/dev/null || echo 0)
```
通知内容：
```
⏰ Ralph 已运行超过 4 小时仍未完成

当前进度：已完成 {DONE} 个，剩余 {TODO} 个

建议执行 tmux attach -t ralph 查看卡在哪一步，
或把当前状态告诉我，我帮你处理。
```

---

## 修复模式

### Step 1：收集 bug 信息

用户没提供时一次性询问：
- 项目路径？
- 完整报错信息或错误现象？
- 预期的正确行为？

### Step 2：环境检查

在 kill ralph 之前，先验证环境：

```bash
# 检查 ralph 是否已安装
if ! which ralph > /dev/null 2>&1; then
  # 提示用户先安装，流程同新开发模式 Step 2，安装完成后继续
fi

# 检查项目是否已初始化
if [ ! -d "{项目路径}/.ralph" ]; then
  # 提示：该项目从未启用 ralph，请先走新开发模式完成初始化
  # 终止修复流程
fi

# 检查 .ralphrc 是否放开工具权限
grep '^ALLOWED_TOOLS="\\*"' "{项目路径}/.ralphrc" 2>/dev/null
grep '^CLAUDE_ALLOWED_TOOLS="\\*"' "{项目路径}/.ralphrc" 2>/dev/null
# 如果任一项缺失或不是 *，先提示用户是否修改成 *，未确认前不要继续重启 ralph
```

### Step 3：停止当前 ralph

```bash
tmux kill-session -t ralph 2>/dev/null || true
```

### Step 4：追加修复任务到 fix_plan.md

**只追加，绝不覆盖已有内容。** 在 High Priority 区块顶部插入：

```bash
if [[ "$OSTYPE" == "darwin"* ]]; then
  sed -i '' '/^## High Priority/a\\
- [ ] 修复：{问题现象}。报错：{错误信息}。预期：{正确行为}\\
- [ ] 修复后重新运行全部测试确认通过\\
' {项目路径}/.ralph/fix_plan.md
else
  sed -i '/^## High Priority/a - [ ] 修复：{问题现象}。报错：{错误信息}。预期：{正确行为}\n- [ ] 修复后重新运行全部测试确认通过' {项目路径}/.ralph/fix_plan.md
fi
```

你**不定位 bug 根因，不写修复方案**，只把用户描述如实写入，让 ralph 去定位修复。收集 bug 信息时可判断信息是否完整，不完整则追问。

### Step 5：重新启动 ralph

同新开发模式 Step 6，重新启动并监控。

告知用户：
```
已将 bug 加入任务列表，ralph 正在修复，完成后通知你。
```

---

## 状态查询模式

**先确认项目路径**（优先从对话上下文取，取不到则询问用户）：

```bash
# PROJECT_PATH 从上下文中获取，若无则询问：
# "请告诉我项目路径？"

DONE=$(grep -c '^\- \[x\]' "$PROJECT_PATH/.ralph/fix_plan.md" 2>/dev/null || echo 0)
TODO=$(grep -c '^\- \[ \]' "$PROJECT_PATH/.ralph/fix_plan.md" 2>/dev/null || echo 0)

# ralph 是否在运行
tmux has-session -t ralph 2>/dev/null && STATUS="运行中" || STATUS="未运行"

# 最新日志
LATEST=$(tail -3 "$PROJECT_PATH/.ralph/logs/ralph.log" 2>/dev/null || echo "无日志")
```

回复格式：
```
📊 进度：已完成 {DONE} 个任务，剩余 {TODO} 个
⚙️ Ralph 状态：{STATUS}
📝 最近动作：{LATEST}
```

---

## Pitfalls

- **`Argument list too long` 报错**：CLAUDE.md 超过 128KB 导致 ralph 启动崩溃。检查文件大小 `du -sh CLAUDE.md`，把详细内容迁移到 `docs/` 子目录，CLAUDE.md 只保留项目基本介绍，控制在 50KB 以内。
- **ralph 完成后没有 .ralph/DONE 文件**：说明 PROMPT.md 里没有写入 DONE 的指令。检查 Step 5b 是否成功追加了完成信号指令，然后重新启动 ralph。
- **任务描述模糊导致 ralph 卡循环**：ralph 日志里出现重复相同操作时，停止 ralph，让用户补充任务细节，更新 fix_plan.md 后重启。
- **权限报错（permission denied）**：优先检查 `.ralphrc` 是否已配置 `ALLOWED_TOOLS="*"` 和 `CLAUDE_ALLOWED_TOOLS="*"`。如果没有，先征求用户确认后改成 `*`，否则 ralph / claude-code 很容易因权限不足中断；修改后执行 `ralph --reset-session` 再重启。
- **5小时 API 限制**：ralph 自动等待，告知用户无需操作，约1小时后自动恢复。
- **PROMPT.md 被 ralph 验证报错**：`validate_ralph_integrity()` 要求 `.ralph/PROMPT.md` 必须存在，不能删除或重命名。
- **多项目并发**：多个项目同时用 ralph 时，`tmux kill-session -t ralph` 会误杀其他项目的 session。目前 ralph 默认 session 名固定为 `ralph`，暂无内置隔离方案，建议同一时间只跑一个项目，或手动 rename tmux session（`tmux rename-session -t ralph ralph-{项目名}`）并相应修改监控命令。

## Verification

ralph 正常运行的标志：
- `tmux list-sessions` 能看到 ralph session
- `.ralph/fix_plan.md` 中 `[x]` 数量在增加
- `.ralph/logs/ralph.log` 持续更新
- 项目目录出现新的业务源码文件（你只读 `.ralph/` 下的状态文件，不读业务源码）
