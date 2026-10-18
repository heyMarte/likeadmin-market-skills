# VEO 3.1 Fast

快速视频生成模型，8 秒视频，支持 720p/1080p/4K。支持文生视频、首尾帧生视频、参考图生视频。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | VEO 3.1 Fast |
| 模型编码 | `veo3.1-fast` |
| 模型类型 | 视频生成（video） |
| 调用方式 | `POST /api/v1/tasks` |
| 调用模式 | 异步 |
| 计费方式 | 按业务参数计费（按 `pricing_rules` 阶梯定价） |
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
| `prompt` | string | 是 | — | — | `The car moves forward at a high speed` | 视频描述文本。 |
| `quality` | string | 是 | `720p` | `720p` / `1080p` / `4k` | — | 输出清晰度，不同清晰度价格不同。 |
| `duration` | integer | 否 | `8` | `8` | — | 视频时长（秒），固定 8 秒。 |
| `image_urls` | array | 否 | — | — | `["https://example.com/ref.jpg"]` | 参考图片地址列表，最多 3 张。 |
| `aspect_ratio` | string | 否 | `16:9` | `16:9` / `9:16` | — | 视频宽高比。 |
| `generation_type` | string | 否 | `TEXT` | `TEXT` / `FIRST&LAST` / `REFERENCE` | — | 视频生成模式。TEXT=文本生视频；FIRST&LAST=首尾帧生视频（可传 1~2 张图）；REFERENCE=参考图生视频（最多 3 张图，仅支持 16:9）。 |

## 请求示例

```json
{
    "model": "veo3.1-fast",
    "prompt": "The car moves forward at a high speed",
    "quality": "720p",
    "duration": "8",
    "image_urls": [
        "https://example.com/ref.jpg"
    ],
    "aspect_ratio": "16:9",
    "generation_type": "TEXT"
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
