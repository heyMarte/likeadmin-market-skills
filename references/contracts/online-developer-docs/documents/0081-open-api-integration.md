# 公共参数与 storage

**Base**：`https://<域名>/api/v1`

**鉴权**：`Authorization: Bearer <API_KEY>`；JSON 请求加 `Content-Type: application/json`。

**响应**：`{ "code": 1|0, "msg": "…", "data": … }`。成功 `code=1`，失败 `code=0` 看 `msg`。

支持在 **`POST /tasks`**、**`POST /chat/completions`**、**应用中心**（`ANY /apps/{app_code}/{api_code}`）等与**业务 JSON 同级**传下列公共字段；具体业务参数以各模型/应用文档为准。

---

## 一、公共参数

| 字段 | 说明 |
| --- | --- |
| `model` | 模型编码；`POST /tasks` 必填，`/chat` 必填 |
| `channel` | 选填，渠道/线路，不传由平台解析 |
| `callback_url` | 选填，异步终态时向该地址 **POST** JSON（`POST /tasks`、部分应用可传） |
| `storage` | 选填，见**第二节**；AK/SK 不落库，按任务短期缓存 |
| `stream` | 选填，仅 `/chat/completions` 流式 |
| `params` | 仅 `POST /tasks` 旧写法：有非空 `params` 对象时，**仅**其内为业务体；`model` 等仍在根级 |

**应用**请求里会**去掉**与路由同名的 `app_code`/`api_code` 和 **`callback_url`**，**`storage` 也不参与**与接口默认参数合并（解析后移除）。

---

## 二、用户相关查询接口

| API | 说明 |
| --- | --- |
| `GET /tasks/{task_id}` | 查询异步模型任务或异步应用任务的状态与结果；详见 [通用任务查询](api-task-query.md)。 |
| `GET /user/balance` | 查询当前 API Key 对应用户余额/点数。 |
| `GET /user/usage` | 查询当前 API Key 对应用户使用量。 |
| `GET /pricing` | 查询模型 API 或应用 API 的当前有效价格规则。 |
| `POST /pricing/batch` | 批量查询模型 API 或应用 API 的当前有效价格规则。 |

价格查询只返回规则与可读说明，不计算单次调用预计扣点；完整字段说明见仓库 [api-common-parameters.md](api-common-parameters.md)。

---

## 三、storage：对象转存

任务或对话产出**媒体**后，平台可**拉取源 URL 再上传**到**你方**桶。一次请求**只能**选**一种**云。传参**两种**写法（二选一）：

- **子对象**：`"storage": { "oss" | "cos" | "kodo": { … } }`（三键**只能有一个**有内容）
- **平铺**：`"storage": { "provider": "oss"|"cos"|"kodo"|"ftp", … }`

主密钥**可用多组键名**（与代码 `firstNonEmpty` 一致），下表以**常用名**为主。

---

### 3.1 阿里云 OSS（`oss` / `aliyun`）

| 入参 | 必填 | 说明 |
| --- | --- | --- |
| `bucket` | 是 | 桶名 |
| `access_key` / `access_key_id` 等 | 是 | 访问主键（见上「别名」） |
| `secret_key` / `access_key_secret` 等 | 是 | 访问密钥 |
| `endpoint` 或 `domain` 或 `internal_endpoint` | 是 | OSS 访问域名，如 `oss-cn-hangzhou.aliyuncs.com`（作为 SDK endpoint） |
| `public_domain` / `cdn_domain` / `domain_url` / `url_domain` | 否 | 有则**返回的** `stored_url` 以该域为基 |
| `path_rule` / `key_prefix` / `path` | 否 | 对象键，默认 `ai-api/{date}/{task_id}_{index}.{ext}`，可含 `{task_id}` 等占位符 |

无 `public_domain` 时，默认 `stored_url` 形态：`https://<bucket>.<endpoint>/<key>`（有 endpoint 时）。

---

### 3.2 腾讯云 COS（`cos` / `qcloud`）

| 入参 | 必填 | 说明 |
| --- | --- | --- |
| `bucket` | 是 | 桶名（含 appid 的桶名按 COS 要求） |
| `access_key` / `secret_id` 等 | 是 | 对应腾讯云 SecretId |
| `secret_key` 等 | 是 | 对应腾讯云 SecretKey |
| `region` | 是 | 地域，如 `ap-guangzhou`（平台归一**必须**能取到 `region`） |
| `public_domain` / `cdn_domain` / `domain_url` / `url_domain` | 否 | 对外访问基域，用于 `stored_url` |
| `path_rule` / `key_prefix` / `path` | 否 | 同 OSS，默认与占位符一致 |

无 `public_domain` 时，默认 `stored_url`：`https://<bucket>.cos.<region>.myqcloud.com/<key>`（有 `region` 时）。

---

### 3.3 七牛 Kodo（`kodo` / `qiniu`）

| 入参 | 必填 | 说明 |
| --- | --- | --- |
| `bucket` | 是 | 空间名（bucket） |
| `access_key` 等 | 是 | AccessKey |
| `secret_key` 等 | 是 | SecretKey |
| `public_domain` / `cdn_domain` / `domain_url` / `url_domain` | **强烈建议** | 绑定域名/ CDN；不归一则 **`stored_url` 可能无完整 http 前缀**（仅 key） |
| `path_rule` / `key_prefix` / `path` | 否 | 对象键，默认同前 |

转存用七牛 `fetch` 到指定空间/Key；**无**在配置校验里与 OSS 同级的「endpoint 必传」，但**线上访问**需你配置好域名或自行拼 URL。

---

### 3.4 响应中的 `storage` / `storage_error`

- 转存**成功**且任务完成：可能多 **`data.storage`**（`provider`、`bucket`、`files` 含 `stored_path`、`stored_url` 等）。
- 转存**失败**：可能多 **`data.storage_error`**（`provider`、`code`、`message`）。
- 配置在**本次请求解析**阶段不通过：通常 **`code=0`**，以 `msg` 为准。

---

## 四、（仓库内备查）

更长的参数手册仍保留在仓库 [api-common-parameters.md](api-common-parameters.md)，**不在**开发者中心菜单中单独展示；需要字段级全表时自行打开。
