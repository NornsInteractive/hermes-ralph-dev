# 表：tasks

## 字段
- `id`：UUID，主键
- `title`：varchar，非空
- `description`：text，可空
- `status`：varchar，非空，默认 `todo`
- `assignee_id`：UUID，可空，关联 `users.id`
- `created_at`：timestamp，非空
- `updated_at`：timestamp，非空

## 约束
- `status` 只允许 `todo`、`in_progress`、`done`
- 如果 `assignee_id` 存在，必须指向有效用户

## 用途
保存任务的核心状态与负责人信息。
