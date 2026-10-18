---
doc_type: application_api
app_id: 8
app_code: mmaudio
ai_app_api_id: 13
api_code: submit
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 26
existing_parent_id: 73
existing_slug: mmaudio-submit
existing_title: "音效生成、视频配音 · 提交任务"
---

# 音效生成、视频配音 · 提交任务

创建一条弹性 GPU 任务，返回平台任务 id

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 音效生成、视频配音 |
| 应用编码 | `mmaudio` |
| API 名称 | 提交任务 |
| API 编码 | `submit` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/mmaudio/submit` |
| 调用模式 | 异步 |
| 计费方式 | 按次（0.1000 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/mmaudio/submit
```

该接口为异步任务，提交后返回 `task_id`，请通过 `GET /api/v1/tasks/{task_id}` 查询结果，或在请求中传 `callback_url` 接收完成回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `mode` | string | 否 | `async_query` | — | — | 任务模式，默认 async_query |
| `audio_url` | string | 否 | — | — | `https://example.com/reference_audio.wav` | 可选的输入音频 URL，用于参考配音风格 |
| `input_url` | string | 是 | — | — | `https://example.com/silent_video.mp4` | 输入视频文件 URL |
| `duration` | number | 否 | 自动探测 | — | `15.695` | 输入视频时长，单位秒；通常可不传，由平台从 input_url 探测 |
| `prompt` | string | 否 | 系统默认提示词 | — | `Generate natural cinematic sound effects and ambience for this video.` | 音频生成提示词，用于描述期望的音效、环境声或配乐风格 |

## 请求示例

```json
{
    "mode": "async_query",
    "audio_url": "https://example.com/reference_audio.wav",
    "input_url": "https://example.com/silent_video.mp4",
    "duration": 15.695,
    "prompt": "Generate natural cinematic sound effects and ambience for this video."
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
