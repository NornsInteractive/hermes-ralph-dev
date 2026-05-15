[English](./README.md) | [简体中文](./README.zh-CN.md)

# hermes-ralph-dev

一个面向通用 agent 的开发编排 skill：你负责收集需求、准备 Ralph / Claude Code 所需文件、启动 `ralph` 并回报状态。若由 `Claude Code` 执行，它只负责审计、拆分任务和创建子 agent，真正的代码编写全部交给子 agent。适用于 `Codex`、`Claude Code`、`Cursor` 等常见 agent。

已支持的 agent 形态：`Codex`、`Claude Code`、`Cursor`，以及其他能遵循结构化指令并执行终端命令的 agent。

## 仓库目标

- 把“提需求 -> 生成 Ralph 配置 -> 启动 Claude Code 开发 -> 回报进度”这条链路标准化
- 明确当前 agent 与 Ralph 的职责边界，避免 agent 越权直接改业务代码
- 提供可复用模板与完整示例，方便你直接开源、二次修改或给团队内部复用

## 这个 Agent 做什么

- 收集需求或 bug 信息
- 检查 `ralph` 是否可用、项目是否已初始化
- 写入或更新 `CLAUDE.md`
- 写入或更新 `.ralph/fix_plan.md`
- 幂等补充 `.ralph/PROMPT.md` 的完成信号规范
- 启动 `ralph --monitor`
- 监控完成、超时、异常退出并通知用户

## 这个 Agent 不做什么

- 不写任何业务代码
- 不直接修改源码文件
- 不替 Ralph 分析 bug 根因或设计修复方案

## 仓库内容

```text
.
├── SKILL.zh-CN.md                # 中文版通用 agent skill
├── SKILL.md                      # 英文版通用 agent skill
├── templates/                    # 可复用项目模板
├── examples/
│   ├── basic-web-app/            # 中文示例项目
│   └── basic-web-app-en/         # 英文示例项目
├── README.md                     # 英文 README
├── README.zh-CN.md               # 中文 README
└── LICENSE
```

## 快速使用

1. 安装 `ralph-claude-code` 以及它依赖的工具。
2. 把 [SKILL.zh-CN.md](./SKILL.zh-CN.md) 作为系统 prompt、skill 或规则文件导入你的 agent。
3. 给 agent 发需求，例如：

```text
在 /Users/me/projects/todo-api 里新增一个任务管理模块：
- Node.js + Express + PostgreSQL
- 需要任务增删改查
- 要有 JWT 登录
```

4. 当前 agent 会检查环境、准备文档和 `.ralph/` 文件，然后启动 `ralph`。
5. Ralph 调起 Claude Code 进行审计、拆分任务并协调子 agent；当前 agent 负责汇报状态与结果。

## 推荐配套方式

- `CLAUDE.md` 只保留项目简介、技术栈、规则和 docs 索引
- 详细模块说明、表结构、接口约定全部写到 `docs/`
- `fix_plan.md` 只写具体可执行任务，不写模糊目标
- `PROMPT.md` 里加入完成信号，保证当前 agent 能识别 Ralph 已结束
- 如果当前执行者是 `Claude Code`，它只做审计和任务拆分，不直接改业务代码，开发任务交给它创建的子 agent
- 启动 Ralph 前，确保目标项目的 `.ralphrc` 中已配置 `ALLOWED_TOOLS="*"` 和 `CLAUDE_ALLOWED_TOOLS="*"`，否则 Claude Code 很容易因为权限限制中途中断
- Skill 在每次启动前都应检查 `.ralphrc`；如果缺少任一项，当前 agent 需要先提示用户是否改成 `*`

## 多语言文件

- 中文 skill：[SKILL.zh-CN.md](./SKILL.zh-CN.md)
- 英文 skill：[SKILL.md](./SKILL.md)
- 中文示例：[examples/basic-web-app](./examples/basic-web-app)
- 英文示例：[examples/basic-web-app-en](./examples/basic-web-app-en)

## 示例说明

示例项目展示了通用 agent 初始化 Ralph 工作区后的典型结构，包括：

- `CLAUDE.md`
- `.ralph/fix_plan.md`
- `.ralph/PROMPT.md`
- `docs/project.md`
- `docs/modules/*.md`
- `docs/database/*.md`
- `docs/api/*.md`
- `docs/skills/*.md`

这些示例主要用于说明输出结构，不要求可以直接运行。

## 适合开源发布的点

- Skill 行为边界清晰
- 示例项目完整，便于理解输出产物
- 模板与示例分离，既能复制也能演示
- MIT 许可证，便于他人复用

## 后续可选增强

- 增加双语模板
- 增加多技术栈示例（Node / Python / Go）
- 增加 `.github` issue / PR 模板
- 增加将 `templates/` 快速复制到目标项目的引导脚本

## License

MIT
