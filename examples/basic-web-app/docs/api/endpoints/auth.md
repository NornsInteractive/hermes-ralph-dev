# 接口模块：auth

## `POST /api/auth/login`

- 用途：用户登录并获取 token
- 请求体：
  - `email`: string
  - `password`: string
- 成功响应：`200`

```json
{
  "token": "jwt-token",
  "user": {
    "id": "user_123",
    "email": "alice@example.com",
    "displayName": "Alice"
  }
}
```

- 失败响应：
  - `400`：参数缺失
  - `401`：邮箱或密码错误
  - `403`：用户被禁用
