---
doc_type: application_api
app_id: 4
app_code: watermark_removal
ai_app_api_id: 20
api_code: xhs_user_note_list
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 14
existing_parent_id: 67
existing_slug: watermark-removal-xhs-user-note-list
existing_title: "水印消除 · 用户笔记"
---

# 水印消除 · 用户笔记

用户笔记列表

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 水印消除 |
| 应用编码 | `watermark_removal` |
| API 名称 | 用户笔记 |
| API 编码 | `xhs_user_note_list` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/watermark_removal/xhs_user_note_list` |
| 调用模式 | 同步 |
| 计费方式 | 按次（20.0000 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/watermark_removal/xhs_user_note_list
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `cursor` | string | 否 | — | — | — | 翻页游标，取自上一页笔记项中的 cursor |
| `userId` | string | 是 | — | — | `5f85aabb0000000001006faf` | 笔记侧用户 ID（userid） |
| `lastCursor` | string | 否 | — | — | — | 上一页末条目的分页游标（可选），也可用 last_cursor |

## 请求示例

```json
{
    "cursor": "string",
    "userId": "5f85aabb0000000001006faf",
    "lastCursor": "string"
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
