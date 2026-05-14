# 技术栈说明

## 选型
- 语言：TypeScript
- 服务框架：Express
- 数据库：PostgreSQL
- ORM / 查询层：SQL + migration 工具
- 测试：Vitest、Supertest

## 约束
- 所有接口返回 JSON
- 需要认证的接口统一使用 Bearer Token
- 第一版先不做复杂权限系统，只校验用户登录身份
