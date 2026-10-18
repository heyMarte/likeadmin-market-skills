# GPT Image 2

文生图与图生图，支持多比例与 1K/2K/4K 档位；单任务轮询取图。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | GPT Image 2 |
| 模型编码 | `gpt-image-2` |
| 模型类型 | 图片生成（image） |
| 调用方式 | `POST /api/v1/tasks` |
| 调用模式 | 异步 |
| 计费方式 | 多规格价（1K / 2K / 4K） |
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
| `n` | integer | 否 | `1` | — | — | 出图张数。当前接口仅支持 1，请勿传其他值。 |
| `prompt` | string | 是 | — | — | `窗边的橘猫，夕阳，水彩风格` | 画面描述，可中英文，建议写清主体、风格、光线等。 |
| `image_size` | string | 否 | — | — | — | 图片尺寸说明（与业务展示相关；接口侧以比例与 resolution 为准）。 |
| `image_urls` | array | 否 | — | — | `["https://example.com/reference.jpg"]` | 参考图数组；传图生图。最多 16 项。每项为公网可访问的 URL 或 data:image/...;base64,... 的 data URI，可混用。与 aspect_ratio 同传时按画幅出图；未传 aspect_ratio 时输出可随参考图。 |
| `resolution` | string | 否 | `1k` | `1k` / `2k` / `4k` | — | 输出档位：1k、2k 或 4k，参与计费。 |
| `aspect_ratio` | string | 否 | `auto` | `auto` / `1:1` / `3:2` / `2:3` / `4:3` / `3:4` / `5:4` / `4:5` / `16:9` / `9:16` / `2:1` / `1:2` / `3:1` / `1:3` / `21:9` / `9:21` | `auto` | 画幅比例，可传 `auto` 由系统自动选择，也可传 1:1、16:9 等比例写法。 |

## 请求示例

```json
{
    "model": "gpt-image-2",
    "n": 1,
    "prompt": "窗边的橘猫，夕阳，水彩风格",
    "image_size": "string",
    "image_urls": [
        "https://example.com/reference.jpg"
    ],
    "resolution": "1k",
    "aspect_ratio": "auto"
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
        "images": [
            "https://cdn.example.com/result.jpg"
        ]
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
