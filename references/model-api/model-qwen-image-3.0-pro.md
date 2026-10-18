---
doc_type: model_api
model_code: qwen-image-3.0-pro
channel_code: dashscope_compatible
type_code: image
sync_target: ai_developer_doc
sync_status: pending_review
---

# Qwen Image 3.0 Pro

图片生成模型，支持文生图和参考图编辑。每个任务仅生成一张图片；完成后返回临时图片链接，平台不转存。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | Qwen Image 3.0 Pro |
| 模型编码 | `qwen-image-3.0-pro` |
| 模型类型 | 图片生成（image） |
| 调用方式 | `POST /api/v1/tasks` |
| 调用模式 | 异步 |
| 计费方式 | 请以价格查询接口为准 |
| 最大参考图片数 | 3 |

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

该模型为异步任务。提交后返回平台 `task_id`，通过 `GET /api/v1/tasks/{task_id}` 查询结果；也可传入 `callback_url` 接收平台终态回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `model` | string | 是 | — | `qwen-image-3.0-pro` | — | 模型编码。 |
| `channel` | string | 否 | — | `dashscope_compatible` | — | 指定渠道；不传时按当前租户可用路由选择。 |
| `input.messages` | array | 是 | — | 仅 1 条消息 | — | 消息数组，仅支持一条 `role=user` 的消息。 |
| `input.messages[0].content` | array | 是 | — | 1 个文本项，附加 0 至 3 个图片项 | — | 文生图只传文本项；参考图编辑传一个文本项和 1 至 3 个图片项。 |
| `parameters.n` | integer | 是 | — | `1` | `1` | 每个任务生成图片数，固定为 `1`。 |
| `parameters.size` | string | 否 | 服务自动选择 | 总像素范围 `512*512` 至 `2048*2048`；比例 1:8 至 8:1 | `1024*1024` | 输出尺寸，格式为 `宽*高`。 |
| `parameters.prompt_extend` | boolean | 否 | `true` | `true` / `false` | `true` | 是否启用提示词优化。 |
| `parameters.prompt_extend_mode` | string | 否 | `direct` | `direct` / `agent` | `direct` | 参考图编辑仅支持 `direct`。 |
| `parameters.negative_prompt` | string | 否 | — | — | `模糊，低质量` | 反向提示词。 |
| `parameters.seed` | integer | 否 | — | `0` 至 `2147483647` | `12345` | 随机种子。 |
| `parameters.watermark` | boolean | 否 | `false` | `true` / `false` | `false` | 是否添加水印。 |
| `callback_url` | string | 否 | — | HTTPS URL | `https://example.com/hook` | 任务进入终态时的平台回调地址。 |

参考图使用 `{"image": "<图片 URL 或 data:image Base64>"}`。支持 JPG、JPEG、PNG、BMP、TIFF、WEBP、GIF 格式；公网 URL 在任务执行期间必须可访问。

## 请求示例

### 文生图

```json
{
  "model": "qwen-image-3.0-pro",
  "input": {
    "messages": [
      {
        "role": "user",
        "content": [
          {"text": "窗边的橘猫，清晨阳光，写实摄影，温暖自然色调"}
        ]
      }
    ]
  },
  "parameters": {
    "n": 1,
    "size": "1024*1024",
    "prompt_extend": true,
    "watermark": false
  }
}
```

### 参考图编辑

```json
{
  "model": "qwen-image-3.0-pro",
  "input": {
    "messages": [
      {
        "role": "user",
        "content": [
          {"image": "https://example.com/reference.png"},
          {"text": "保留主体轮廓，改成雨后霓虹街景的电影海报风格"}
        ]
      }
    ]
  },
  "parameters": {
    "n": 1,
    "size": "1024*1536",
    "prompt_extend_mode": "direct",
    "watermark": false
  },
  "callback_url": "https://example.com/hook"
}
```

## 成功响应

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "status": "pending",
    "model": "qwen-image-3.0-pro",
    "created_at": "2026-08-12T12:00:00Z"
  }
}
```

### 查询任务结果

```http
GET /api/v1/tasks/{task_id}
```

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "status": "completed",
    "result": {
      "results": [
        {"url": "https://example.com/result.png", "type": "image"}
      ]
    },
    "usage": {
      "output_width": 1024,
      "output_height": 1024,
      "output_image_count": 1
    },
    "completed_at": "2026-08-12T12:00:30Z"
  }
}
```

`result.results[].url` 是临时图片链接，有效期约 24 小时。平台不转存，也不提供永久保存，请及时下载或保存。

## 失败响应

```json
{
  "code": 0,
  "msg": "请求参数不合法",
  "data": null
}
```

### 错误说明

| 场景 | 说明 |
| --- | --- |
| 参数校验失败 | `input.messages` 不是单条用户消息、文本项数量不为 1、参考图超过 3 张、`n` 不为 1，或尺寸、种子不在支持范围内。 |
| 参考图编辑限制 | 传入参考图时，`prompt_extend_mode` 不能为 `agent`。 |
| 任务失败 | 查询结果 `status=failed`，`msg` 返回安全的失败说明。 |
| 权限或余额不足 | 请检查 API Key 权限、当前模型上架状态和账户余额。 |

## 价格查询

调用前请通过 `GET /api/v1/pricing?type=model&model=qwen-image-3.0-pro` 查询有效价格。实际扣费以任务创建时的价格快照为准。
