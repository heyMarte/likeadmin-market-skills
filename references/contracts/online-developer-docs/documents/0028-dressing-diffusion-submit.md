# AI换装 · 提交换装任务

提交图片换装任务，返回 task_id 用于查询结果

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | AI换装 |
| 应用编码 | `dressing_diffusion` |
| API 名称 | 提交换装任务 |
| API 编码 | `submit` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/dressing_diffusion/submit` |
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
POST /api/v1/apps/dressing_diffusion/submit
```

该接口为异步任务，提交后返回 `task_id`，请通过 `GET /api/v1/tasks/{task_id}` 查询结果，或在请求中传 `callback_url` 接收完成回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `garment` | object | 是 | — | — | — | 服装配置。格式：{"data": [{"category": "upper", "url": "服装图URL"}, ...]}。category 可选值：upper（上衣）、bottom（下装）、full（连衣裙/全身装）。支持单件或上下套装组合，服装图建议白底平铺。 |
| `model_url` | string | 是 | — | `JPG` / `PNG` | `https://example.com/model.jpg` | 模特图片 URL，建议主体清晰、光线均匀的正面或半身模特图。格式：JPG/PNG |

## 请求示例

```json
{
    "garment": {},
    "model_url": "https://example.com/model.jpg"
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
