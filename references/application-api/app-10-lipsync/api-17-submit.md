---
doc_type: application_api
app_id: 10
app_code: lipsync
ai_app_api_id: 17
api_code: submit
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 30
existing_parent_id: 71
existing_slug: lipsync-submit
existing_title: "数字人对口型 · 提交任务"
---

# 数字人对口型 · 提交任务

创建一条数字人对口型任务，返回平台任务 ID

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 数字人对口型 |
| 应用编码 | `lipsync` |
| API 名称 | 提交任务 |
| API 编码 | `submit` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/lipsync/submit` |
| 调用模式 | 异步 |
| 计费方式 | 按输入时长（0.000000 元 / 分钟） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/lipsync/submit
```

该接口为异步任务，提交后返回 `task_id`，请通过 `GET /api/v1/tasks/{task_id}` 查询结果，或在请求中传 `callback_url` 接收完成回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `mode` | string | 否 | `async_query` | — | — | 任务模式，默认 async_query |
| `model` | string | 否 | `xiaojiayu1.0` | — | — | 数字人模型标识，支持 xiaojiayu1.0、xiaojiayu2.0、xiaojiayu3.0 及对应简写 |
| `audio_url` | string | 是 | — | — | `https://example.com/speech.wav` | 输入音频文件 URL（驱动口型的语音） |
| `video_url` | string | 是 | — | — | `https://example.com/avatar_video.mp4` | 输入视频文件 URL（数字人原始视频） |

## 请求示例

```json
{
    "mode": "async_query",
    "model": "xiaojiayu1.0",
    "audio_url": "https://example.com/speech.wav",
    "video_url": "https://example.com/avatar_video.mp4"
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
        "output": "任务输出内容"
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
