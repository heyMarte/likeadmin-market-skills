# 语音TTS · 语音转文字

将语音音频识别转写为文本，支持文件上传或音频URL

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 语音TTS |
| 应用编码 | `voice_tts` |
| API 名称 | 语音转文字 |
| API 编码 | `stt` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/voice_tts/stt` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.0150 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/voice_tts/stt
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `language` | string | 否 | — | — | — | 识别语言，不传则自动检测 |
| `audio_url` | string | 否 | — | — | — | 音频文件URL（与文件上传二选一） |
| `ignore_timestamps` | boolean | 否 | — | — | — | 是否忽略精确时间戳，默认 true |

## 请求示例

```json
{
    "language": "string",
    "audio_url": "string",
    "ignore_timestamps": true
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
