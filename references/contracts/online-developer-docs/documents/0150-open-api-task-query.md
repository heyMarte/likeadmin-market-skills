# 通用任务查询

异步模型任务和异步应用任务提交成功后，均使用本接口查询平台任务状态和最终结果。

**请求路径**：`GET /api/v1/tasks/{task_id}`

**鉴权**：`Authorization: Bearer <API_KEY>`

`task_id` 取自创建任务或异步应用接口成功响应中的 `data.task_id`。任务只能由创建它的 API Key 所属租户和用户查询。

## 请求示例

```http
GET /api/v1/tasks/task_xxxxxxxxxxxx
Authorization: Bearer <YOUR_API_KEY>
```

也可以使用 cURL：

```bash
curl -X GET "https://<域名>/api/v1/tasks/task_xxxxxxxxxxxx" \
  -H "Authorization: Bearer <YOUR_API_KEY>"
```

## 响应字段

所有成功响应的业务数据均在 `data` 中。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `task_id` | string | 平台任务 ID。 |
| `model` | string | 创建任务时使用的平台模型编码。 |
| `type` | string | 任务类型，例如 `image`、`video`、`audio`、`text` 或 `app`。 |
| `call_type` | string | `sync` 或 `async`。 |
| `status` | string | `pending`、`processing`、`completed`、`failed` 或 `cancelled`。 |
| `created_at` | string \| null | 创建时间，ISO 8601 格式。 |
| `result` | object \| array | 仅任务完成时返回。实际结果字段和结构以所调用模型或应用的文档为准。 |
| `usage` | object | 正常完成时返回，含 token 用量及 `points_cost` 实扣点数。 |
| `completed_at` | string \| null | 正常完成时返回，ISO 8601 格式。 |
| `response_time_ms` | number \| null | 正常完成时返回，处理耗时（毫秒）。 |
| `error` | object | 任务处理失败或结果不可用时可能返回，包含 `code`、`message`。 |
| `storage` | object | 请求中使用 `storage` 且转存成功时返回。 |
| `storage_error` | object | 请求中使用 `storage` 且转存失败时可能返回。 |

## 响应示例

### 处理中

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "model": "h3-video",
    "type": "video",
    "call_type": "async",
    "status": "processing",
    "created_at": "2026-08-01T10:00:00+08:00"
  }
}
```

### 已完成

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "model": "h3-video",
    "type": "video",
    "call_type": "async",
    "status": "completed",
    "created_at": "2026-08-01T10:00:00+08:00",
    "completed_at": "2026-08-01T10:01:20+08:00",
    "response_time_ms": 80000,
    "result": {
      "video_url": "https://example.com/output.mp4"
    },
    "usage": {
      "input_tokens": 0,
      "output_tokens": 0,
      "total_tokens": 0,
      "points_cost": 400
    }
  }
}
```

### 失败

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "model": "h3-video",
    "type": "video",
    "call_type": "async",
    "status": "failed",
    "created_at": "2026-08-01T10:00:00+08:00",
    "error": {
      "code": "task_failed",
      "message": "任务处理失败，请检查参数后重试。"
    }
  }
}
```

## 轮询与回调

- 未传 `callback_url` 时，使用本接口轮询。建议从提交后 2 秒开始，每次间隔 2 至 5 秒；任务处于 `completed`、`failed` 或 `cancelled` 时停止轮询。
- 提交时传入 `callback_url` 后，平台会在任务成功或失败时向该地址发送 POST 通知；仍建议保留本查询接口作为回调未收到时的补偿查询。
- 查询接口不产生新的扣费。实际扣费以任务完成时返回的 `usage.points_cost` 为准。

任务列表和取消任务的接口说明见 [公共参数与 storage](api-integration.md)。
