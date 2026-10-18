# 全能视频生成 · 查询任务

全能视频生成使用独立的应用查询接口；查询接口不重复计费。

## 鉴权与路径

```http
POST /api/v1/apps/full_video/query
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求参数

`task_id` 与 `elastic_task_id` 二选一。通常直接保存并传回提交接口返回的 `task_id`。

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `task_id` | string | 二选一 | 提交接口返回的平台任务 ID，例如 `task_xxxxxxxxxxxx`。 |
| `elastic_task_id` | integer | 二选一 | 兼容弹性任务数字 ID；常规接入不需要传。 |

```json
{
  "task_id": "task_xxxxxxxxxxxx"
}
```

## 成功响应

任务等待或运行中时：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "result": {
      "task_id": "task_xxxxxxxxxxxx",
      "status": "running"
    },
    "usage": {
      "points_cost": 0,
      "actual_points": 0
    }
  }
}
```

任务完成后，机器返回的结果位于 `data.result.data`。视频结果的具体字段由当前部署版本决定；调用方应保存完整 `data.result`，并从其中的结果地址字段读取视频。

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "result": {
      "task_id": "task_xxxxxxxxxxxx",
      "status": "done",
      "mode": "async_query",
      "remote_task_id": "xxxxxxxxxxxxxxxx",
      "data": {
        "status": "completed",
        "output_url": "https://example.com/output.mp4",
        "progress": 100
      }
    }
  }
}
```

| 字段 | 说明 |
| --- | --- |
| `data.result.task_id` | 平台任务 ID，与提交响应一致。 |
| `data.result.status` | 任务状态；常见为 `pending`、`running`、`done` 或 `error`。 |
| `data.result.remote_task_id` | 任务已进入部署服务后可能返回的内部任务标识。 |
| `data.result.data` | 完成后的机器结果；视频地址通常在其中的 `output_url` 或同等结果字段。 |
| `data.usage.actual_points` | 任务实际扣费点数。 |