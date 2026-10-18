# 全能视频生成 · 提交任务

提交后保存平台 `task_id`，并通过本应用的查询接口读取任务状态和视频结果。

## 鉴权与路径

```http
POST /api/v1/apps/full_video/submit
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 顶层参数

| 参数 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `model` | string | 否 | `full-video` | 固定为 `full-video`。 |
| `content` | array | 是 | - | 多模态内容数组，必须至少有一个非空 `text` 项。 |
| `resolution` | string | 否 | `480P` | 输出分辨率：`480P`、`768P` 或 `2K`。 |
| `duration` | integer | 否 | `4` | 输出视频时长，支持 `4` 至 `15` 的整数秒。 |
| `ratio` | string | 否 | `16:9` | 视频画幅比例；图生视频和参考生成可使用 `adaptive`。 |
| `aigc_watermark` | boolean | 否 | `false` | 是否添加生成标识。 |
| `callback_url` | string | 否 | - | HTTPS 地址；任务终态时由平台通知。 |

## 计费说明

平台先按 `resolution` 命中分辨率 SKU，再以请求的 `duration` 作为秒数计量。价格由当前租户为对应 SKU 配置的售价决定：

| `resolution` | 计费规格 |
| --- | --- |
| `480P` | 480P 按秒 SKU |
| `768P` | 768P 按秒 SKU |
| `2K` | 2K 按秒 SKU |

提交时平台按 `duration × 当前 SKU 单价 + 超额参考图片附加点数` 冻结点数，完成后按该任务创建时的价格快照结算。默认前 5 张 `reference_image` 免费，超出部分每张加 20 点；实际免费数量和附加点数以当前应用配置为准。首帧和尾帧不计入参考图片附加项。不同租户可分别配置各分辨率 SKU 的售价；调用方应通过价格查询接口或租户后台读取当前实际售价。

## content 元素

媒体地址写入与类型同名的对象中，例如图片地址为 `image_url.url`。媒体 URL 必须能被服务访问。

| `type` | 必填字段 | 支持的 `role` | 限制与说明 |
| --- | --- | --- | --- |
| `text` | `text` | 不填写 | 每次请求至少一个非空文本项；所有文本项总长度最多 7000 字符。 |
| `image_url` | `image_url.url` | `first_frame`、`last_frame`、`reference_image` | 首帧最多 1 张，尾帧最多 1 张，参考图最多 9 张。首帧、尾帧可单独或组合使用。 |
| `video_url` | `video_url.url` | `reference_video` | 仅用于参考生成，最多 3 个。 |
| `audio_url` | `audio_url.url` | `reference_audio` | 仅用于参考生成，最多 3 个。 |

首帧/尾帧模式与参考媒体模式互斥：出现任意 `reference_image`、`reference_video` 或 `reference_audio` 时，不能同时出现 `first_frame` 或 `last_frame`。参考生成不能只传音频，至少需要 1 张参考图或 1 个参考视频。

## 场景与示例

### 文本生视频

```json
{
  "model": "full-video",
  "content": [
    {"type": "text", "text": "一只小狗在草地上奔跑，真实自然"}
  ],
  "resolution": "480P",
  "duration": 4,
  "ratio": "16:9",
  "aigc_watermark": false
}
```

### 首帧生视频

```json
{
  "model": "full-video",
  "content": [
    {"type": "text", "text": "一只小狗从画面左侧开心地跑向镜头，保持首帧小狗外观，真实自然，有环境音"},
    {"type": "image_url", "image_url": {"url": "https://example.com/first_frame.png"}, "role": "first_frame"}
  ],
  "resolution": "480P",
  "duration": 4,
  "ratio": "adaptive",
  "aigc_watermark": false
}
```

### 尾帧生视频

尾帧可以单独使用，也可以与首帧组合使用。

```json
{
  "model": "full-video",
  "content": [
    {"type": "text", "text": "一只小狗在草地上奔跑，最后定格成尾帧图片中的构图，真实自然，有轻快环境音"},
    {"type": "image_url", "image_url": {"url": "https://example.com/last_frame.png"}, "role": "last_frame"}
  ],
  "resolution": "480P",
  "duration": 4,
  "ratio": "adaptive",
  "aigc_watermark": false
}
```

### 首尾帧生视频

```json
{
  "model": "full-video",
  "content": [
    {"type": "text", "text": "一只小狗从首帧位置跑到尾帧位置，动作连贯，镜头稳定，真实自然，有环境音"},
    {"type": "image_url", "image_url": {"url": "https://example.com/first_frame.png"}, "role": "first_frame"},
    {"type": "image_url", "image_url": {"url": "https://example.com/last_frame.png"}, "role": "last_frame"}
  ],
  "resolution": "480P",
  "duration": 4,
  "ratio": "adaptive",
  "aigc_watermark": false
}
```

### 多模态参考生成

```json
{
  "model": "full-video",
  "content": [
    {"type": "text", "text": "参考 <Picture 1> 的小狗形象、参考 <Video 1> 的运动节奏、参考 <Audio 1> 的声音氛围，生成一只小狗在画面中轻快奔跑的短视频"},
    {"type": "image_url", "image_url": {"url": "https://example.com/ref_image.png"}, "role": "reference_image"},
    {"type": "video_url", "video_url": {"url": "https://example.com/ref_video.mp4"}, "role": "reference_video"},
    {"type": "audio_url", "audio_url": {"url": "https://example.com/ref_audio.wav"}, "role": "reference_audio"}
  ],
  "resolution": "480P",
  "duration": 4,
  "ratio": "adaptive",
  "aigc_watermark": false
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
    "app": "full_video",
    "api": "submit",
    "actual_points": 0,
    "created_at": "2026-08-04T00:00:00Z"
  }
}
```

`data.task_id` 是平台任务 ID。提交成功后保存它，并调用“全能视频生成 · 查询任务”接口。

## 常见错误

| HTTP | code | 说明 |
| --- | --- | --- |
| 400 | `invalid_request` | `content` 结构、媒体数量、文本长度、时长或模式组合不合法。 |
| 401 | `auth_failed` | API Key 缺失或无效。 |
| 402 | `insufficient_points` | 用户或租户点数不足。 |
| 403 | `permission_denied` | 当前 API Key 无权调用应用 API。 |
| 404 | `not_found` | 应用或 API 未上架、未配置价格或不存在。 |
| 429 | `queue_limit_exceeded` | 应用排队任务已达上限。 |