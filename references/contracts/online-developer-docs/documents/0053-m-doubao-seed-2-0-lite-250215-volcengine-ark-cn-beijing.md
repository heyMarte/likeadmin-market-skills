# Doubao Seed 2.0 Lite

深度思考、文本生成、多模态理解、工具调用。上下文256k；最大输入256k；最大输出128k（默认4k）；推理链128k。30,000RPM/5,000,000TPM。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 模型 API |
| 模型名称 | Doubao Seed 2.0 Lite |
| 模型编码 | `doubao-seed-2-0-lite-250215` |
| 模型类型 | 文本生成（text） |
| 调用方式 | `POST /api/v1/chat/completions` |
| 调用模式 | 同步 |
| 计费方式 | 按 token（输入 70.000000 元 / 百万 token，输出 380.000000 元 / 百万 token） |
| 最大 token | 131072 |

</details>

## 鉴权

所有调用必须在请求头携带平台分配的 API Key：

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/chat/completions
```

该模型走对话补全接口，请在 Body 中传入标准 `messages` 数组；如需流式输出可设置 `stream: true`。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `model` | string | 是 | — | `doubao-seed-2-0-lite-250215` | `doubao-seed-2-0-lite-250215` | 模型编码，固定传 `doubao-seed-2-0-lite-250215` |
| `messages` | array | 是 | — | — | `[{"role":"user","content":"你好"}]` | 标准对话消息数组，元素形如 `{role, content}` |
| `stream` | boolean | 否 | `false` | `true` / `false` | `false` | 是否流式返回 |
| `tools` | array | 否 | — | — | — | 工具调用定义（模型支持时） |
| `top_p` | number | 否 | — | `0~1` | — | 核采样 |
| `stream` | boolean | 否 | `1` | — | — | 是否 SSE 流式 |
| `messages` | array | 是 | — | — | — | 对话消息列表。每条消息包含角色和内容，支持纯文本及图片、视频等多模态输入。 |
| `max_tokens` | integer | 否 | — | — | — | 最大生成 token，勿超过本模型上限 |
| `temperature` | number | 否 | `0.7` | `0~2` | — | 采样温度 |
| `tool_choice` | string | 否 | — | `auto` / `none` / `required` | — | 工具选择策略：auto/none/required 或指定函数名 |
| `response_format` | object | 否 | — | — | — | 结构化输出（模型支持时），如 {"type":"json_object"} |
| `presence_penalty` | number | 否 | — | `-2~2` | — | 存在惩罚 |
| `frequency_penalty` | number | 否 | — | `-2~2` | — | 频率惩罚 |

## 请求示例

```json
{
    "model": "doubao-seed-2-0-lite-250215",
    "messages": [
        {
            "role": "user",
            "content": "你好，请介绍一下你自己。"
        }
    ],
    "stream": false
}
```

## 成功响应

```json
{
    "id": "chatcmpl-xxxxxxxxxxxxxxx",
    "object": "chat.completion",
    "created": 1740000000,
    "model": "doubao-seed-2-0-lite-250215",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": "这是模型生成的回复内容。"
            },
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 20,
        "completion_tokens": 100,
        "total_tokens": 120
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
