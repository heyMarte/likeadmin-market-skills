# Omni-Flash-Ext 视频生成 · 查询任务

按平台 `task_id` 查询视频生成任务状态与结果。本接口只读取平台任务记录。

## 基本信息

| 字段 | 内容 |
| --- | --- |
| 应用编码 | `omni_flash_ext` |
| API 编码 | `query` |
| 请求方式 | `GET` |
| 请求路径 | `/api/v1/apps/omni_flash_ext/query` |
| 调用模式 | 同步 |
| 计费方式 | 免费查询 |

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
```

## 请求参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `task_id` | string | 是 | 创建接口返回的平台任务 ID |

## 请求示例

```http
GET /api/v1/apps/omni_flash_ext/query?task_id=task_xxxxxxxxxxxx
```

## 处理中响应

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "status": "processing"
  }
}
```

## 完成响应

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "status": "completed",
    "task_id": "task_xxxxxxxxxxxx",
    "resolution": "720p",
    "duration": 6,
    "video_url": "https://example.com/output.mp4",
    "data": {
      "video_url": "https://example.com/output.mp4"
    },
    "frozen_points": 57.12,
    "actual_points": 57.12
  }
}
```

## 常见错误码

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `missing_task_id` | 未传任务 ID |
| 404 | `task_not_found` | 任务不存在或不属于当前租户 |