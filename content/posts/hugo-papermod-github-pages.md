---
title: "用 Hugo + PaperMod 搭建博客，并通过 GitHub Actions 部署到 GitHub Pages"
date: 2026-11-04T13:00:00+08:00
draft: false
tags: ["Hugo", "PaperMod", "GitHub Actions", "GitHub Pages"]
categories: ["项目搭建"]
description: "从安装 Hugo 到博客上线的完整步骤，附带中文配置、样式定制和部署时踩过的坑"
---

这篇文章记录本博客的搭建过程：用 [Hugo](https://gohugo.io/) 生成静态页面，用 [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 作为主题，再用 GitHub Actions 自动构建、部署到 GitHub Pages。整个过程免费，写完文章 `git push` 就能自动上线。

## 准备工作

- 一个 GitHub 账号
- 本机已安装 Git，并且配置好了 SSH Key（能 `git push` 到 GitHub）
- macOS 用户需要 [Homebrew](https://brew.sh/)

## 一、安装 Hugo

PaperMod 需要 **extended** 版本的 Hugo。macOS 上直接用 Homebrew 安装，装的就是 extended 版本：

```bash
brew install hugo
hugo version
# hugo v0.169.0+extended+withdeploy darwin/arm64 ...
```

输出里带有 `extended` 就说明装对了。Windows 用户可以用 `winget install Hugo.Hugo.Extended`，Linux 可以从 [Releases 页面](https://github.com/gohugoio/hugo/releases) 下载 `hugo_extended` 开头的安装包。

## 二、创建站点并添加主题

```bash
hugo new site blog
cd blog
git init -b main
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
```

主题用 git submodule 引入，不要直接复制主题文件。这样主题代码和自己的代码是分开的，以后升级主题只需要：

```bash
git submodule update --remote --merge
```

**不要修改 `themes/` 里的任何文件。** 想改样式或模板时，在项目自己的目录里放同名文件覆盖即可，后面会讲到。

## 三、配置 hugo.toml

站点根目录下的 `hugo.toml` 是全部配置。下面是本博客正在用的配置（菜单图标部分省略）：

```toml
baseURL = 'https://<用户名>.github.io/'
defaultContentLanguage = 'zh'
title = '我的博客'
theme = 'hugo-PaperMod'

[params]
  env = "production"
  description = "我的个人博客"
  author = "你的名字"
  defaultTheme = "auto"          # 深浅色跟随系统
  showBreadCrumbs = true         # 面包屑导航
  showReadingTime = true         # 阅读时长
  ShowWordCount = true           # 字数统计
  showToc = true                 # 文章目录
  TocOpen = false                # 目录默认折叠
  ShowCodeCopyButtons = true     # 代码块复制按钮
  ShowPostNavLinks = true        # 上一篇 / 下一篇
  ShowRssButtonInSectionTermList = true
  showShareButtons = true
  shareButtons = ["x", "telegram"]

  [params.homeInfoParams]
    Title = "欢迎来到我的博客 👋"
    Content = "这里记录我的技术探索、学习笔记和思考"

[[params.socialIcons]]
  name = "github"
  url = "https://github.com/<用户名>"

# 搜索功能依赖首页输出 JSON 索引
[outputs]
  home = ["HTML", "RSS", "JSON"]

[taxonomies]
  category = "categories"
  tag = "tags"
  series = "series"

[menu]
  [[menu.main]]
    identifier = "categories"
    name = "分类"
    url = "/categories/"
    weight = 3
  [[menu.main]]
    identifier = "tags"
    name = "标签"
    url = "/tags/"
    weight = 4
  [[menu.main]]
    identifier = "archives"
    name = "归档"
    url = "/archives/"
    weight = 5
  [[menu.main]]
    identifier = "search"
    name = "搜索"
    url = "/search/"
    weight = 10
  [[menu.main]]
    identifier = "about"
    name = "关于"
    url = "/about/"
    weight = 15

[languages]
  [languages.zh]
    locale = 'zh-CN'
    weight = 1
```

有三个地方容易出错，单独说明一下：

1. **中文界面**：语言代码要写 `zh`，不能写 `zh-cn`。PaperMod 的翻译文件叫 `zh.yaml`，写成 `zh-cn` 会匹配不到，界面就会显示 "min"、"Table of Contents" 这些英文。另外，新版 Hugo 已经弃用 `languageCode`，改用 `[languages.zh]` 里的 `locale`。
2. **搜索**：必须在 `[outputs]` 里给首页加上 `JSON`，否则搜索页找不到 `index.json`，什么都搜不出来。
3. **分享按钮**：PaperMod 支持的分享平台有 x、telegram、facebook、linkedin、reddit、whatsapp、ycombinator 等，**不支持微博**，写了也不会显示。

## 四、创建归档、搜索和关于页

归档页和搜索页使用主题自带的布局，只需要建两个文件：

`content/archives/index.md`：

```markdown
---
title: "归档"
layout: "archives"
url: "/archives/"
summary: "archives"
---
```

`content/search/index.md`：

```markdown
---
title: "搜索"
layout: "search"
url: "/search/"
summary: "search"
placeholder: "搜索文章内容..."
---
```

关于页就是一篇普通的 Markdown，放在 `content/about/index.md`，内容随意。

分类和标签总览页的标题默认是英文的 Categories、Tags，可以分别新建 `content/categories/_index.md` 和 `content/tags/_index.md` 改成中文：

```markdown
---
title: "分类"
---
```

## 五、写第一篇文章

```bash
hugo new content posts/my-first-post.md
```

生成的文件开头是 front matter，用来设置文章的元信息：

```yaml
---
title: "我的第一篇文章"
date: 2026-11-04T10:00:00+08:00
draft: false
tags: ["Hugo"]
categories: ["教程"]
description: "文章摘要，会显示在列表页"
---
```

`draft: true` 的文章不会被发布，写完记得改成 `false`。

**注意 `date` 不要写成未来的时间。** Hugo 默认不发布日期还没到的文章。我搭建时把两篇文章的时间写成了当天下午，结果它们在构建结果里直接消失了，排查了好一会儿才发现原因。

## 六、本地预览

```bash
hugo server -D
```

打开 <http://localhost:1313/> 就能看到效果。`-D` 表示把草稿也显示出来。修改文章或配置后，页面会自动刷新。

如果改了 CSS 而页面没变化，按 `Ctrl+C` 停掉服务，再重新运行 `hugo server`。

## 七、定制样式（可选）

PaperMod 提供了两个不需要修改主题的扩展方式：

- **`assets/css/extended/*.css`**：这个目录下的 CSS 会被合并进主题的样式文件，不会多发一次请求。
- **同名模板覆盖**：在项目的 `layouts/` 目录下放一个和主题同路径的模板，就会替换掉主题里的那份。

比如想把强调色换成蓝色，新建 `assets/css/extended/custom.css`：

```css
:root {
    --accent: #1f5fae;
}

:root[data-theme="dark"] {
    --accent: #7fb0e8;
}

.post-content a:not(.anchor) {
    color: var(--accent);
}
```

PaperMod 切换深色模式时，会在 `<html>` 上设置 `data-theme="dark"`，所以深色样式写在 `:root[data-theme="dark"]` 里就行。

本博客的分类卡片和标签云就是用 `layouts/taxonomy.html` 覆盖主题模板实现的。

## 八、创建 GitHub 仓库

在 GitHub 上新建一个**空仓库**，名字必须是：

```text
<用户名>.github.io
```

`<用户名>` 要和你的 GitHub 用户名完全一致（不区分大小写），博客地址就是 `https://<用户名>.github.io/`。仓库名不对的话，博客地址会变成 `https://<用户名>.github.io/<仓库名>/`。

新建时**不要**勾选添加 README、.gitignore 或 License，保持空仓库。

## 九、添加 GitHub Actions 工作流

先在项目根目录建一个 `.gitignore`，不提交构建产物：

```gitignore
public/
resources/_gen/
.hugo_build.lock
```

然后新建 `.github/workflows/hugo.yml`：

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.169.0
      TZ: Asia/Shanghai
    steps:
      - name: Install Hugo
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build
        env:
          HUGO_ENVIRONMENT: production
        run: |
          hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

几个关键点：

- `HUGO_VERSION` 尽量和本机版本保持一致，避免本地和线上效果不同。
- `submodules: recursive` 一定要加，否则构建时拉不到主题。
- `--baseURL` 使用 GitHub Pages 给出的实际地址，就算 `hugo.toml` 里的 `baseURL` 写错了，也不影响部署。
- `TZ: Asia/Shanghai` 让构建时的时区和文章日期一致，避免当天写的文章被当成“未来文章”跳过。

## 十、推送代码

```bash
git add .
git commit -m "Initial blog setup"
git remote add origin git@github.com:<用户名>/<用户名>.github.io.git
git push -u origin main
```

## 十一、开启 GitHub Pages（最容易漏的一步）

打开仓库的 **Settings → Pages**，把 **Build and deployment → Source** 选成 **GitHub Actions**。

**这一步一定要做。** 我第一次推送后访问网站是 404，打开 Actions 页面一看，有两个工作流都显示成功：

1. 我们自己的 Hugo 工作流
2. GitHub 默认的 `pages build and deployment`（用 Jekyll 构建）

Source 默认是 **Deploy from a branch**，GitHub 会把仓库里的 Hugo 源码直接当成 Jekyll 网站来构建。它比 Hugo 工作流晚完成，就把我们的部署结果覆盖了，仓库根目录又没有 `index.html`，于是首页就是 404。

把 Source 改成 **GitHub Actions** 后，在 **Actions** 页面打开 Hugo 工作流，点 **Re-run all jobs** 重新运行。一两分钟后访问 `https://<用户名>.github.io/` 就能看到博客了。

如果还是 404，先用 `Cmd + Shift + R`（Windows 用 `Ctrl + F5`）强制刷新，或者换无痕窗口打开，浏览器可能缓存了之前的 404 页面。

## 日常写作流程

博客上线以后，写文章只需要这几步：

```bash
hugo new content posts/xxx.md   # 新建文章
hugo server -D                  # 本地预览
# 写完后把 draft 改成 false
git add .
git commit -m "新文章：xxx"
git push                        # 推送后 GitHub Actions 会自动部署
```

## 常见问题速查

| 现象 | 原因 | 解决办法 |
|------|------|------|
| 界面显示英文 | 语言代码写成了 `zh-cn` | 改为 `defaultContentLanguage = 'zh'` |
| 搜索没有结果 | 没有生成 `index.json` | `[outputs]` 里加上 `JSON` |
| 文章没有出现 | 日期是未来时间，或者 `draft: true` | 修改日期，或把 draft 改成 false |
| 线上没有主题样式 | 构建时没拉到 submodule | checkout 步骤加 `submodules: recursive` |
| 部署成功但网站 404 | Pages 的 Source 不是 GitHub Actions | 改成 GitHub Actions，然后重新运行工作流 |
| 修改 CSS 后页面没变 | 本地服务没检测到文件变化 | 重启 `hugo server` |

## 参考链接

- [Hugo 官方文档](https://gohugo.io/documentation/)
- [PaperMod Wiki](https://github.com/adityatelange/hugo-PaperMod/wiki)
- [GitHub Pages 文档](https://docs.github.com/pages)
- [Hugo 官方的 GitHub Pages 部署说明](https://gohugo.io/host-and-deploy/host-on-github-pages/)
