---
doc_type: application_api
app_id: 1
app_code: voice_tts
ai_app_api_id: 2
api_code: tts
sync_target: ai_developer_doc
sync_status: pending_review
existing_doc_id: 8
existing_parent_id: 64
existing_slug: voice-tts-tts
existing_title: "语音TTS · 文字转语音"
---

# 语音TTS · 文字转语音

将文本同步合成为语音音频文件（适合短文本，500字符内），直接返回音频URL

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 语音TTS |
| 应用编码 | `voice_tts` |
| API 名称 | 文字转语音 |
| API 编码 | `tts` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/voice_tts/tts` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.0200 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/voice_tts/tts
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `text` | string | 是 | — | — | — | 待合成文本，同步接口建议不超过 500 字符 |
| `model` | string | 否 | — | `s1` / `s2-pro` | — | TTS 模型，可选 s1 / s2-pro，默认 s2-pro |
| `top_p` | number | 否 | — | `0` ~ `1` | — | Top-P 采样，0~1，默认 0.7 |
| `format` | string | 否 | — | `wav` / `pcm` / `mp3` / `opus` | — | 输出格式：wav / pcm / mp3 / opus，默认 mp3 |
| `latency` | string | 否 | — | `low` / `normal` / `balanced` | — | 延迟模式：low / normal / balanced |
| `prosody` | object | 否 | — | — | — | 语调控制对象：speed、volume、normalize_loudness |
| `normalize` | boolean | 否 | — | — | — | 文本规范化，默认 true |
| `mp3_bitrate` | integer | 否 | — | `64` / `128` / `192` | — | MP3 比特率：64 / 128 / 192 |
| `sample_rate` | integer | 否 | — | — | — | 采样率按格式限制 |
| `temperature` | number | 否 | — | `0` ~ `1` | — | 生成温度，0~1，默认 0.7 |
| `chunk_length` | integer | 否 | — | `100` ~ `300` | — | 文本分块长度，范围 100~300，默认 300 |
| `opus_bitrate` | integer | 否 | — | `-1000` / `24` / `32` / `48` / `64` | — | Opus 比特率：-1000 / 24 / 32 / 48 / 64 |
| `reference_id` | string | 否 | — | — | — | 音色模型ID。单说话人传 string；多说话人模式可传 string[]（仅 s2-pro） |
| `max_new_tokens` | integer | 否 | — | — | — | 每个分块最多生成音频 token，默认 1024 |
| `min_chunk_length` | integer | 否 | — | `0` ~ `100` | — | 最小分块长度，范围 0~100，默认 50 |
| `repetition_penalty` | number | 否 | — | — | — | 重复惩罚，默认 1.2 |
| `early_stop_threshold` | number | 否 | — | `0` ~ `1` | — | 提前停止阈值，范围 0~1，默认 1 |
| `condition_on_previous_chunks` | boolean | 否 | — | — | — | 是否利用前一段音频作为上下文，默认 true |

## 请求示例

```json
{
    "text": "string",
    "model": "s1",
    "top_p": 1,
    "format": "mp3",
    "latency": "low",
    "prosody": {},
    "normalize": true,
    "mp3_bitrate": "64",
    "sample_rate": 1,
    "temperature": 1,
    "chunk_length": 1,
    "opus_bitrate": "-1000",
    "reference_id": "string",
    "max_new_tokens": 1,
    "min_chunk_length": 1,
    "repetition_penalty": 1,
    "early_stop_threshold": 1,
    "condition_on_previous_chunks": true
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
