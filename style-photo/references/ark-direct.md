# 风格照片 · 服务器直连方舟 API

适用场景：agent 环境没有 `image_gen` 工具、或用户有自己的服务端（如本项目的 Ubuntu systemd 服务），需要直连方舟图片生成 API。

## 请求

```http
POST {ark_media_base}/images/generations
Authorization: Bearer {DASHSCOPE_KEY}
Content-Type: application/json
```

```json
{
  "model": "doubao-seedream-5-0-pro",
  "prompt": "（按 prompt-template.md 组装的完整提示词）",
  "image": [
    "人物参考图1(dataURL或URL)",
    "穿搭参考图1",
    "场景参考图1"
  ],
  "size": "1248x1664",
  "response_format": "b64_json",
  "watermark": true
}
```

要点：
- `image` 数组顺序 = 人物图 + 穿搭图 + 场景图，与 prompt 中"图1、图2…"编号一一对应
- `size`：`2K` / `1K` / 指定宽高像素；指定像素必须小写 `x`（`1248x1664`），需 16 的倍数、总像素 921,600–4,194,304
- `watermark: true` 会在右下角标"AI生成"水印
- 模型名可用环境变量覆盖：`SHOWCASE_IMAGE_MODEL`（默认 `doubao-seedream-5-0-pro`）

## 响应

```json
{ "data": [ { "b64_json": "..." } ] }
```

- 非 200：按官方错误码处理，不要把响应体/令牌泄露给用户
- 输出直接用 seedream 生成结果，不做额外后处理滤镜（避免画面发灰、变脏）

## 计费速查（doubao-seedream-5-0-pro）

- ≤261 万像素（1.5K 及以下）：0.30 元/张；>261 万：0.60 元/张
- 输入图：首张免费，第 2 张起 0.02 元/张
- 参考：官方价格页 https://docs.volcengine.com/docs/82379/1544106 ，API size 文档 https://www.volcengine.com/docs/82379/1541523
