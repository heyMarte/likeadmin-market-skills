# Grok 视频生成

Grok 视频生成提供异步视频创作能力。创建任务后取得平台 `task_id`，通过查询接口或任务终态回调获取视频结果。

## 认证

所有请求使用平台 API Key：`Authorization: Bearer YOUR_API_KEY`。请求体使用 `Content-Type: application/json`。

## 调用流程

1. 调用创建任务接口，获得平台 `task_id`。
2. 使用查询接口轮询任务状态，或在创建时传入 `callback_url` 接收终态通知。
3. 任务成功后，从 `video_url` 获取视频。

## 接口与计费

- 创建任务：`POST /api/v1/apps/grok_video/submit`
- 查询任务：`GET /api/v1/apps/grok_video/query?task_id=...`
- 快速生成按次计费；标准生成按输出秒计费。实际扣点以创建任务时的价格快照为准。