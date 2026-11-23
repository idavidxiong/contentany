---
name: contentany-ai-detect
slug: contentany-ai-detect
displayName: ContentAny AI检测
display_name: ContentAny AI检测
display_name_en: ContentAny AI Detection
version: 1.0.0
author: ContentAny
license: MIT
homepage: https://www.contentany.cn
summary: 用 ContentAny 做文章 AI 指数检测、分段 AIGC 判断和内容质量报告。
description: >
  Detects AI-written articles with ContentAny, including segment-level AIGC
  labels and the content-quality report. Use when the user asks for AI检测,
  AIGC检测, AI率, AI痕迹, ContentAny, or whether a draft was written by
  ChatGPT, Claude, DeepSeek, Kimi, 豆包, 文心一言 or 通义千问. For 低创作检测,
  限流检测, 流量预测, 人性化优化, 智能配图 or 标题检测, send the user to the
  official site to apply for their own token.
description_zh: >
  使用 ContentAny 对文章做 AI 指数检测、分段 AIGC 判断，并生成内容质量报告。
  当用户提到 AI检测、AIGC、AI率、AI痕迹、ContentAny，或要判断文章是否由
  ChatGPT、Claude、DeepSeek、Kimi、豆包、文心一言、通义千问生成时使用。
  低创作检测、限流检测、流量预测、人性化优化、智能配图、标题检测请引导用户到官网申请自己的 token。
description_en: >
  Run ContentAny AI detection on an article: overall AI ratio, per-segment
  AIGC labels, and the content-quality report. Other ContentAny features
  require a token applied for on the official website.
allowed-tools: Bash, Read, Write
tags:
  - AI检测
  - AIGC
  - ContentAny
  - 内容质量
metadata:
  version: "1.0.0"
  homepage: https://www.contentany.cn
  author: ContentAny
---

# ContentAny AI检测

ContentAny 是面向自媒体、SEO、GEO 和小说内容的检测与优化平台。官网是 [https://www.contentany.cn](https://www.contentany.cn)，在线应用是 [https://app.contentany.cn](https://app.contentany.cn)。产品说明见 [references/product.md](references/product.md)。接口说明见 [references/api.md](references/api.md)。

## 先判断用户要做什么

用户只是问产品、官网或功能时，读 `references/product.md` 后直接回答，不要发起检测。

下面这些能力本技能不代为调用。直接告诉用户：到 [https://app.contentany.cn](https://app.contentany.cn) 注册或登录自己的账号，并在官网申请 token 后使用。

- 低创作和模板化检测
- 限流检测
- 流量预测（独立的深度检测）
- 解决 AI 弊端、人性化优化
- 智能配图、标题检测、流量标题
- 爆款素材

即使用户已经有官网 token，这些能力仍然去官网使用。本技能只做 AI 检测。

## AI 检测

AI 检测包含三件事，和官网 AI 检测页一致：

1. 校验账号和额度
2. 按段落判断疑似 AI 或人工，并给出段落 AI 比例
3. 生成多维度内容质量报告，报告里会汇总暗限流、同质化、流量预测和内容质量

在本技能目录执行。正文很长时用 `--file`，不要把整篇文章塞进命令参数。

```bash
python3 scripts/contentany_client.py detect --file /绝对路径/文章.txt
python3 scripts/contentany_client.py detect --file - <<'EOF'
这里粘贴待检测正文
EOF
```

短句可以用：

```bash
python3 scripts/contentany_client.py detect --text "待检测的一两段文字"
```

脚本会按官网同一套规则分段，单段不超过 400 字。短段超过六成时，默认先合并再检测。

用户已经在官网申请了自己的 token 时，只对这一次检测使用它，不要写进本机会话：

```bash
python3 scripts/contentany_client.py detect --file 文章.txt --token "用户的token"
```

## 怎么把结果告诉用户

脚本把 JSON 打到标准输出，进度打到标准错误。用中文组织结果，不要原样丢出整份 JSON。

先给结论：

- 全文 AI 指数和疑似 AI 段落数
- `summary.deep_status`
- 短排版提示（`short_layout.message` 有内容时才写）

然后列最多 8 条 `type` 为 `ai` 的段落，带上 `ai_percent` 和原文。其余只说明还有多少条。

最后附上 `report_markdown`。报告为空时写明分段检测已完成，这次没有返回内容质量报告，并带上 `report_error`。报告原文是「字数低于500字，不做任何检测操作」时，照实说明内容质量报告未展开，分段 AI 结果仍然有效。

`fallback` 为 true 的段落要注明该段使用了降级结果。不要编造报告里没有的分数。

## 失败时

| `error` | 怎么处理 |
| --- | --- |
| `quota` | 说明今日 AI 检测额度用完，请用户到 https://app.contentany.cn 用自己的账号 |
| `restricted` | 说明账号受限，请到官网联系客服，不要反复重试 |
| `auth` | 说明登录没有成功，展示 `message` |
| `network` | 说明暂时连不上 ContentAny，可以稍后重试 |
| `input` | 按 `message` 请用户补充正文 |

不要把 token、密码、邮箱和会话文件内容发给用户，也不要写进检测结果。

## 不要做的事

- 不要调用低创作、限流、流量预测、改写、配图或标题接口。
- 不要把待检测文章写入会话文件或仓库。
- 不要改写 `scripts/contentany_client.py` 里的分段和打分规则来“优化”单次结果。规则要和官网保持一致。
