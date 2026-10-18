# Seedance 2.0 Pro · 提交生视频

提交视频生成任务。接口返回平台 `task_id`，请使用查询接口或统一任务查询接口获取结果。

## 基本信息

| 字段 | 内容 |
| --- | --- |
| 应用编码 | `seedance2_pro` |
| API 编码 | `create` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/seedance2_pro/create` |
| 调用模式 | 异步 |
| 计费方式 | 按输入时长计费，`duration × mode` 对应每秒 SKU 单价 |

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `prompt` | string | 是 | - | - | `一位职业女性在办公室自然口播，镜头稳定，不出现字幕和水印` | 视频生成提示词 |
| `mode` | string | 否 | `pro` | `pro` / `fast` | `pro` | 提交模式 |
| `image_urls` | array | 否 | - | 最多 9 张 | `["https://example.com/ref.png"]` | 参考图 URL 数组 |
| `audio_references` | array | 否 | - | 最多 3 段，总时长最多 15 秒 | `[{"url":"https://example.com/ref.wav","duration":5}]` | 音频参考数组 |
| `audio_urls` | array | 否 | - | 最多 3 段 | `["https://example.com/ref.wav"]` | 音频参考 URL 简写数组 |
| `aspect_ratio` | string | 否 | `adaptive` | `9:16` / `16:9` / `1:1` / `4:3` / `3:4` / `21:9` / `adaptive` | `9:16` | 视频画幅比例 |
| `duration` | integer | 否 | `5` | `4` 到 `15` | `5` | 生成视频时长，单位秒 |
| `callback_url` | string | 否 | - | HTTPS URL | `https://your-domain.com/webhook/video` | 任务完成或失败时由平台主动通知 |

## 请求示例

```json
{
  "mode": "pro",
  "prompt": "一位职业女性在办公室自然口播，镜头稳定，不出现字幕和水印",
  "image_urls": ["https://example.com/ref.png"],
  "aspect_ratio": "9:16",
  "duration": 5
}
```

## 成功响应

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "status": "processing",
    "app": "seedance2_pro",
    "api": "create",
    "frozen_points": 500,
    "actual_points": 0
  }
}
```

## 常见错误码

| code | 含义 |
| --- | --- |
| `invalid_request` | 参数缺失或格式错误 |
| `insufficient_points` | 点数余额不足 |
| `permission_denied` | 当前 API Key 无权调用 |
| `queue_limit_exceeded` | 排队任务已达上限 |
| `task_failed` | 任务生成失败 |