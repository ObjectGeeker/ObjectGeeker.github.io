---
title: "Hugo 快速上手指南"
date: 2026-11-04T12:10:00+08:00
draft: false
tags: ["Hugo", "教程", "静态网站", "入门"]
categories: ["技术", "教程"]
description: "学习如何使用 Hugo 搭建个人博客"
cover:
  image: "https://images.unsplash.com/photo-1517694712202-14dd9538aa97?w=800"
  alt: "编程工作区"
  relative: false
---

## 什么是 Hugo？

Hugo 是一个用 Go 编写的静态网站生成器，具有以下特点：

- ⚡ **极快的构建速度** - 毫秒级构建
- 🎨 **丰富的主题** - 开箱即用
- 📝 **Markdown 支持** - 专注内容创作
- 🔧 **高度可定制** - 满足各种需求

## 快速开始

### 安装 Hugo

```bash
# macOS
brew install hugo

# Windows
choco install hugo-extended

# Linux (snap)
snap install hugo --channel=extended
```

### 创建新站点

```bash
hugo new site myblog
cd myblog
```

### 添加主题

```bash
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
```

### 创建第一篇文章

```bash
hugo new content posts/my-first-post.md
```

## 目录结构

```
myblog/
├── content/     # 文章内容
├── layouts/     # 自定义布局
├── static/      # 静态资源
├── themes/      # 主题文件
└── hugo.toml    # 配置文件
```

## 常用命令

| 命令 | 说明 |
|------|------|
| `hugo server` | 启动本地服务器 |
| `hugo new content` | 创建新内容 |
| `hugo` | 构建静态网站 |
| `hugo --gc --minify` | 构建并优化 |

## 部署到 GitHub Pages

1. 在 GitHub 创建仓库 `username.github.io`
2. 推送生成的 `public/` 目录
3. 访问 `https://username.github.io`

---

更多信息请参考 [Hugo 官方文档](https://gohugo.io/documentation/)。
