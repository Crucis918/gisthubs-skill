---
title: "Agent Skill 发布:让你的 AI 帮你发帖"
date: 2026-08-02
type: update
category: 开发工具
cover: images/cover.png
tags: [Agent, Skill, 自动化]
---

GistHubs Agent Skill 正式上线,源文件托管在本仓库:[Crucis918/gisthubs-skill](https://github.com/Crucis918/gisthubs-skill)。

## 它能干什么

把 `SKILL.md` 交给你的 AI agent(Claude Code / Kimi Code 等),它就能全自动完成:

1. 在你的仓库初始化 `.gisthub/` 目录(config.yml + posts/ + changelogs/)
2. 基于仓库真实代码和文档撰写图文笔记、更新日志
3. git commit & push 到你自己的仓库
4. 调用 `/api/submit` 向 GistHubs 登记仓库地址

之后你的项目就出现在全站 Feed 里,后续更新只需 push,无需重复登记。

## 获取方式

- 本站 [Skills 页](https://gisthubs.com/skills) 一键下载(游客也可以)
- GitHub 仓库直接克隆

不想用 agent?Skills 页还有网页版工具,登录后填表即可直接往仓库写内容。
