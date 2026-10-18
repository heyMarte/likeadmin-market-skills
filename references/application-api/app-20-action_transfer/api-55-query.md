---
doc_type: application_api
app_id: 20
app_code: action_transfer
ai_app_api_id: 55
api_code: query
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 128
existing_parent_id: 126
existing_slug: action-transfer-query
existing_title: "动作迁移 · 查询任务"
---

# 动作迁移 · 查询任务

根据提交接口返回的 `task_id` 查询动作迁移任务状态、生成结果与计费信息。查询接口不重复计费。

## 接口地址

```http
POST /api/v1/apps/action_transfer/query
```

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `task_id` | string | 是 | 提交任务接口返回的平台任务 ID，例如 `task_xxxxxxxxxxxxxxxx`。 |

## 请求示例

```json
{
  "task_id": "task_xxxxxxxxxxxxxxxx"
}
```

## cURL 示例

```bash
curl -X POST "https://你的域名/api/v1/apps/action_transfer/query" \
  -H "Authorization: Bearer <YOUR_API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{
    "task_id": "task_xxxxxxxxxxxxxxxx"
  }'
```

## 成功响应

处理中：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "result": {
      "task_id": "task_xxxxxxxxxxxxxxxx",
      "status": "processing",
      "duration": 0
    },
    "usage": {
      "points_cost": 0,
      "actual_points": 0
    }
  }
}
```

完成后：

任务完成后，`result` 中会返回与对口型数字人一致的视频任务结构。调用方优先读取 `result.video_url`；如需保留多结果兼容，可读取 `result.results`。

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "result": {
      "task_id": "task_xxxxxxxxxxxxxxxx",
      "status": "completed",
      "mode": "async_query",
      "data": {
        "result": "推理完成并上传成功",
        "status": "completed",
        "duration": 15.695,
        "progress": 100,
        "cover_url": "https://example.com/cover.jpg",
        "output_url": "https://example.com/output.mp4",
        "resolution": "1080x1920"
      },
      "result": "推理完成并上传成功",
      "results": [
        {
          "url": "https://example.com/output.mp4",
          "video_url": "https://example.com/output.mp4"
        }
      ],
      "duration": 15.695,
      "cover_url": "https://example.com/cover.jpg",
      "video_url": "https://example.com/output.mp4",
      "output_url": "https://example.com/output.mp4",
      "resolution": "1080x1920",
      "remote_task_id": "xxxxxxxxxxxxxxxx"
    },
    "usage": {
      "points_cost": 31.39,
      "actual_points": 31.39
    }
  }
}
```

| 字段 | 说明 |
| --- | --- |
| `result.status` | 任务状态，常见值：`processing`、`completed`、`failed`、`cancelled`。 |
| `result.duration` | 平台识别到的视频时长，单位秒。 |
| `result.video_url` | 生成后的视频地址，建议优先读取。 |
| `result.output_url` | 兼容字段，通常与 `video_url` 一致。 |
| `result.results` | 多结果兼容数组。 |
| `usage.actual_points` | 任务实际扣费点数。 |

## 失败响应

```json
{
  "code": 0,
  "msg": "任务不存在",
  "data": []
}
```

常见错误：

| HTTP | code | 说明 |
| --- | --- | --- |
| 400 | `invalid_request` | `task_id` 缺失或格式错误。 |
| 401 | `auth_failed` | API Key 缺失或无效。 |
| 403 | `permission_denied` | 当前 API Key 无权查询该任务。 |
| 404 | `not_found` | 任务不存在或不属于当前租户。 |
| 5xx | `server_error` | 服务异常，请稍后重试。 |
