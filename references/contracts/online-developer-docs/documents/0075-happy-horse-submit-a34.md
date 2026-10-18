# Happy Horse · 提交任务

创建文生视频、图生视频或多参考图生成任务。提交后为**异步**：响应返回平台 `task_id`，请通过本应用 **`query`** 或 **`GET /api/v1/tasks/{task_id}`** 查询进度与结果。

平台需在应用管理中配置可用的 **服务地址** 与 **API 密钥**；未配置时返回 `app_not_configured`。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | Happy Horse |
| 应用编码 | `happy_horse` |
| API 名称 | 提交任务 |
| API 编码 | `submit` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/happy_horse/submit` |
| 调用模式 | 异步（返回 `task_id`，可 `query` 或 `GET /api/v1/tasks/{task_id}` 查询） |
| 计费方式 | **按参数档位**：`resolution`（`720P` / `1080P`）与 **整数秒** `duration` 须同时命中后台 `pricing_rules` 中的一条（常见为 1～60 秒；总价为该档已配置的固定点数，相当于分档单价×秒数） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/happy_horse/submit
```

## 能力与 `model` / `media`

| 场景 | `model` | `media` 要求 |
| --- | --- | --- |
| 文生视频 | `happyhorse-1.1-t2v` | 不要传图片、视频 |
| 单图首帧图生 | `happyhorse-1.1-i2v` | 恰好一张 `image` |
| 多参考图生成 | `happyhorse-1.1-r2v` | 1～9 张 `image`（建议至少两张），不要混入视频 |
| 视频编辑 | `happyhorse-1.0-video-edit` | 恰好 1 个 `video`，可附 0～5 张 `image` |

`model` 决定生成能力，不需要再传 `mode`。平台兼容接收 `happyhorse-1.1-video-edit`，提交时会归一为百炼实际模型 `happyhorse-1.0-video-edit`。

所有参考素材统一放在 **`media`** 数组里，每项为 `{ "url": "https://...", "type": "image" }` 或 `{ "url": "https://...", "type": "video" }`；`type` 可省略，系统会按 URL 扩展名推断。图片建议使用 JPG/JPEG/PNG/BMP/WEBP，视频请使用公网可访问地址。

## 业务参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `model` | string | 是 | 能力代号。支持：`happyhorse-1.1-t2v`、`happyhorse-1.1-i2v`、`happyhorse-1.1-r2v`、`happyhorse-1.0-video-edit` |
| `prompt` | string | 是 | 画面、镜头、风格与对白等描述；最多 2500 字，不能为空 |
| `resolution` | string | 是 | 目标分辨率。支持：`720P`、`1080P`（也接受 `720p` / `1080p`，平台会归一） |
| `duration` | number | 是 | 目标生成时长（秒），整数。文生、图生、多参考支持 `3`～`15`；视频编辑时长由输入视频决定，但本平台仍按请求时长/矩阵计费 |
| `ratio` | string | 否 | 画幅比例。文生和多参考支持：`16:9`、`9:16`、`1:1`、`4:3`、`3:4`；单图首帧不需要传 |
| `seed` | number | 否 | 随机种子，支持 `0`～`2147483647` 的整数；不传则随机 |
| `watermark` | boolean | 否 | 是否添加水印。支持：`true`、`false`；不传默认 `true` |
| `audio_setting` | string | 否 | 视频编辑音频处理。支持：`auto`、`origin` |
| `media` | array | 否 | 统一参考素材数组。每项包含 `url`，可选 `type`（`image`/`video`）。文生不传；单图首帧传 1 张 `image`；多参考传 1～9 张 `image`；视频编辑传 1 个 `video`，可附 0～5 张 `image` |

### `media` 子字段

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `media[].url` | string | 是 | 素材 URL，需为公网可访问地址 |
| `media[].type` | string | 否 | 素材类型。支持：`image`、`video`；省略时按 URL 后缀推断 |

**按 `model` 的补充规则**：

| `model` | `media` 规则 | 其它规则 |
| --- | --- | --- |
| `happyhorse-1.1-t2v` | 不传 `media` | 支持 `ratio` / `duration` / `seed` / `watermark` |
| `happyhorse-1.1-i2v` | 恰好 1 张 `image` | 不需要传 `ratio`，画幅由首帧图决定 |
| `happyhorse-1.1-r2v` | 1～9 张 `image` | 支持 `ratio`；提示词中可用“图1、图2...”指代素材顺序 |
| `happyhorse-1.0-video-edit` | 恰好 1 个 `video`，可附 0～5 张 `image` | 支持 `seed` / `watermark` / `audio_setting`；参考图会按 `reference_image` 提交 |

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

**视频编辑**

```json
{
    "model": "happyhorse-1.0-video-edit",
    "resolution": "720P",
    "duration": 3,
    "seed": 1234,
    "watermark": true,
    "audio_setting": "origin",
    "prompt": "保持原视频动作和镜头不变，仅将人物替换为参考图中的主体",
    "media": [
        {
            "type": "video",
            "url": "https://example.com/input.mp4"
        },
        {
            "type": "image",
            "url": "https://example.com/ref.jpg"
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
    "api": "submit",
    "created_at": "2026-01-01T00:00:00Z"
}
```

## 常见错误

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `invalid_request` | 缺 `prompt` / `resolution`、媒体与能力不匹配等 |
| 400 | `billing_params_unmatched` | `resolution`+`duration` 未命中任何计费规则 |
| 401 | `auth_failed` | API Key 无效 |
| 402 | `insufficient_points` | 点数不足 |
| 404 | `not_found` | 应用未对租户开放或未配置价格 |
| 502 | `app_not_configured` | 应用未配置服务地址或密钥 |

第三方返回的业务错误可能以其它 `code` 出现在 `error` 对象中，以实际响应为准。

## 说明

- 冻结与结算点数由 **`resolution` + `duration`** 命中的规则决定；租户可在应用市场调整规则或点数映射。
- 更完整的自动化正文可由 `php think ai_developer_doc:sync_from_source --app-id=12 --only=app --force --publish` 从 `ai_app_api` 元数据生成/覆盖本页在库中副本。
