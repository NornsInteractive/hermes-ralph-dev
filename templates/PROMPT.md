## 开发规范

开始编码前：
1. 阅读 `CLAUDE.md`
2. 阅读当前任务涉及的 `docs/` 文档
3. 若存在对应阶段 skill 文件，优先遵循该文件

## 完成信号

每完成一个任务后：
1. 更新 `.ralph/fix_plan.md` 中的勾选状态
2. 将完成记录追加到 `docs/changelog/done.md`

所有任务完成后：
1. 在状态输出中包含 `EXIT_SIGNAL: true`
2. 在 `.ralph/DONE` 写入完成时间、完成任务和测试结果摘要
