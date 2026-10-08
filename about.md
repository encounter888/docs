# 关于

## 关于我

- GitHub：[@encounter888](https://github.com/encounter888)
- 这个文档站是我的个人知识库，主要记录折腾过程中的方法与坑

## 关于本站

| 项目 | 说明 |
| --- | --- |
| 框架 | [Docsify](https://docsify.js.org) v5，纯前端渲染 Markdown |
| 主题 | 官方 `core` + `vue` 附加主题，跟随系统自动切换深色模式 |
| 搜索 | Docsify 官方 search 插件，全文检索 |
| 托管 | GitHub Pages |
| 构建 | 无。不需要 CI，也不需要打包 |

## 为什么选 Docsify

对比过几种方案，最后选它的理由是**零构建**：

| 方案 | 构建步骤 | 适合场景 |
| --- | --- | --- |
| **Docsify** | 无，浏览器实时渲染 | 轻量笔记、快速迭代 |
| VitePress | 需要 `build` | 大型文档、要 SEO |
| MkDocs Material | 需要 Python 构建 | Python 生态项目 |
| Docusaurus | 需要 `build` | 带博客的多功能站点 |

代价是搜索引擎抓不到内容（因为正文是 JS 渲染出来的）。如果哪天需要 SEO，再迁移到 VitePress 即可，Markdown 原文可以直接复用。

## 目录结构

```
docs/
├── index.html          # 站点配置（Docsify 的入口）
├── _sidebar.md         # 左侧目录
├── README.md           # 首页
├── .nojekyll           # 必须存在，见下方说明
├── about.md
├── guide/
│   ├── quickstart.md
│   └── github-pages.md
└── notes/
    └── deploy-dist.md
```

> **`.nojekyll` 为什么必须存在？**
> GitHub Pages 默认会跑一遍 Jekyll，而 Jekyll 会忽略所有下划线开头的文件。`_sidebar.md` 正好以下划线开头，不加这个空文件，左侧目录就会整个消失。