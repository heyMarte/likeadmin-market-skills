# Grok Imagine

视频生成：支持文生视频与单张参考图图生视频。时长 6 或 10 秒；输出分辨率 480p / 720p；画幅比例 16:9、1:1 或 9:16。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | Grok Imagine |
| 模型编码 | `grok-imagine` |
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
| `size` | string | 否 | — | — | `720p` | 输出分辨率。未传时使用模型默认 720p。常用 480p、720p。 |
| `prompt` | string | 是 | — | — | `女孩坐在车里，窗外是城市夜景` | 主提示词，描述画面内容与镜头。 |
| `duration` | integer | 否 | — | — | `10` | 输出时长，单位秒。未传时使用模型默认 10；推荐 6 或 10（以通道说明为准）。 |
| `image_url` | string | 否 | — | — | `https://example.com/reference.jpg` | 单张参考图地址；传入则为图生视频，不传则为文生视频。 |
| `aspect_ratio` | string | 否 | — | — | `16:9` | 输出画幅比例。未传时使用模型默认 16:9。 |

## 请求示例

```json
{
    "model": "grok-imagine",
    "size": "720p",
    "prompt": "女孩坐在车里，窗外是城市夜景",
    "duration": 10,
    "image_url": "https://example.com/reference.jpg",
    "aspect_ratio": "16:9"
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
