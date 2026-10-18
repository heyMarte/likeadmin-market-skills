---
doc_type: application_api
app_id: 4
app_code: watermark_removal
ai_app_api_id: 25
api_code: xhs_search_note
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 19
existing_parent_id: 67
existing_slug: watermark-removal-xhs-search-note
existing_title: "水印消除 · 小红书笔记搜索"
---

# 水印消除 · 小红书笔记搜索

小红书笔记搜索

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 水印消除 |
| 应用编码 | `watermark_removal` |
| API 名称 | 小红书笔记搜索 |
| API 编码 | `xhs_search_note` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/watermark_removal/xhs_search_note` |
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
POST /api/v1/apps/watermark_removal/xhs_search_note
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `page` | string | 否 | — | — | `1` | 页码，默认 1 |
| `sort` | string | 否 | — | `general` / `popularity_descending` / `time_descending` / `comment_descending` / `collect_descending` | `general` | 排序：general / popularity_descending / time_descending / comment_descending / collect_descending |
| `keyword` | string | 是 | — | — | `护肤` | 搜索关键词 |
| `noteTime` | string | 否 | — | — | — | 发布时间筛选（按服务约定传值），也可用 note_time |
| `noteType` | string | 否 | — | — | `_0` | 笔记类型：_0 通用 / _1 视频 / _2 图文，也可用 note_type |

## 请求示例

```json
{
    "page": "1",
    "sort": "general",
    "keyword": "护肤",
    "noteTime": "string",
    "noteType": "_0"
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
