# API 总览

## 约定
- 所有响应均为 JSON
- 成功响应包含业务数据
- 失败响应统一为 `{ code, message }`
- 需要认证的接口要求 `Authorization: Bearer <token>`

## 模块入口
- 认证：`docs/api/endpoints/auth.md`
- 任务：`docs/api/endpoints/tasks.md`
