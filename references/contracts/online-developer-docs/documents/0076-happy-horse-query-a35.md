# Happy Horse · 查询任务

根据 **`submit`** 或 **`create`** 返回的**平台** `task_id` 查询任务状态与结果。本接口为**同步**调用，不产生新的计费。

任务完成后，结果中可包含 **`video_url`**（及平台转存后的展示字段，与全站任务查询一致）。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | Happy Horse |
| 应用编码 | `happy_horse` |
| API 名称 | 查询任务 |
| API 编码 | `query` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/happy_horse/query` |
| 调用模式 | 同步 |
| 计费方式 | 一般按次/固定点数（以租户后台配置为准，可与 0 点查询一致） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/happy_horse/query
```

## 业务参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `task_id` | string | 是 | `submit` 成功返回的平台任务 id（`task_…`） |

## 请求示例

```json
{
    "task_id": "task_xxxxxxxxxxxxxxxx"
}
```

## 响应说明

- **`status` 为 `processing`（或等价进行中状态）**：任务仍在处理，可间隔轮询。
- **`status` 为 `completed`（或等价成功状态）**：处理完成，响应内带有可播放地址等字段（如 `video_url`，具体以平台任务结果与转存策略为准）。
- **`error`**：任务失败或参数错误时返回，含 `code` / `message`。

示例（进行中，字段名以实际接口为准）：

```json
{
    "status": "processing",
    "task_id": "task_xxxxxxxxxxxxxxxx"
}
```

示例（成功，简化示意）：

```json
{
    "status": "completed",
    "task_id": "task_xxxxxxxxxxxxxxxx",
    "video_url": "https://example.com/output.mp4"
}
```

若配置了 **`callback_url`**，任务终态也会按平台统一规则推送，不必依赖轮询。

## 常见错误

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `invalid_request` | 缺少或非法的 `task_id` |
| 404 | `task_not_found` | 任务不存在或不属于当前租户 |
| 404 | `not_found` | 应用未上架或价格未配 |

## 说明

- 与异步 `submit` 联用时，可轮询本接口；亦可使用 **`GET /api/v1/tasks/{task_id}`**，与全站任务查询行为对齐（含转存后的展示 URL）。
- 由 `php think ai_developer_doc:sync_from_source --app-id=12 --only=app --force --publish` 可据 `ai_app_api` 重生成本页在库中正文。
