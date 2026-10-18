## 全驱动数字人 - 查询任务

`POST /api/v1/apps/image_human/query`

根据提交接口返回的平台任务 ID 查询任务状态和结果。

### 请求参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `task_id` | string | 是 | 平台任务 ID，格式通常为 `task_xxxxxxxxxxxx`。 |

### 请求示例

```json
{
  "task_id": "task_xxxxxxxxxxxx"
}
```

### 运行中返回示例

```json
{
  "task_id": "task_xxxxxxxxxxxx",
  "status": "running"
}
```

### 完成返回示例

```json
{
  "task_id": "task_xxxxxxxxxxxx",
  "status": "done",
  "data": {
    "output_url": "https://example.com/output.mp4",
    "cover_url": "https://example.com/cover.jpg",
    "duration": 40.68
  }
}
```