---
doc_type: application_api
app_id: 12
app_code: happy_horse
ai_app_api_id: 36
api_code: create
sync_target: ai_developer_doc
sync_status: ready
existing_doc_id: 95
existing_slug: happy-horse-create-a36
existing_title: "Happy Horse · 创建任务"
---

# Happy Horse · 创建任务

与 **`submit`** 使用**完全相同**的请求体格式（DashScope 风格：`model`、`prompt`、`resolution`、`duration`、`ratio`、`seed`、`watermark`、`media`）。区别在于本接口 **固定走 happyhorse 上游渠道**（`api-gateway` / `STS` → `OSS` → **`/api/v2/tasks`**），**不参与**租户在 **`submit`** 上选择的 DashScope 渠道。

提交后为**异步**：响应返回平台 `task_id`，请通过 **`query`** 或 **`GET /api/v1/tasks/{task_id}`** 查询进度与结果。

平台须在应用管理中配置 **`api_secret`**（aorizon Bearer token，用于happyhorse 渠道）；可选用 **`extra_config`** 覆盖默认网关等业务参数。若未按需配置 happyhorse 渠道，调用会返回与 channel 校验一致的错误。**`submit`** 侧使用的 **`api_key`**（DashScope）仍用于 DashScope **`submit`** 路径，互不替代。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | Happy Horse |
| 应用编码 | `happy_horse` |
| API 名称 | 创建任务 |
| API 编码 | `create` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/happy_horse/create` |
| 上游渠道 | 固定 **`happyhorse`**（aorizon） |
| 调用模式 | 异步（返回 `task_id`，可 `query` 或 `GET /api/v1/tasks/{task_id}` 查询） |
| 计费方式 | **按参数档位矩阵**：与 **`submit`** 同为 `pricing_matrix`，但 **独立维护**，可按 `resolution` / 档位与 **`submit` 差异化**计价（仍为「点数/秒 × 目标时长」类语义） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

开放平台调用仍使用租户的 **平台 API Key**；服务端在锁死 happyhorse 渠道时，会使用应用配置中的 **`api_secret`** 访问上游。

## 请求路径

```http
POST /api/v1/apps/happy_horse/create
```

## 能力与 `model` / `media`

| 场景 | `model` | `media` 要求 |
| --- | --- | --- |
| 文生视频 | `happyhorse-1.1-t2v` | 不要传图片、视频 |
| 单图首帧图生 | `happyhorse-1.1-i2v` | 恰好一张 `image` |
| 多参考图生成 | `happyhorse-1.1-r2v` | 1～9 张 `image`（建议至少两张），不要混入视频 |

**本接口不支持** `happyhorse-1.1-video-edit`。若传入该模型，渠道会返回 `invalid_request`。其它约定与 **`submit`** 文档一致：**所有参考素材**放在 **`media`** 数组，格式为 `{ "url": "https://...", "type": "image" }`（本渠道 **`create`** 仅处理图片链路，请勿传 video-edit 或视频素材）。

## 业务参数

与 **`submit`** 相同字段语义，字段表请直接对齐 [提交任务文档](./api-34-submit.md)。**必填/可选、`ratio`/`duration` 范围**与 **`submit`** 一致。**差异要点**：

- **`model`**：仅允许 **`happyhorse-1.1-t2v`**、**`happyhorse-1.1-i2v`**、**`happyhorse-1.1-r2v`**；勿传 **`happyhorse-1.1-video-edit`**。
- **`seed` / `watermark`**：可传；happyhorse aorizon 流水线当前可作占位，是否透传以实际上线为准（与迁移脚本说明一致）。

## 请求示例

**文生视频**

```json
{
    "model": "happyhorse-1.1-t2v",
    "resolution": "720P",
    "duration": 5,
    "ratio": "16:9",
    "seed": 42,
    "watermark": true,
    "prompt": "都市夜景，车流水灯，中景推进镜头，对白自然"
}
```

**单图首帧**

```json
{
    "model": "happyhorse-1.1-i2v",
    "resolution": "720P",
    "duration": 5,
    "prompt": "一只猫在草地上奔跑",
    "media": [
        {
            "type": "image",
            "url": "https://example.com/first.png"
        }
    ]
}
```

**多参考图**

```json
{
    "model": "happyhorse-1.1-r2v",
    "resolution": "720P",
    "duration": 5,
    "ratio": "16:9",
    "prompt": "保持人物与服饰一致，镜头缓慢推进",
    "media": [
        {
            "type": "image",
            "url": "https://example.com/ref1.jpg"
        },
        {
            "type": "image",
            "url": "https://example.com/ref2.jpg"
        }
    ]
}
```

## 成功响应

```json
{
    "task_id": "task_xxxxxxxxxxxxxxxx",
    "status": "pending",
    "app": "happy_horse",
    "api": "create",
    "created_at": "2026-01-01T00:00:00Z"
}
```

实际字段以开放平台统一 **`AbstractAppDriver`** / 任务入库封装为准，可能包含等价别名；**`task_id`** 可用于 **`query`**。

## 常见错误

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `invalid_request` | 缺少必填项、`model` 为 video-edit、媒体数量与模型不匹配、happhorse 流水线校验失败等 |
| 400 | `billing_params_unmatched` | `resolution` + `duration`（及矩阵维度）未命中 **`create`** 侧计费矩阵 |
| 401 | `auth_failed` | 平台 API Key 或上游 `api_secret` 无效 |
| 402 | `insufficient_points` | 点数不足 |
| 404 | `not_found` | 应用未对租户开放或未配置 **`create`** 价格 |
| 502 | `app_not_configured` | happyhorse 渠道所需 **`api_secret`** 或未配置可用的上游参数 |

详见 **`submit`** 页「常见错误」；第三方业务错误仍以实际 **`error`** 响应为准。

## 说明

- **`create`** 与 **`submit`** 并行存在：前者锁死happyhorse链路，计费矩阵**独立**。租户/平台在应用后台可为两者分别编辑「矩阵定价」。
- 更完整的自动化正文可由 `php think ai_developer_doc:sync_from_source --app-id=12 --only=app --force --publish` 从 `ai_app_api` 元数据生成或覆盖库存副本。
