---
doc_type: application_api
app_id: 11
app_code: seedance
ai_app_api_id: 31
api_code: getAsset
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 34
existing_parent_id: 61
existing_slug: seedance-getasset
existing_title: "Seedance 2.0 · 获取素材详情"
---

# Seedance 2.0 · 获取素材详情

查询素材信息，包括状态（Active / Processing / Failed）、URL、所属分组等。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | Seedance 2.0 |
| 应用编码 | `seedance` |
| API 名称 | 获取素材详情 |
| API 编码 | `getAsset` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/seedance/getAsset` |
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
POST /api/v1/apps/seedance/getAsset
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `Id` | string | 是 | — | — | `asset-20260402224305-54j64` | Asset Id（来自 createAsset 返回的 Result.Id） |
| `ProjectName` | string | 否 | `default` | — | — | 项目名称，默认 default |

## 请求示例

```json
{
    "Id": "asset-20260402224305-54j64",
    "ProjectName": "default"
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
