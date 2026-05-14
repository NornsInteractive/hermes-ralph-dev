## 开发规范

开始编码前：
1. 读取 `CLAUDE.md`
2. 读取当前任务涉及的 `docs/` 文档
3. 如果 `docs/skills/` 中存在对应阶段文件，先遵循其中准则

## 完成信号

每完成一个任务后：
1. 从 `fix_plan.md` 中更新该任务状态
2. 将完成记录追加到 `docs/changelog/done.md`

所有 `fix_plan.md` 任务完成后：
1. 在状态输出中包含 `EXIT_SIGNAL: true`
2. 在 `.ralph/DONE` 写入完成时间、已完成任务和测试摘要
