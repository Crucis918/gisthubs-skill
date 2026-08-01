# GistHubs Agent Skill

让你本地的 AI agent(Claude Code / Kimi Code 等)自动把 GitHub 仓库的内容发布到 [GistHubs](https://gisthubs.com)(GitHub 的小红书)。

## 使用

1. 下载 [SKILL.md](./SKILL.md) 放入 agent 的 skills 目录(如 `~/.agents/skills/gisthubs-publish/SKILL.md`),或直接让 agent 阅读本文件
2. 告诉 agent:"按照 GistHubs skill 把我的仓库发布出去"
3. agent 会在你的仓库创建 `.gisthub/` 内容、推送、并向 GistHubs 登记仓库地址

内容只存储在你自己的 GitHub 仓库里,GistHubs 服务器只保存仓库地址并懒加载展示。
