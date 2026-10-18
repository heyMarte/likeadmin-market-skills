# nano-banana 图片生成

`nano-banana` 提供统一的图片生成与编辑接口。创建任务为异步调用，返回平台 `task_id`；查询接口按 `task_id` 获取任务状态、图片结果与实际扣点。

## 接入流程

1. 调用 `POST /api/v1/apps/nano_banana/submit` 创建图片任务。
2. 保存响应里的平台 `task_id`。
3. 调用 `GET /api/v1/apps/nano_banana/query?task_id=...` 或统一任务查询接口获取状态和结果。
4. 如提交时传入 `callback_url`，平台会在任务完成或失败时主动通知该地址。

## API 列表

| API | 路径 | 说明 |
| --- | --- | --- |
| 创建图片任务 | `/api/v1/apps/nano_banana/submit` | 支持文生图、图生图编辑、模型和分辨率选择 |
| 查询图片任务 | `/api/v1/apps/nano_banana/query` | 按平台 `task_id` 查询处理状态、图片结果和实际扣点 |

## 价格展示策略

页面和文档只展示汇总档位：普通模型 24 点/次起，官方模型 28.03 点/次起，官方高清按 1K/2K/4K 分档。完整实时价格以租户当前价格配置和 `/api/v1/pricing`、`/api/v1/pricing/batch` 查询结果为准，避免一次性展开全部 12 个 SKU 影响阅读。