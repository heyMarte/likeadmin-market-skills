# Seedance 2.0 Pro · 查询生视频

按平台 `task_id` 查询任务状态与结果。

## 基本信息

| 字段 | 内容 |
| --- | --- |
| 应用编码 | `seedance2_pro` |
| API 编码 | `query` |
| 请求方式 | `GET` |
| 请求路径 | `/api/v1/apps/seedance2_pro/query` |
| 调用模式 | 同步 |
| 计费方式 | 免费查询 |

## 请求示例

```http
GET /api/v1/apps/seedance2_pro/query?task_id=task_xxxxxxxxxxxx
```

## 完成响应

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "task_id": "task_xxxxxxxxxxxx",
    "status": "completed",
    "urls": ["https://example.com/output.mp4"],
    "result": ["https://example.com/output.mp4"],
    "actual_points": 500
  }
}
```

处理中时 `status` 为 `processing`；失败时返回平台错误码与安全错误文案。