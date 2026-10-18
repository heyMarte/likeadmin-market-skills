---
doc_type: application_api
app_id: 8
app_code: mmaudio
ai_app_api_id: 14
api_code: query
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 27
existing_parent_id: 73
existing_slug: mmaudio-query
existing_title: "音效生成、视频配音 · 查询任务"
---

# 音效生成、视频配音 · 查询任务

按弹性任务 id 查询状态与结果

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 音效生成、视频配音 |
| 应用编码 | `mmaudio` |
| API 名称 | 查询任务 |
| API 编码 | `query` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/mmaudio/query` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.1000 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/mmaudio/query
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `task_id` | integer | 否 | — | — | — | 同 elastic_task_id |
| `elastic_task_id` | integer | 否 | — | — | — | 弹性任务 id（与 task_id 二选一） |

## 请求示例

```json
{
    "task_id": 1,
    "elastic_task_id": 1
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
