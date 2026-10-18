---
doc_type: model_api
ai_model_id: 71
model_code: grok-video
channel_code: xAIQ
type_code: video
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 48
existing_parent_id: 69
existing_slug: m-grok-video-xaiq
existing_title: "Grok Video（xAIQ）"
---

# Grok Video

视频生成模型，支持文生视频和图生视频。支持 6-30 秒时长，720p 输出。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | Grok Video |
| 模型编码 | `grok-video` |
| 模型类型 | 视频生成（video） |
| 调用方式 | `POST /api/v1/tasks` |
| 调用模式 | 异步 |
| 计费方式 | 按输出时长（0.028000 元 / 分钟） |
| 最大 token | 1 |

</details>

## 鉴权

所有调用必须在请求头携带平台分配的 API Key：

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/tasks
```

该模型为异步任务，提交后返回 `task_id`，请通过 `GET /api/v1/tasks/{task_id}` 查询结果，或在请求中传 `callback_url` 接收完成回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `prompt` | string | 是 | — | — | — | 视频描述文本。 |
| `quality` | string | 否 | — | `720p` | — | 输出清晰度。 |
| `duration` | integer | 是 | `6` | `6` / `10` / `15` / `20` / `25` / `30` | — | 视频时长（秒）。 |
| `image_urls` | array | 否 | — | — | — | 参考图片地址列表，最多 7 张。 |
| `aspect_ratio` | string | 否 | — | `2:3` / `3:2` / `1:1` / `9:16` / `16:9` | — | 视频宽高比。建议与参考图比例一致。 |

## 请求示例

```json
{
    "model": "grok-video",
    "prompt": "string",
    "quality": "720p",
    "duration": "6",
    "image_urls": [],
    "aspect_ratio": "2:3"
}
```

## 成功响应

```json
{
    "task_id": "tsk_xxxxxxxxxxxxxxxx",
    "status": "pending",
    "created_at": 1740000000
}
```

### 查询任务结果

```http
GET /api/v1/tasks/{task_id}
```

```json
{
    "task_id": "tsk_xxxxxxxxxxxxxxxx",
    "status": "completed",
    "created_at": 1740000000,
    "completed_at": 1740000060,
    "result": {
        "video_url": "https://cdn.example.com/result.mp4",
        "duration": 10
    },
    "usage": {
        "points": 100
    }
}
```

## 失败响应

```json
{
    "error": {
        "message": "点数余额不足",
        "type": "insufficient_points",
        "code": "insufficient_points"
    }
}
```

### 错误码

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `invalid_request` | 参数缺失或格式错误 |
| 401 | `auth_failed` | API Key 缺失或无效 |
| 402 | `insufficient_points` | 点数余额不足 |
| 402 | `key_quota_exceeded` | 当前 API Key 点数额度不足 |
| 403 | `permission_denied` | 当前 API Key 无权调用该模型/应用 |
| 404 | `not_found` | 模型 / 应用 / 任务不存在 |
| 429 | `queue_limit_exceeded` | 排队任务已达上限 |
| 5xx | `server_error` | 服务异常，请稍后重试 |
