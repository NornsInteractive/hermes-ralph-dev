# 接口模块：tasks

## `GET /api/tasks`

- 用途：获取任务列表
- 查询参数：
  - `status`: `todo | in_progress | done`
  - `assigneeId`: string
- 成功响应：`200`

## `POST /api/tasks`

- 用途：创建任务
- 请求体：
  - `title`: string
  - `description`: string
  - `assigneeId`: string，可选
- 成功响应：`201`

## `PATCH /api/tasks/:id`

- 用途：更新任务
- 请求体：
  - `title`: string，可选
  - `description`: string，可选
  - `status`: string，可选
  - `assigneeId`: string，可选
- 失败响应：
  - `400`：参数非法
  - `404`：任务不存在
