# Omni-Flash-Ext 视频生成 · 创建任务

提交视频生成任务。平台会创建异步任务并返回 `task_id`，客户端可通过查询接口轮询结果，也可传入 `callback_url` 接收任务完成或失败通知。

## 基本信息

| 字段 | 内容 |
| --- | --- |
| 应用编码 | `omni_flash_ext` |
| API 编码 | `create` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/omni_flash_ext/create` |
| 调用模式 | 异步 |
| 计费方式 | 按分辨率与生成时长矩阵计费 |

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| `prompt` | string | 是 | - | - | 视频画面、动作、镜头与风格描述 |
| `duration` | integer | 否 | `6` | `4` / `6` / `8` / `10` | 生成视频时长，单位秒 |
| `resolution` | string | 否 | `720p` | `720p` / `1080p` / `4k` | 输出分辨率，同时参与计费矩阵匹配 |
| `aspect_ratio` | string | 否 | `16:9` | `16:9` / `9:16` / `1:1` | 视频画幅比例 |
| `size` | string | 否 | - | `16:9` / `9:16` / `1:1` | 画幅比例兼容字段；未传 `aspect_ratio` 时使用 |
| `image_urls` | array | 否 | - | 1 张或 3 张 | 参考图片 URL 列表 |
| `callback_url` | string | 否 | - | HTTPS URL | 任务完成或失败时的平台回调地址 |

## 请求示例

```json
{
  "prompt": "一只小猫在雨后的霓虹街道上奔跑，电影感镜头，细节清晰",
  "duration": 6,
  "resolution": "720p",
  "aspect_ratio": "16:9"
}
```

## 成功响应

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "status": "queued",
    "app": "omni_flash_ext",
    "api": "create",
    "frozen_points": 57.12
  }
}
```

## 常见错误码

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `missing_prompt` | 未传视频描述 |
| 400 | `invalid_duration` | `duration` 不在支持范围内 |
| 400 | `invalid_resolution` | `resolution` 不在支持范围内 |
| 400 | `invalid_image_urls` | 参考图片数量不符合要求 |
| 402 | `insufficient_points` | 点数余额不足 |
| 403 | `permission_denied` | 当前 API Key 无权调用应用接口 |
| 429 | `queue_limit_exceeded` | 排队任务已达上限 |