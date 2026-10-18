# 公共文件临时上传

**Base**：`https://<租户 API 域名>/api/v1`

**鉴权**：`Authorization: Bearer <API_KEY>`

## 临时存储限制

本接口仅用于临时文件中转。成功响应中的 `url` 最长有效期为 **24 小时**，平台可因存储回收、容量治理或安全策略在此之前失效。不得将该 URL 用作持久文件地址、唯一业务凭据或长期媒体库；需要长期保存时，请在有效期内下载并转存到自己的存储服务。

## `POST /upload`

使用 `multipart/form-data` 提交单个 `file` 字段。平台将文件暂存于当前租户 API 域名可访问的本地路径，由平台的临时存储服务拉取并返回最终 URL。平台本地暂存文件在成功、失败或异常时都会删除。

请求字段：

| 字段 | 位置 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- | --- |
| `file` | form-data | file | 是 | 单个待上传文件。支持平台既有的图片、视频和通用文件扩展名。 |

默认单文件最大 512 MB；平台可通过 `storage.openapi_upload.max_bytes` 调整限制。

```bash
curl -X POST "https://<租户API域名>/api/v1/upload" \
  -H "Authorization: Bearer <API_KEY>" \
  -F "file=@./example.mp4"
```

成功响应：

```json
{
  "code": 1,
  "msg": "success",
  "data": {
    "url": "https://files.example.com/openapi/12/20260814/abc.mp4",
    "path": "openapi/12/20260814/abc.mp4",
    "name": "example.mp4",
    "size": 123456,
    "mime_type": "video/mp4",
    "type": "video"
  }
}
```

响应字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `url` | string | 临时文件访问地址，最长有效期 24 小时，可能提前失效。 |
| `path` | string | 临时存储服务中的相对路径，仅供排查或同服务内引用，不保证长期可用。 |
| `name` | string | 原始文件名。 |
| `size` | integer | 上传文件字节数。 |
| `mime_type` | string | 平台识别到的 MIME 类型。 |
| `type` | string | `image`、`video` 或 `file`。 |

失败时返回 `{ "code": 0, "msg": "...", "data": null }`。不返回内部存储服务地址、临时本地路径或认证信息。

## 使用边界

- 接口不收取模型调用费用，但受 API Key 身份、限流和平台文件大小限制约束。
- 仅可上传调用方有权处理的文件；不得上传违法、侵权、恶意程序或规避平台安全策略的内容。
- 平台不承诺临时地址的固定保存时长、可恢复性、跨区域可用性或长期下载带宽。
- 若上传后需要给第三方服务消费，请在同一业务流程内使用 `url`；不要将其写入长期订单、素材库或用户资料。
