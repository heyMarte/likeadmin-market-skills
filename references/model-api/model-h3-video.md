---
doc_type: model_api
model_code: h3-video
channel_code: minimax
type_code: video
sync_target: ai_developer_doc
sync_status: synced
existing_doc_id: 149
existing_parent_id: 0
existing_slug: model-h3-video
existing_title: H3 视频生成
---

# H3 视频生成

H3 视频生成使用统一异步任务接口。支持文本生视频、首帧/尾帧/首尾帧生视频，以及图片、视频、音频的多模态参考生成。成功后直接返回视频下载链接，不进行转存；请在链接有效期内下载。

## 鉴权与路径

```http
POST /api/v1/tasks
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

提交成功后通过 `GET /api/v1/tasks/{task_id}` 查询平台任务结果，也可以传入 `callback_url` 接收平台的终态通知。

## 顶层参数

| 参数 | 类型 | 必填 | 可选值 | 说明 |
| --- | --- | --- | --- | --- |
| `model` | string | 是 | `h3-video` | 平台模型编码。 |
| `content` | array | 是 | 见下文 | 多模态内容数组，必须有且仅需至少一个非空 `text` 项。 |
| `resolution` | string | 是 | `2K` | 输出分辨率。 |
| `duration` | integer | 是 | `4` 至 `15` | 输出视频时长，单位秒。 |
| `ratio` | string | 是 | `adaptive`、`21:9`、`16:9`、`4:3`、`1:1`、`3:4`、`9:16` | 纯文本生成不可使用 `adaptive`。 |
| `callback_url` | string | 否 | HTTPS URL | 平台完成或失败后通知的地址。 |

## content 元素

每个元素使用 `type` 表示内容类型。媒体元素的地址写入与类型同名的对象中，例如图片是 `image_url.url`。`role` 用于表明媒体用途。

| `type` | 必填字段 | 支持的 `role` | 说明 |
| --- | --- | --- | --- |
| `text` | `text` | 不填写 | 提示词；每次请求必须至少有一个非空文本项。 |
| `image_url` | `image_url.url` | `first_frame`、`last_frame`、`reference_image` | 首帧最多 1 张、尾帧最多 1 张、参考图最多 9 张。未写 `role` 的单张图片按首帧处理；`last_frame` 必须与 `first_frame` 成对出现。 |
| `video_url` | `video_url.url` | `reference_video` | 仅用于参考生成，最多 3 个；每段 2 至 15 秒，总时长不超过 15 秒。 |
| `audio_url` | `audio_url.url` | `reference_audio` | 仅用于参考生成，最多 3 个；每段 2 至 15 秒，总时长不超过 15 秒。 |

媒体 URL 需要能被服务访问。图片支持 JPG、JPEG、PNG、WEBP、HEIC、HEIF；参考视频支持 MP4、MOV；参考音频支持 WAV、MP3。

## 场景与示例

### 文本生视频

`content` 只包含一个 `text` 项，`ratio` 必须是具体比例。

```json
{
  "model": "h3-video",
  "content": [
    {"type": "text", "text": "清晨的海边，镜头缓慢推近灯塔，海鸥掠过天空。"}
  ],
  "resolution": "2K",
  "duration": 5,
  "ratio": "16:9"
}
```

### 首帧生视频

首帧使用 `first_frame`；只传一张首帧时 `role` 可省略。输入图片会决定画幅，建议传 `adaptive`。即使传入其他合法比例，系统也会按 `adaptive` 处理。

```json
{
  "model": "h3-video",
  "content": [
    {"type": "text", "text": "人物转身看向窗外，窗帘被微风吹动。"},
    {"type": "image_url", "image_url": {"url": "https://example.com/first.png"}, "role": "first_frame"}
  ],
  "resolution": "2K",
  "duration": 5,
  "ratio": "adaptive"
}
```

### 首尾帧生视频

```json
{
  "model": "h3-video",
  "content": [
    {"type": "text", "text": "城市从白昼平滑过渡到夜晚，车流逐渐亮起。"},
    {"type": "image_url", "image_url": {"url": "https://example.com/day.png"}, "role": "first_frame"},
    {"type": "image_url", "image_url": {"url": "https://example.com/night.png"}, "role": "last_frame"}
  ],
  "resolution": "2K",
  "duration": 8,
  "ratio": "adaptive"
}
```

### 多模态参考生成

可组合参考图片、参考视频和参考音频；参考音频不能单独使用，必须至少同时有一个参考图片或参考视频。

```json
{
  "model": "h3-video",
  "content": [
    {"type": "text", "text": "角色以参考音色在街头自然说话，镜头保持稳定。"},
    {"type": "image_url", "image_url": {"url": "https://example.com/character.png"}, "role": "reference_image"},
    {"type": "video_url", "video_url": {"url": "https://example.com/motion.mp4"}, "role": "reference_video"},
    {"type": "audio_url", "audio_url": {"url": "https://example.com/voice.mp3"}, "role": "reference_audio"}
  ],
  "resolution": "2K",
  "duration": 5,
  "ratio": "9:16"
}
```

首尾帧模式与多模态参考模式互斥：出现任意 `reference_image`、`reference_video` 或 `reference_audio` 时，不能同时出现 `first_frame` 或 `last_frame`。

## 提交与查询响应

创建成功：

```json
{
  "code": 1,
  "msg": "success",
  "data": {"task_id": "task_xxxxxxxxxxxx", "status": "processing"}
}
```

查询成功：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "status": "completed",
    "result": {
      "video_url": "https://video-product.example.com/output.mp4",
      "duration": 5,
      "ratio": "16:9"
    }
  }
}
```

`video_url` 为生成服务返回的原始下载链接。平台不转存该文件。

## 计费

当前可调用分辨率为 `2K`，按最终生成视频的输出时长计费，默认单价为 `80` 点/输出秒（约 `¥0.80`/秒）。提交时会按 `duration` 冻结预估点数，完成后按实际输出秒数结算；不同租户的售价以其 SKU 配置为准。
