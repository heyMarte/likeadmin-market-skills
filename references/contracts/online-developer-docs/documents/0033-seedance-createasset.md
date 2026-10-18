# Seedance 2.0 · 上传素材

将公网可访问的素材文件上传到指定 Asset Group。支持图像 / 视频 / 音频。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | Seedance 2.0 |
| 应用编码 | `seedance` |
| API 名称 | 上传素材 |
| API 编码 | `createAsset` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/seedance/createAsset` |
| 调用模式 | 同步 |
| 计费方式 | 按次（0.0000 元 / 次） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/seedance/createAsset
```

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `URL` | string | 是 | — | — | `https://your-domain.com/uploads/image.png` | 素材公网可访问 URL；将由服务拉取并入库 |
| `Name` | string | 否 | — | — | — | 素材名称，上限 64 字符 |
| `GroupId` | string | 是 | — | — | `group-20260402173639-97brg` | Asset 所属的 Asset Group Id（来自 createGroup 返回的 Result.Id） |
| `AssetType` | string | 是 | — | `Image` / `Video` / `Audio` | `Image` | 素材类型：Image / Video / Audio |
| `ProjectName` | string | 否 | `default` | — | — | 项目名称，默认 default |

## 请求示例

```json
{
    "URL": "https://your-domain.com/uploads/image.png",
    "Name": "string",
    "GroupId": "group-20260402173639-97brg",
    "AssetType": "Image",
    "ProjectName": "default"
}
```

## 成功响应

```json
{
    "code": 1,
    "msg": "success",
    "data": {
        "_comment": "具体字段以接口实际返回为准"
    }
}
```

## 失败响应

```json
{
    "error": {
        "message": "点数余额不足",
        "type": "insufficient_points",
        "code": "insufficient_points"
    }
}
```

### 错误码

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `invalid_request` | 参数缺失或格式错误 |
| 401 | `auth_failed` | API Key 缺失或无效 |
| 402 | `insufficient_points` | 点数余额不足 |
| 402 | `key_quota_exceeded` | 当前 API Key 点数额度不足 |
| 403 | `permission_denied` | 当前 API Key 无权调用该模型/应用 |
| 404 | `not_found` | 模型 / 应用 / 任务不存在 |
| 429 | `queue_limit_exceeded` | 排队任务已达上限 |
| 5xx | `server_error` | 服务异常，请稍后重试 |
