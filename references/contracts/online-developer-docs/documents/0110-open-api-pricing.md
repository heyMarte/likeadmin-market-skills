# 价格查询

**Base**：`https://<域名>/api/v1`

**鉴权**：`Authorization: Bearer <API_KEY>`

历史字段说明：响应中的 `per_1k_input` / `per_1k_output` 沿用早期字段名，模型 Token 计费实际单位为**点数 / 百万 Token**。

## `GET /api/v1/pricing`

查询单个模型 API 或应用 API 的当前有效价格规则。

| Query 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `type` | string | 是 | `model` 或 `app_api`。 |
| `model` | string | `type=model` 时是 | 模型编码 `model_code`。 |
| `channel` | string | 否 | 模型渠道编码；不传时按服务端路由权重解析。 |
| `app_code` | string | `type=app_api` 时是 | 应用编码。 |
| `api_code` | string | `type=app_api` 时是 | 应用 API 编码。 |

模型示例：

```bash
curl -H "Authorization: Bearer <API_KEY>" \
  "https://<域名>/api/v1/pricing?type=model&model=gpt-image-2"
```

应用 API 示例：

```bash
curl -H "Authorization: Bearer <API_KEY>" \
  "https://<域名>/api/v1/pricing?type=app_api&app_code=wan&api_code=create"
```

成功时 `data`：

- `type`：`model` / `app_api`
- `available`：是否可用
- `resource`：资源标识，如 `model_code`、`channel_code`、`app_code`、`api_code`
- `pricing`：统一有效价结构，包含 `billing_type`、`per_1k_input`、`per_1k_output`、`fixed_points`、`pricing_rules`、`pricing_matrix`、`model_rates`、`discount_rate`、`source`，以及 Token 扩展字段如 `cached_input_multiplier`、`reasoning_output_multiplier`、`cached_input_per_1m`
- `price_view`：可读价格说明，来自 `PriceExplainService::explain()`，含 `billing_type_desc`、`formula`、`rules`、`has_complex_rules`、`is_missing`
- `pricing_source`：价格来源，可能是租户基础价、用户自定义价或用户折扣价

响应示例：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "type": "model",
    "available": true,
    "resource": {
      "model_code": "gpt-image-2",
      "channel_code": "optional"
    },
    "pricing": {
      "billing_type": 1,
      "per_1k_input": 10,
      "per_1k_output": 40,
      "fixed_points": 0,
      "pricing_rules": [],
      "pricing_matrix": null,
      "model_rates": null,
      "discount_rate": 1,
      "source": "tenant"
    },
    "price_view": {
      "billing_type_desc": "按百万 Token 计费",
      "formula": "输入 Token 与输出 Token 分别计费",
      "rules": [],
      "has_complex_rules": false,
      "is_missing": false
    },
    "pricing_source": {
      "source": "tenant"
    }
  }
}
```

## `POST /api/v1/pricing/batch`

批量查询多个模型 API 或应用 API 的当前有效价格规则，`items` 必填，最多 100 条。

请求体：

```json
{
  "items": [
    { "type": "model", "model": "gpt-image-2", "channel": "optional" },
    { "type": "app_api", "app_code": "wan", "api_code": "create" }
  ]
}
```

成功时 `data.items` 按请求顺序返回。未传 `items` 或传空数组会返回 `invalid_request`。每个成功项的 `resource` 同时包含视觉、深度思考和三类参考素材数量字段，以及 `capabilities` 对象。单项不可用时不会导致整批失败，该项返回：

```json
{
  "type": "model",
  "available": false,
  "error_code": "model_not_available",
  "message": "模型不可用或无权限"
}
```

## 权限与可见性

- 模型必须在当前租户已上架、启用、可见；若模型设置了用户授权，则当前 API Key 绑定用户必须在白名单中。
- API Key 的 `permissions` 必须允许对应模型类型；应用 API 需要 `app` 权限。
- 应用必须在当前租户已上架、启用、可见，且应用 API 已启用并有有效价格。

## 计费体系覆盖

`pricing` 与实际扣费口径保持一致，可覆盖按次、按百万 Token、按参数、按字符、按输入时长、按输出时长、矩阵价格、模型分档价格等体系。`price_view` 用于给开发者展示可读公式和规则说明；如存在复杂规则，`has_complex_rules` 会标记为 `true`。
