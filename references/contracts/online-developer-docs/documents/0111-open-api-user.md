# 用户信息查询

**Base**：`https://<域名>/api/v1`

**鉴权**：`Authorization: Bearer <API_KEY>`

| API | 说明 |
| --- | --- |
| `GET /api/v1/user/balance` | 查询用户余额/点数 |
| `GET /api/v1/user/usage` | 查询用户使用量 |

## `GET /api/v1/user/balance`

查询当前 API Key 对应用户的可用点数。

请求示例：

```bash
curl -H "Authorization: Bearer <API_KEY>" \
  "https://<域名>/api/v1/user/balance"
```

成功响应：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "available_points": 1000
  }
}
```

字段说明：

| 字段 | 说明 |
| --- | --- |
| `available_points` | 当前用户可用点数 |

## `GET /api/v1/user/usage`

查询当前 API Key 对应用户在指定时间范围内的使用统计。

Query 参数：

| 参数 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `start_date` | string | 当天前推 30 天 | 开始日期，格式 `Y-m-d` |
| `end_date` | string | 当天 | 结束日期，格式 `Y-m-d` |

请求示例：

```bash
curl -H "Authorization: Bearer <API_KEY>" \
  "https://<域名>/api/v1/user/usage?start_date=2026-05-01&end_date=2026-05-25"
```

成功响应：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "period": {
      "start": "2026-05-01",
      "end": "2026-05-25"
    },
    "total_calls": 20,
    "completed": 18,
    "failed": 2,
    "total_points": 120.5,
    "total_tokens": 30000
  }
}
```

字段说明：

| 字段 | 说明 |
| --- | --- |
| `period.start` | 统计开始日期 |
| `period.end` | 统计结束日期 |
| `total_calls` | 总调用次数 |
| `completed` | 成功完成次数 |
| `failed` | 未完成或失败次数 |
| `total_points` | 已完成调用消耗点数合计 |
| `total_tokens` | 已完成调用 Token 合计 |
