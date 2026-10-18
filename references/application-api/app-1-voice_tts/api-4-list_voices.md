---
doc_type: application_api
app_id: 1
app_code: voice_tts
ai_app_api_id: 4
api_code: list_voices
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 11
existing_parent_id: 64
existing_slug: voice-tts-list-voices
existing_title: "语音TTS · 音色列表"
---

# 语音TTS · 音色列表

查询当前用户创建的语音音色模型列表

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 语音TTS |
| 应用编码 | `voice_tts` |
| API 名称 | 音色列表 |
| API 编码 | `list_voices` |
| 请求方式 | `GET` |
| 请求路径 | `/api/v1/apps/voice_tts/list_voices` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.0010 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
GET /api/v1/apps/voice_tts/list_voices
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `tag` | string | 否 | — | — | — | 按标签筛选 |
| `title` | string | 否 | — | — | — | 按音色名称搜索 |
| `sort_by` | string | 否 | — | `score` / `task_count` / `created_at` | — | 排序：score / task_count / created_at |
| `language` | string | 否 | — | — | — | 按语言筛选 |
| `page_size` | integer | 否 | — | — | — | 每页数量，官方默认 10 |
| `page_number` | integer | 否 | — | — | — | 页码，默认 1 |
| `title_language` | string | 否 | — | — | — | 按标题语言筛选 |

## 请求示例

```json
{
    "tag": "string",
    "title": "string",
    "sort_by": "score",
    "language": "string",
    "page_size": 20,
    "page_number": 1,
    "title_language": "string"
}
```

## 成功响应

```json
{
    "code": 1,
    "msg": "success",
    "data": {
        "_comment": "具体字段以接口实际返回为准"
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
