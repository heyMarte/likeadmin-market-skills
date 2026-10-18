---
doc_type: model_api
ai_model_id: 90
model_code: kling-v3-std-audio
channel_code: klingYC
type_code: video
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 42
existing_parent_id: 62
existing_slug: m-kling-v3-std-audio-klingyc
existing_title: "可灵 V3 标准有声（klingYC）"
---

# 可灵 V3 标准有声

视频生成（标准画质、带音频）。支持文生视频、图生视频与首尾帧；时长约 3～15 秒。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | 可灵 V3 标准有声 |
| 模型编码 | `kling-v3-std-audio` |
| 模型类型 | 视频生成（video） |
| 调用方式 | `POST /api/v1/tasks` |
| 调用模式 | 异步 |
| 计费方式 | 按输入时长（49.700000 元 / 分钟） |
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
| `prompt` | string | 否 | — | — | `广角镜头，角色在场景中缓慢移动，光影自然。` | 主提示词。图生高级模式下若使用多镜头提示词列表，勿与本字段的图生互斥规则冲突（以接口约定为准）。 |
| `duration` | integer | 否 | `5` | `3` ~ `15` | `5` | 视频时长（秒），约 3～15。 |
| `image_url` | string | 否 | — | — | `https://example.com/first.png` | 参考图 URL。传入则为图生视频；不传则为文生视频。若使用首尾帧，本字段可作首帧图。 |
| `extra_body` | object | 否 | — | — | — | 扩展生成参数（与原接口 extra_body 一致）。 |
| `last_image` | string | 否 | — | — | `https://example.com/last.png` | 结束帧图 URL，与首帧组合时为首尾帧图生视频。 |
| `aspect_ratio` | string | 否 | `16:9` | `16:9` / `9:16` / `1:1` | — | 输出画幅比例。图生模式下不保证严格生效，以实际成片为准。 |

## 请求示例

```json
{
    "model": "kling-v3-std-audio",
    "prompt": "广角镜头，角色在场景中缓慢移动，光影自然。",
    "duration": "5",
    "image_url": "https://example.com/first.png",
    "extra_body": {},
    "last_image": "https://example.com/last.png",
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
