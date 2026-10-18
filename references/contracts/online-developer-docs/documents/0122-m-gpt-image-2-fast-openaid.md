# GPT Image 2 Fast

快速文生图与图生图，支持按比例选择 1K、2K、4K；系统会自动换算为实际像素尺寸。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | GPT Image 2 Fast |
| 模型编码 | `gpt-image-2-fast` |
| 模型类型 | 图片生成（image） |
| 调用方式 | `POST /api/v1/tasks` |
| 调用模式 | 异步 |
| 计费方式 | 按分辨率 SKU 计费（1K / 2K / 4K） |
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
| `model` | string | 是 | — | — | `gpt-image-2-fast` | 模型编码。 |
| `prompt` | string | 是 | — | 最多 5000 字符 | `海边小岛、灯塔、月光、飞鸟与远处船只，电影感光影` | 图像内容的文字描述。 |
| `aspect_ratio` | string | 否 | `auto` | 见下方尺寸说明 | `16:9` | 输出画幅比例。 |
| `image_size` | string | 否 | `1k` | `1k` / `2k` / `4k` | `4k` | 分辨率规格，用于计费和尺寸换算。 |
| `image_urls` | string 或 array | 否 | — | — | `["https://example.com/reference.jpg"]` | 参考图。单张可传字符串，多张可传数组。 |
| `quality` | string | 否 | — | `low` / `medium` / `high` | `medium` | 生成质量。 |

## 尺寸说明

`aspect_ratio` 传画幅比例，`image_size` 传 `1k` / `2k` / `4k`。系统会在提交时自动换算为实际像素尺寸；用户不需要直接传像素尺寸。该模型也兼容旧字段 `size`、`resolution`、`image`，但推荐统一使用 `aspect_ratio`、`image_size`、`image_urls`。

`gpt-image-2-fast` 的生成接口按像素尺寸提交，`image_size` 不会作为独立字段提交；它只用于把比例换算成下表中的实际 `size`。

下表尺寸均满足宽和高可被 16 整除、单边范围与像素预算限制；4K 档会优先使用表内 4K 尺寸。

| 比例 | 1K | 2K | 4K |
| --- | --- | --- | --- |
| `1:1` | `1024x1024` | `2048x2048` | `2880x2880` |
| `3:2` | `1536x1024` | `2048x1360` | `3520x2336` |
| `2:3` | `1024x1536` | `1360x2048` | `2336x3520` |
| `4:3` | `1024x768` | `2048x1536` | `3312x2480` |
| `3:4` | `768x1024` | `1536x2048` | `2480x3312` |
| `5:4` | `1280x1024` | `2560x2048` | `3216x2576` |
| `4:5` | `1024x1280` | `2048x2560` | `2576x3216` |
| `16:9` | `1536x864` | `2048x1152` | `3840x2160` |
| `9:16` | `864x1536` | `1152x2048` | `2160x3840` |
| `2:1` | `2048x1024` | `2688x1344` | `3840x1920` |
| `1:2` | `1024x2048` | `1344x2688` | `1920x3840` |
| `3:1` | `1536x512` | `3072x1024` | `3840x1280` |
| `1:3` | `512x1536` | `1024x3072` | `1280x3840` |
| `21:9` | `2016x864` | `2688x1152` | `3840x1648` |
| `9:21` | `864x2016` | `1152x2688` | `1648x3840` |

## 请求示例

```json
{
    "model": "gpt-image-2-fast",
    "prompt": "海边小岛、灯塔、月光、飞鸟与远处船只，电影感光影",
    "aspect_ratio": "16:9",
    "image_size": "4k",
    "image_urls": [
        "https://example.com/reference.jpg"
    ],
    "quality": "medium"
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
| 400 | `billing_sku_unmatched` | 请求未命中可用计费规格 |
| 401 | `auth_failed` | API Key 缺失或无效 |
| 402 | `insufficient_points` | 点数余额不足 |
| 402 | `key_quota_exceeded` | 当前 API Key 点数额度不足 |
| 403 | `permission_denied` | 当前 API Key 无权调用该模型/应用 |
| 404 | `not_found` | 模型 / 应用 / 任务不存在 |
| 429 | `queue_limit_exceeded` | 排队任务已达上限 |
| 5xx | `server_error` | 服务异常，请稍后重试 |
