---
doc_type: application_api
app_id: 13
app_code: wan
ai_app_api_id: 38
api_code: query
sync_target: ai_developer_doc
sync_status: published
existing_doc_id: 98
existing_slug: wan-query
existing_title: "Wan 视频生成 · 查询任务"
---

# Wan 视频生成 · 查询任务

按平台 `task_id` 查询 Wan 视频任务状态、视频 URL 和计费快照。本接口只读取平台任务表中的数据，不会直接请求上游服务；后台轮询任务会负责同步上游结果。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用编码 | `wan` |
| API 编码 | `query` |
| 请求方式 | `GET` |
| 请求路径 | `/api/v1/apps/wan/query` |
| 调用模式 | 同步 |
| 计费方式 | 免费查询 |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
```

## 请求路径

```http
GET /api/v1/apps/wan/query?task_id={task_id}
```

## 业务参数

| 参数 | 类型 | 必填 | 示例 | 说明 |
| --- | --- | --- | --- | --- |
| `task_id` | string | 是 | `task_xxxxxxxxxxxx` | 创建接口返回的平台任务 ID |

## 请求示例

```http
GET /api/v1/apps/wan/query?task_id=task_xxxxxxxxxxxx
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
    "model": "wan2.7",
    "resolution": "720p",
    "duration": 5,
    "video_url": "https://example.com/output.mp4",
    "data": {
      "video_url": "https://example.com/output.mp4"
    },
    "frozen_points": 717.1,
    "actual_points": 717.1
  }
}
```

字段说明：

| 字段 | 说明 |
| --- | --- |
| `status` | `processing` 表示处理中，`completed` 表示成功完成 |
| `video_url` | 生成视频地址，优先使用该字段播放或下载 |
| `data.video_url` | 与 `video_url` 等价，便于兼容不同客户端解析 |
| `frozen_points` | 创建任务时冻结的点数 |
| `actual_points` | 任务完成后实际扣除点数 |

建议客户端每 3 到 5 秒轮询一次；收到 `completed` 后停止轮询。若创建任务时传入 `callback_url`，也可以以回调为主、轮询为辅。

## 失败响应

```json
{
  "error": {
    "message": "任务不存在或不属于当前租户",
    "code": "task_not_found"
  }
}
```

### 常见错误码

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `missing_task_id` | 未传任务 ID |
| 401 | `auth_failed` | API Key 缺失或无效 |
| 404 | `task_not_found` | 任务不存在或不属于当前租户 |
| 5xx | `server_error` / `upstream_error` | 平台或上游服务异常 |
