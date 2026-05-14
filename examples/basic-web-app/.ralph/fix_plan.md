# Ralph Fix Plan

## High Priority
- [ ] 实现 `POST /api/auth/login`，校验邮箱和密码，返回 JWT access token，详见 `docs/api/endpoints/auth.md`
- [ ] 实现 `GET /api/tasks`，支持按状态和负责人筛选，详见 `docs/api/endpoints/tasks.md`
- [ ] 实现 `POST /api/tasks`，创建任务并写入 `tasks` 表，详见 `docs/database/tables/tasks.md`
- [ ] 实现 `PATCH /api/tasks/:id`，支持更新标题、描述、状态和负责人

## Medium Priority
- [ ] 为认证和任务接口补齐单元测试与集成测试
- [ ] 更新 README 中的 API 使用示例

## Low Priority
- [ ] 清理重复校验逻辑

## Completed
- [x] 项目已启用 Ralph

## Notes
- 每完成一项立即更新本文件的勾选状态
- 完成所有任务后在 `.ralph/DONE` 写入完成时间和任务摘要
