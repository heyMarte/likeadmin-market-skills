# 语音TTS · 文字转语音（Live·异步）

WebSocket 流式服务，长文本异步合成；返回 task_id，由 fish_tts:worker 执行

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | 语音TTS |
| 应用编码 | `voice_tts` |
| API 名称 | 文字转语音（Live·异步） |
| API 编码 | `tts_live` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/voice_tts/tts_live` |
| 调用模式 | 异步 |
| 计费方式 | 按字符（0.020000 元 / 千字节） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/voice_tts/tts_live
```

该接口为异步任务，提交后返回 `task_id`，请通过 `GET /api/v1/tasks/{task_id}` 查询结果，或在请求中传 `callback_url` 接收完成回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `text` | string | 是 | — | — | — | 待合成文本（WebSocket 流式服务，适合长文本） |
| `model` | string | 否 | — | `s1` / `s2-pro` | — | TTS 模型：s1 / s2-pro，默认 s2-pro |
| `top_p` | number | 否 | — | — | — | Top-P |
| `format` | string | 否 | — | `wav` / `pcm` / `mp3` / `opus` | — | wav / pcm / mp3 / opus，默认 mp3 |
| `latency` | string | 否 | — | `low` / `normal` / `balanced` | — | low / normal / balanced |
| `prosody` | object | 否 | — | — | — | 语调：speed、volume、normalize_loudness（仅 s2-pro） |
| `normalize` | boolean | 否 | — | — | — | 文本规范化，默认 true |
| `mp3_bitrate` | integer | 否 | — | `64` / `128` / `192` | — | MP3 比特率：64 / 128 / 192 |
| `sample_rate` | integer | 否 | — | — | — | 采样率 |
| `temperature` | number | 否 | — | — | — | 生成温度 |
| `callback_url` | string | 否 | — | `GET` / `api` / `v1` / `tasks` | — | 回调 URL；不传则 GET /api/v1/tasks/{task_id} 轮询 |
| `chunk_length` | integer | 否 | — | `100` ~ `300` | — | 文本分块长度 100~300，默认 300 |
| `opus_bitrate` | integer | 否 | — | — | — | Opus 比特率 |
| `reference_id` | string | 否 | — | — | — | 音色模型ID。单说话人传 string；多说话人可传 string[]（仅 s2-pro） |
| `max_new_tokens` | integer | 否 | — | — | — | 每分块最大音频 token |
| `min_chunk_length` | integer | 否 | — | `0` ~ `100` | — | 最小分块长度 0~100 |
| `repetition_penalty` | number | 否 | — | — | — | 重复惩罚 |
| `early_stop_threshold` | number | 否 | — | `0` ~ `1` | — | 提前停止阈值，0~1 |
| `condition_on_previous_chunks` | boolean | 否 | — | — | — | 是否利用前一段音频作为上下文 |

## 请求示例

```json
{
    "text": "string",
    "model": "s1",
    "top_p": 1,
    "format": "wav",
    "latency": "low",
    "prosody": {},
    "normalize": true,
    "mp3_bitrate": "64",
    "sample_rate": 1,
    "temperature": 1,
    "callback_url": "GET",
    "chunk_length": 1,
    "opus_bitrate": 1,
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
