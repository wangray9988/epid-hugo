# EPID 多语种柴油配件站 — 复用版交付说明（傻瓜版）

> 这是你现有站 `dieselengineparts.top` 的**源码复用版**：保留原模板、原内容、原图片，
> 只做三件事 —— ① 补上**法语 / 德语**两个语种；② 把域名换成你的新域名；
> ③ 接入 **Cloudflare Pages（自动部署）** + **n8n（每天自动发 5 语种 SEO 文章）**。
> 日常运营基本零代码。

---

## 1. 现在里面有什么

- `content/` —— 英文（主）内容
- `content/ar/` —— 阿拉伯语（**已翻译，原站没放出来，现在启用**）
- `content/es/` —— 西班牙语（**已翻译，原站没放出来，现在启用**）
- `content/fr/` —— 法语（我新写的 4 个核心页：首页 / 产品 / 关于 / 联系）
- `content/de/` —— 德语（同上 4 个核心页）
- `i18n/` —— 各语种界面词（ar / en / es / fr / de 已齐）
- `static/wp-content/` —— 原站全部图片（1287 张，路径不变，已就位）
- `themes/epid/` —— 原站主题（别动）
- `hugo.toml` —— 站点配置（已加 fr/de、baseURL 留了占位符）
- `n8n/daily-article-workflow.json` —— 每天自动发文工作流（见第 5 节）

**语言切换器**会自动出现（EN / ES / AR / FR / DE），不会 404。

---

## 2. 第一步：改域名

打开 `hugo.toml`，搜索这一行：

```
baseURL = "https://chinaengineparts.top/"
```

把 `REPLACE-WITH-YOUR-DOMAIN` 换成你的真实域名，例如：

```
baseURL = "https://chinaengineparts.top/"
```

（结尾一定要带 `/`）

---

## 3. 第二步：部署到 Cloudflare Pages（最省事，推荐）

1. 把整个 `epid-hugo` 文件夹**推送到一个 GitHub 仓库**（不会用 Git？让自由职业者做，或告诉我我带你走一遍）。
2. 登录 Cloudflare 控制台 → **Workers & Pages** → **Create** → **Pages** → **连接 Git**。
3. 选你的仓库，按下面填：
   - **Framework preset**：选 `Hugo`
   - **Build command**：`hugo --minify`
   - **Build output directory**：`public`
   - **环境变量**（重要）：`HUGO_VERSION` = `0.125.0`（Extended 版）
4. 点 Deploy。以后**每次往仓库推送，Cloudflare 会自动重新构建并上线**。

> 域名：在 Cloudflare Pages 的 **Custom domains** 里绑定你的新域名（先把域名 DNS 转到 Cloudflare）。
> 也可以用 `wrangler.toml` 走命令行部署（`wrangler pages deploy public`），一般不用。

---

## 4. 怎么加内容（两种，都不碰代码）

**A. 手动加文章（英文）**
在 `content/news/` 里新建一个 `.md` 文件，格式参考 `content/news/sample-weichai-wp10-turbo-maintenance.md`：
```
---
title: "文章标题"
date: "2026-07-21"
type: "post"
description: "一句话摘要，给谷歌看"
slug: "url-slug"
---
正文用 Markdown 写……
```
对应语种就放 `content/es/news/`、`content/ar/news/`、`content/fr/news/`、`content/de/news/`。
推送到 GitHub → 自动上线。

**B. 让 n8n 每天自动写（见第 5 节）**

长尾产品页（比如某个具体机型）暂时先用**英文**页顶着，等 n8n 或人工慢慢补 fr/de 翻译即可，不影响收录。

---

## 5. 第三步：n8n 每天自动发 5 语种文章

文件：`n8n/daily-article-workflow.json`

流程：**定时（每天 09:00）→ 用 AI 写英文文章 → 自动翻译成 西/阿/法/德 → 分别提交到 GitHub 仓库 → Cloudflare 自动上线**。

设置步骤：
1. 你自己装一个 n8n（本地或云服务器都行，有免费版）。
2. 在 n8n 里 **Import from File** 导入这个 JSON。
3. 配两个密钥（在 n8n 的 Environment / Credentials 里设）：
   - `OPENAI_API_KEY` —— 你的 OpenAI key（用来写和翻译文章）
   - `GITHUB_TOKEN` —— 一个有写权限的 GitHub Token
4. 打开 `配置` 节点，把 `repo_owner` / `repo_name` / `branch` 改成你的仓库信息；`slug` 和 `topic` 是当天的文章主题（想轮换话题，可把 `配置` 节点换成读取一个关键词表）。
5. 启用工作流。每天到点自动发 5 语种文章，无需人工。

> 注意：这个 JSON 是“模板”。不同 n8n 版本节点参数可能略有差异，导入后若某个 HTTP 节点报错，
> 在界面里照着提示补一下参数即可（核心逻辑不变）。AI 生成内容建议**人工过一眼**再正式放量，避免机器腔被谷歌降权。

---

## 6. 图片 / 静态资源

原站所有图片已在 `static/wp-content/uploads/...`，路径和原站完全一致，**无需改动**。
新文章要插图，直接写 `/wp-content/uploads/年/月/文件名.jpg` 即可。

---

## 7. 安全提醒（重要）

- 你之前在聊天里发过 VPS 的 **root 密码**。建议**立刻改密**：在服务器上执行 `passwd root`。
- 本机已从服务器拉取的临时压缩包（源码 26MB、图片 20MB）**已全部删除**，服务器上的临时包也已清理。
- 之后运维建议用 **SSH 密钥登录**，别再用密码。

---

## 8. 上线后建议做的（按优先级）

1. 绑定新域名 + 开启 HTTPS（Cloudflare 一键）。
2. 接 **Google Search Console** + **GA4**，盯着收录和询盘。
3. 配 **WhatsApp 业务号**（首屏直链，询盘主力）。
4. 慢慢补 fr/de 长尾产品/文章页（可交给 n8n 或兼职翻译）。
5. 旧域名 `dieselengineparts.top` 做 301 跳转到新域名（保留已有 SEO 权重）。

---

## 9. 本地预览（可选，给会命令的人）

```bash
# 需要装 Hugo extended 版
hugo server
# 浏览器打开 http://localhost:1313
```

> 你不用自己跑这个。改完域名、推到 GitHub，Cloudflare 那边就自动出来了。
