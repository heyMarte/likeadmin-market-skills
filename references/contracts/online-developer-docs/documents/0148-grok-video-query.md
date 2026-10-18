# Grok 视频生成 - 查询任务

`GET /api/v1/apps/grok_video/query?task_id=task_xxxxxxxxxxxx`

查询接口只读取平台任务状态，不扣点。

## 请求头

| 名称 | 值 |
| --- | --- |
| `Authorization` | `Bearer YOUR_API_KEY` |

## 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `task_id` | string | 是 | 创建任务接口返回的平台任务 ID |

## 状态说明

| 状态 | 说明 |
| --- | --- |
| `pending` | 任务已创建，等待处理 |
| `processing` | 任务正在生成 |
| `succeeded` | 任务成功，返回 `video_url` |
| `failed` | 任务失败，返回错误信息 |

## 成功响应示例

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "result": {
      "task_id": "task_xxxxxxxxxxxx",
      "status": "succeeded",
      "video_url": "https://example.com/video.mp4"
    },
    "usage": {
      "points_cost": 0,
      "actual_points": 0
    }
  }
}
```

只有创建该任务的同一租户可以查询任务结果。