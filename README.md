# hermes-ralph-dev

一个给 Hermes 使用的开发编排 skill：Hermes 负责收集需求、准备 Ralph / Claude Code 所需文件、启动 `ralph` 并回报状态，真正的代码编写全部交给 `ralph-claude-code`。

English summary: this repository contains a Hermes skill plus templates and examples for delegating software tasks to Ralph + Claude Code without letting Hermes write business code itself.

## 仓库目标

- 把“提需求 -> 生成 Ralph 配置 -> 启动 Claude Code 开发 -> 回报进度”这条链路标准化
- 明确 Hermes 与 Ralph 的职责边界，避免 Hermes 越权直接改业务代码
- 提供可复用模板与完整示例，方便你直接开源、二次修改或给团队内部复用

## Hermes 在这个 skill 中做什么

- 收集需求或 bug 信息
- 检查 `ralph` 是否可用、项目是否已初始化
- 写入或更新 `CLAUDE.md`
- 写入或更新 `.ralph/fix_plan.md`
- 幂等补充 `.ralph/PROMPT.md` 的完成信号规范
- 启动 `ralph --monitor`
- 监控完成、超时、异常退出并通知用户

## Hermes 不做什么

- 不写任何业务代码
- 不直接修改源码文件
- 不替 Ralph 分析 bug 根因或设计修复方案

## 仓库内容

```text
.
├── ralph-dev.md                 # Hermes skill 主文件
├── templates/                   # Hermes 生成文件时可参考的模板
│   ├── CLAUDE.md
│   ├── fix_plan.md
│   ├── PROMPT.md
│   └── docs/...
├── examples/
│   └── basic-web-app/           # 一个完整示例项目骨架
├── README.md
└── LICENSE
```

## 快速使用

1. 安装 `ralph-claude-code` 以及它依赖的工具。
2. 把 [ralph-dev.md](./ralph-dev.md) 导入 Hermes skill 系统。
3. 给 Hermes 发需求，例如：

```text
在 /Users/me/projects/todo-api 里新增一个任务管理模块：
- Node.js + Express + PostgreSQL
- 需要任务增删改查
- 要有 JWT 登录
```

4. Hermes 会检查环境、准备文档和 `.ralph/` 文件，然后启动 `ralph`。
5. Ralph 调起 Claude Code 完成开发；Hermes 负责汇报状态与结果。

## 推荐配套方式

- `CLAUDE.md` 只保留项目简介、技术栈、规则和 docs 索引
- 详细模块说明、表结构、接口约定全部写到 `docs/`
- `fix_plan.md` 只写具体可执行任务，不写模糊目标
- `PROMPT.md` 里加入完成信号，保证 Hermes 能识别 Ralph 已结束

## 示例说明

[`examples/basic-web-app`](./examples/basic-web-app) 展示了一个“任务管理 Web API”项目在被 Hermes 初始化后的样子，包括：

- `CLAUDE.md`
- `.ralph/fix_plan.md`
- `.ralph/PROMPT.md`
- `docs/project.md`
- `docs/modules/*.md`
- `docs/database/*.md`
- `docs/api/*.md`
- `docs/skills/*.md`

如果你要给别人展示这个 skill 的工作流，这个示例可以直接截图或作为 demo 仓库结构引用。

## 适合开源发布的点

- Skill 行为边界清晰
- 示例项目完整，便于理解输出产物
- 模板与示例分离，既能复制也能演示
- MIT 许可证，便于他人复用

## 后续可选增强

- 增加多语言模板（Node / Python / Go）
- 增加 `.github` issue template，方便收集 bug / feature request
- 增加一个脚本把 `templates/` 快速复制到目标项目

## License

MIT
