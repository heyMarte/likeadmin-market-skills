---
doc_type: application_api
app_id: 9
app_code: dressing_diffusion
ai_app_api_id: 16
api_code: query
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 29
existing_parent_id: 72
existing_slug: dressing-diffusion-query
existing_title: "AI换装 · 查询换装结果"
---

# AI换装 · 查询换装结果

根据 task_id 查询换装任务状态和结果图片

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | AI换装 |
| 应用编码 | `dressing_diffusion` |
| API 名称 | 查询换装结果 |
| API 编码 | `query` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/dressing_diffusion/query` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.0000 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/dressing_diffusion/query
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `task_id` | string | 是 | — | — | `7xxxxxxxxxxxxxx` | 提交换装任务时返回的任务 ID |

## 请求示例

```json
{
    "task_id": "7xxxxxxxxxxxxxx"
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
