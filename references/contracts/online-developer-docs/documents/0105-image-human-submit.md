## 全驱动数字人 - 提交任务

`POST /api/v1/apps/image_human/submit`

根据输入人物图片和参考音频创建全驱动数字人生成任务。`mode` 同时作为生成模式和清晰度计费档位，支持 `fast`、`standard`、`2k`、`4k`；`resolution` 仅作为兼容别名，新接入建议直接使用 `mode=2k` 或 `mode=4k`。

### 请求参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `file_url` | string | 是 | 输入图片 URL。 |
| `ref_file_url` | string | 是 | 输入音频 URL，平台按该音频时长计费。 |
| `prompt` | string | 否 | 用户提示词；未传时使用默认提示词。 |
| `mode` | string | 否 | 生成模式/清晰度档位：`fast`、`standard`、`2k`、`4k`。默认 `standard`。 |
| `resolution` | string | 否 | 兼容字段：传 `2k` 或 `4k` 时等同于 `mode=2k` 或 `mode=4k`。新接入建议直接使用 `mode`。 |
| `duration` | number | 否 | 音频时长，单位秒；当系统无法自动解析音频时长时必须传入。 |

### 请求示例

```json
{
  "file_url": "https://example.com/person.png",
  "ref_file_url": "https://example.com/audio.wav",
  "prompt": "一个男人在说话",
  "mode": "2k"
}
```

提交成功后请使用查询接口按 `task_id` 拉取结果。