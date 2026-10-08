# 部署前端 dist 产物

把 Vite / React 项目打包后发布到 GitHub Pages 的笔记。

## 两种路线

| | 推 dist 产物 | Actions 自动构建 |
| --- | --- | --- |
| 构建在哪 | 本地 `npm run build` | GitHub 服务器 |
| 仓库里放什么 | 编译后的文件 | 源代码 |
| 改代码后 | 手动重新构建再推 | push 即自动部署 |
| 适合 | 偶尔更新 | 长期维护 |

## 路线一：本地构建后推送

```bash
npm run build
cd dist && touch .nojekyll
```

要点：**推的是 `dist` 里的内容，不是 `dist` 文件夹本身**，否则访问路径会多一层 `/dist/`。

发布目录二选一：

- 仓库根目录 —— 直接覆盖
- `docs/` 目录 —— 然后去 **Settings → Pages** 把 Source 选成 `main` + `/docs`

## 路线二：Actions 自动构建

先把 **Settings → Pages → Source** 改成 **GitHub Actions**，再新建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - id: deployment
        uses: actions/deploy-pages@v5
```

`permissions` 里的 `pages: write` 和 `id-token: write` 是必须的，缺了会直接报权限错误。

## 三个必配项

### 1. base 路径

配置错误的表现是：页面能打开，但样式和 JS 全部 404，页面一片空白。

```js
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  // 项目站点写 '/仓库名/'，用户站点写 '/'
  base: '/myapp/',
  plugins: [react()],
  build: { outDir: 'dist' },
})
```

| 部署位置 | base 应该写 |
| --- | --- |
| `encounter888.github.io`（用户站点） | `/` |
| `encounter888.github.io/myapp/`（项目站点） | `/myapp/` |
| 绑定自定义域名 | `/` |

### 2. .nojekyll

Jekyll 会忽略下划线开头的目录。Vite 一般没事，但 Astro 输出 `_astro/`、Next 输出 `_next/`，不加这个空文件这些资源会全部 404。

方式一：构建后 `touch dist/.nojekyll`。
方式二：在项目 `public/` 目录里放一个空 `.nojekyll`，构建时会自动复制过去。

### 3. SPA 路由兜底

Pages 不支持服务器端 rewrite，直接访问 `/about` 并刷新会得到 404。两种解法：

```bash
# 构建后把首页复制成 404 页，让前端路由接管
cp dist/index.html dist/404.html
```

或者用 `HashRouter`，地址变成 `/#/about`，零配置但 URL 不够干净。

## 框架速查

| 框架 | 关键配置 |
| --- | --- |
| Vite (React / Vue) | `base: '/仓库名/'` |
| Create React App | `package.json` 里加 `"homepage"` |
| Next.js | `output: 'export'` + `basePath` |
| Astro | `site` + `base`，输出目录默认 `dist` |
| Nuxt | `ssr: false` 或 `nuxi generate` |

> 一句话总结：**Actions 负责构建，Pages 只负责发文件**。核心配置就三件事 —— `base` 路径、`.nojekyll`、SPA 的 404 兜底。