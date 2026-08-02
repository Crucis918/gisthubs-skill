# GistHubs Agent Skill — 把你的仓库发布到 GistHubs

> 这份文档面向 AI agent。按照下面的步骤，帮用户把任意 GitHub 仓库的内容展示到 GistHubs（GitHub 的小红书）上。

GistHubs 从仓库根目录的 `.gisthub/` 文件夹读取内容并渲染成图文 feed。你的任务是：**在用户的仓库里创建/更新 `.gisthub/` 内容 → 推送到 GitHub → 调用一次提交接口**。服务器只在访客浏览时懒加载内容，你不需要上传任何内容到 GistHubs 服务器本身。

> 结构模板仓库（可直接对照/复制）：<https://github.com/Crucis918/gisthub-demo>

## 第一步：检查 `.gisthub/` 是否存在

在用户仓库根目录检查 `.gisthub/config.yml`。不存在就创建：

```yaml
# .gisthub/config.yml
name: "项目显示名称"          # 必填，展示用
description: "一句话描述"     # 可选
accent: "#f43f5e"            # 可选，品牌色
```

## 第二步：创建内容

每篇内容是一个目录，放在 `.gisthub/posts/`（图文/笔记）或 `.gisthub/changelogs/`（更新日志）下：

```
.gisthub/
  posts/
    2026-08-01-my-post/      # 目录名即 slug：小写字母/数字/连字符，建议日期前缀
      index.md               # frontmatter + markdown 正文
      images/                # 配图必须提交进仓库，不能用外链图床
        cover.png
```

`index.md` 格式：

```markdown
---
title: "标题"                 # 必填
date: 2026-08-01             # 必填 YYYY-MM-DD
type: photo                  # 必填：photo | update | note
category: 开发工具            # 可选：开发工具 | AI | 效率工具 | 技术教程 | 产品动态 | 生活日常（不填会自动从标签/标题推断）
cover: images/cover.png      # 可选，封面图（相对本目录）
tags: [tag1, tag2]           # 可选
---

正文 Markdown。图片用相对路径引用：![截图](images/screenshot.png)
```

- `photo` = 图文笔记（截图、demo、UI）；`update` = 更新日志（放 changelogs/）；`note` = 技术笔记
- slug 规则：`^[a-z0-9][a-z0-9-]{0,99}$`
- **内容必须真实**：基于仓库实际的代码、文档、截图来写，不要编造功能

## 第三步：提交并推送

用用户本地的 git 凭据 commit 并 push 到仓库默认分支。**不要**替用户决定提交信息以外的任何事，推送前确认用户授权。

## 第四步：向 GistHubs 服务器提交仓库地址

只需发仓库地址，服务器会自己校验并懒加载内容：

```bash
curl -X POST <GISTHUB_HOST>/api/submit \
  -H "Content-Type: application/json" \
  -d '{"repoUrl": "https://github.com/OWNER/REPO"}'
```

`repoUrl` 也接受 `OWNER/REPO` 简写。公开实例为 `https://gisthubs.com`（自建实例换成对应地址）。

响应：

| 状态码 | 含义 |
|--------|------|
| 200 `{"ok":true,"fullName":"owner/repo"}` | 成功（重复提交返回同样结果，幂等） |
| 400 | URL 不合法 / 仓库不存在 / 不是公开仓库 / 缺少 `.gisthub/` 目录 |
| 429 | 提交过于频繁（每 IP 每小时 10 次），按 `Retry-After` 重试 |
| 502 | GitHub 校验暂时失败，稍后重试 |

成功后仓库会出现在该 GistHubs 实例的游客 feed 和「发现 → 社区仓库」里。内容变更（新帖子）推送后**无需再次提交**，feed 会在缓存过期（约 15 分钟）后自动更新。

## 可选：查询社区目录

```bash
curl <GISTHUB_HOST>/api/submit
```

返回该实例已收录的全部社区仓库列表。

## 注意

- 仓库必须是**公开仓库**（私有仓库只有登录用户本人授权后能看，社区提交只接受公开仓库）
- 图片必须提交在 post 的 `images/` 目录里，GistHubs 通过自己的代理读取，外链图片不保证可用
- `<GISTHUB_HOST>` 是目标 GistHubs 实例的地址（公开实例 `https://gisthubs.com`，自建实例可能是 `http://localhost:3001` 或其他域名），向用户确认或使用他们指定的实例
