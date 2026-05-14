# 表：users

## 字段
- `id`：UUID，主键
- `email`：varchar，唯一，非空
- `password_hash`：varchar，非空
- `display_name`：varchar，非空
- `status`：varchar，非空，默认 `active`
- `created_at`：timestamp，非空

## 约束
- `email` 唯一
- `status` 只允许 `active` 和 `disabled`

## 用途
保存可登录系统的用户账号信息。
