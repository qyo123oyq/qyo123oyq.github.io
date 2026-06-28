# qyo123oyq 的博客

个人博客,记录开源贡献、AI 辅助编程实践与技术折腾。

**线上地址:** <https://qyo123oyq.github.io>

## 技术栈

- [Jekyll](https://jekyllrb.com) — 静态站点生成器
- [GitHub Pages](https://pages.github.com) — 免费托管(原生 Jekyll 构建,无需 Actions)
- Markdown — 写文章
- 自定义布局 + 一个手写 CSS(`assets/css/style.css`),支持亮/暗主题

## 目录结构

```
├── _config.yml          # Jekyll 配置
├── _layouts/            # 页面布局(default / home / post / page)
├── _includes/           # 复用组件(header / footer)
├── _posts/              # 博文(文件名格式:YYYY-MM-DD-标题.md)
├── assets/css/style.css # 样式
├── index.html           # 首页(home 布局,自动列出 _posts)
├── about.md             # 关于页
└── 404.html             # 404 页
```

## 写新文章

在 `_posts/` 下新建一个 `YYYY-MM-DD-标题.md` 文件,头部加 front matter:

```markdown
---
layout: post
title: "文章标题"
date: 2026-06-28
tags: [标签1, 标签2]
description: "一句话摘要,会显示在首页卡片和 SEO 里"
---

正文用 Markdown 写……
```

提交到 `main` 分支后,GitHub Pages 会自动构建,文章出现在首页。

## 本地预览

需要 Ruby 环境:

```bash
bundle install
bundle exec jekyll serve
# 打开 http://127.0.0.1:4000
```

## License

文章内容版权归作者所有,代码结构可参考使用。
