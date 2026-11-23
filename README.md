# ContentAny AI检测

给 WorkBuddy、GitHub 和 SkillHub 使用的 ContentAny 技能。它介绍产品和官网，并完成和官网 AI 检测页一致的检测。

- 官网：https://www.contentany.cn
- 在线应用：https://app.contentany.cn/ai-detect

## 这个技能会做什么

- 介绍 ContentAny 的产品能力和对应入口。
- 执行 AI 指数检测：分段判断疑似 AI 或人工，并返回内容质量报告。
- 低创作检测、限流检测、独立的流量预测、人性化优化、智能配图和标题检测不在这里调用，会引导到官网申请自己的 token。

## 本地试用

需要 Python 3.9 以上，不需要安装第三方包。

```bash
python3 scripts/contentany_client.py self-check
python3 scripts/contentany_client.py detect --text "把一段待检测的文章放在这里。"
```

检测整篇文件：

```bash
python3 scripts/contentany_client.py detect --file article.txt
```

## 发布

技能目录就是发布单元，根目录包含 `SKILL.md`。

SkillHub 预检：

```bash
skillhub publish . --dry-run
```

WorkBuddy 上传时，把本目录打成 zip，保留 `SKILL.md`、`manifest.yaml`、`LICENSE`、`scripts/`、`references/` 和 `assets/icon-512.png`。不要把本机的 `~/.contentany/ai-detect-session.json` 打进去。
