# 语音TTS · 克隆音色

上传参考音频或提供音频URL，创建专属语音音色模型

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 语音TTS |
| 应用编码 | `voice_tts` |
| API 名称 | 克隆音色 |
| API 编码 | `clone_voice` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/voice_tts/clone_voice` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.0100 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/voice_tts/clone_voice
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `tags` | array | 否 | — | — | — | 模型标签数组 |
| `texts` | array | 否 | — | — | — | 与音频对应的文本数组；不传时自动ASR |
| `title` | string | 是 | — | — | — | 音色名称 |
| `audio_url` | string | 否 | — | `mp3` / `wav` / `ogg` / `flac` | — | 参考音频URL（与上传文件二选一），支持 mp3/wav/ogg/flac |
| `visibility` | string | 否 | — | `public` / `unlist` / `private` | — | 可见性：public / unlist / private，平台默认 private |
| `description` | string | 否 | — | — | — | 音色描述 |
| `enhance_audio_quality` | boolean | 否 | — | — | — | 是否增强音频质量，默认 false |

## 请求示例

```json
{
    "tags": [],
    "texts": [],
    "title": "string",
    "audio_url": "mp3",
    "visibility": "private",
    "description": "string",
    "enhance_audio_quality": true
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
