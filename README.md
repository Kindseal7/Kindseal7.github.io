# 个人网站

基于 **Hugo + PaperMod 主题** 构建，托管在 **GitHub Pages**。
每次 `git push` 到 `main` 分支，GitHub Actions 会自动重新构建并发布。

---

## 目录说明

```
个人网站/
├── hugo.toml                    # ★ 站点主配置（标题/副标题/菜单/颜色）
├── content/
│   ├── _index.md                # 首页
│   ├── by-date.md               # "按时间"列表页
│   ├── by-importance.md         # "按重要性"列表页
│   ├── search.md                # 搜索页
│   └── posts/                   # ★ 全部文章放在这里
│       └── YYYY-MM-DD-slug/
│           ├── index.md         # 文章正文
│           └── *.jpg / *.png    # 图片和 index.md 同目录
├── layouts/_default/
│   └── by-importance.html       # "按重要性"页的自定义模板
├── assets/css/extended/
│   └── custom.css               # ★ 自定义 CSS（改颜色/字体）
├── archetypes/default.md        # 新文章模板（hugo new 会用它）
├── static/                      # 静态资源（favicon、下载文件等）
├── themes/PaperMod/             # 主题（git submodule）
└── .github/workflows/hugo.yml   # 自动部署脚本
```

---

## 首次使用（第一次部署）

前提：本机已安装 **git**，**Hugo (extended 版, ≥ 0.128)**。

```bash
# 1. 安装 Hugo（Debian/Ubuntu；其他系统见 https://gohugo.io/installation/）
sudo apt install hugo

# 2. 进入项目
cd /home/dingding/code/个人网站

# 3. 初始化 git 仓库
git init
git branch -M main

# 4. 添加 PaperMod 主题（这一步必须用 submodule，不能直接 clone）
git submodule add --depth=1 \
  https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

# 5. 本地预览
hugo server
# 浏览器打开 http://localhost:1313 检查效果

# 6. 在 GitHub 上创建仓库，名字必须是 <你的用户名>.github.io
#    然后关联并推送
git remote add origin git@github.com:<你的用户名>/<你的用户名>.github.io.git
git add .
git commit -m "初始化"
git push -u origin main

# 7. 在 GitHub 仓库网页里：Settings → Pages → Source 选 "GitHub Actions"

# 8. 修改 hugo.toml 中的 baseURL 为 https://<你的用户名>.github.io/
#    再 commit + push 一次
```

约 1–2 分钟后，`https://<你的用户名>.github.io/` 就能访问了。

---

## 日常写作

```bash
# 1. 新建一篇文章（会自动生成带 front matter 的模板）
hugo new posts/2026-07-15-新文章标题/index.md

# 2. 用任意 Markdown 编辑器写正文
#    推荐：Typora / Obsidian / VSCode（都支持 Ctrl+V 直接粘贴截图）
#    图片会保存到文章目录，语法 ![](图片名.jpg) 即可插入

# 3. 本地预览（可选，但推荐）
hugo server
# 浏览器打开 http://localhost:1313，改内容会自动刷新

# 4. 发布
git add .
git commit -m "新文章：xxx"
git push
```

---

## 文章 front matter 字段

每篇文章顶部的 `---` 之间是元信息：

```yaml
---
title: "文章主标题"              # 必填
subtitle: "副标题（可选）"        # 副标题，展示在"按重要性"页
date: 2026-07-03                 # 发布日期，影响"按时间"排序
draft: false                     # true 时不会发布
weight: 10                       # ★ 影响"按重要性"排序（越小越靠前，不填=最后）
tags: [读书, 笔记]                # 标签
cover:
  image: "封面.jpg"              # 封面图（同目录下的文件名）
  alt: "封面说明"
---
```

---

## 常见修改

| 我想改…             | 改哪里                                             |
| ------------------- | -------------------------------------------------- |
| 主标题、副标题       | `hugo.toml` → `title` / `[params]` → `description`  |
| 首页欢迎语           | `hugo.toml` → `[params.homeInfoParams]`             |
| 顶部菜单             | `hugo.toml` → `[[menu.main]]` 各段                  |
| 主题色 / 字体         | `assets/css/extended/custom.css`                    |
| 每页显示文章数       | `hugo.toml` → `paginate = 10`                       |
| 一篇文章的排序权重    | 该文章 front matter 里的 `weight`                    |
| 一篇文章的时间       | 该文章 front matter 里的 `date`                      |
| 站点部署域名         | `hugo.toml` → `baseURL`                             |

---

## 两种排序页面的行为说明

- **首页 `/`** — PaperMod 默认按日期倒序（最新在前），展示所有文章
- **`/by-date/`** — 按年月归档，同样按日期倒序
- **`/by-importance/`** — 按 `weight` 字段升序（数字小的在前）
  - 未设 `weight` 的文章会被排到最后
  - 想手动置顶某篇文章：给它加 `weight: 1`

---

## 故障排查

- **push 后网站没更新**：去 GitHub 仓库的 Actions 页面查看构建日志
- **本地 `hugo server` 打不开**：检查是否装了 extended 版（`hugo version` 输出应含 `extended`）
- **主题不加载 / 页面样式全无**：确认 `themes/PaperMod/` 目录不空；如为空，运行
  `git submodule update --init --recursive`
- **图片显示不出来**：确认图片和 `index.md` 在同一目录；引用时用文件名而非绝对路径
