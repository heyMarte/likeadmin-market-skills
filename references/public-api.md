# Public API Catalog

All paths below are relative to `${base_url}/api/v1`. All require Bearer authentication.

| Method | Path | Use |
| --- | --- | --- |
| POST | `/upload` | Upload one local input file as multipart field `file`; use `data.url` for URL business fields. |
| POST | `/tasks` | Submit asynchronous non-chat model task. |
| GET | `/tasks/{task_id}` | Query platform model or application task. |
| GET | `/tasks` | List own platform tasks; `page`, `per_page` (max 100), optional `type`, optional valid `status`. |
| POST | `/tasks/{task_id}/cancel` | Cancel an own platform task. Also put `task_id` in the POST body because current controller reads the body value. |
| POST | `/chat/completions` | Chat-completion model call, optionally SSE with `stream=true`. |
| POST | `/responses`, `/messages`, `/completions` | OpenAI/Anthropic-style compatibility entry points; use only when caller explicitly needs that protocol. |
| GET | `/models` | List tenant-visible models; `type` filters and `detail=1` adds schema/default parameters. |
| GET | `/models/detail` | Model details; query `model`, optional `channel`. |
| GET | `/apps` | List tenant-visible apps and API metadata. |
| GET | `/apps/{app_code}` | Get one visible app and pricing-related fields. |
| ANY | `/apps/{app_code}/{api_code}` | Invoke a documented application API. |
| GET | `/pricing` | Query one current price rule. |
| POST | `/pricing/batch` | Query up to 100 current price rules. |
| GET | `/user/balance` | Get `data.available_points`. |
| GET | `/user/usage` | Get usage; optional `start_date`, `end_date` in `Y-m-d`. |

## Pricing

For a model: `GET /pricing?type=model&model={model_code}&channel={optional_channel}`.

For an app API: `GET /pricing?type=app_api&app_code={app_code}&api_code={api_code}`.

Use `data.available`, `data.pricing`, and `data.price_view` as the current price authority. Price structures can be fixed, token, character, duration, parameter rule, matrix, or model-rate based; do not calculate a quote unless the returned rule is unambiguous.

## Task Status

`pending` and `processing` are non-terminal. `completed`, `failed`, and `cancelled` are terminal. Completed responses can include `result`, `usage`, `completed_at`, and `response_time_ms`. A failed response can include `error.code` and safe `error.message`.

## Result Transfer

Pass `storage` only when caller expressly asks to copy generated media to their own object storage. One request selects one provider. Do not expose its credentials in output or persist them. Successful query responses may include `storage.files[].stored_url`; failures may include `storage_error`.
