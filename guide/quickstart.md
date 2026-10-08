# 快速开始

本站没有构建步骤，新增一篇文档只需要三步。

## 第一步：新建 Markdown 文件

在仓库里 **Add file → Create new file**，路径就是你想要的网址。

| 文件名 | 生成的网址 |
| --- | --- |
| `guide/quickstart.md` | `.../docs/#/guide/quickstart` |
| `notes/deploy-dist.md` | `.../docs/#/notes/deploy-dist` |

目录可以随便嵌套，Docsify 会按路径去找。

## 第二步：写内容

直接写标准 Markdown 就行：

```markdown
# 标题

正文段落，支持 **加粗**、*斜体*、`行内代码`。

## 二级标题

- 列表项
- 列表项

> 引用块
```

代码块记得标语言，会自动高亮：

```js
const greet = (name) => `Hello, ${name}`;
console.log(greet('world'));
```

## 第三步：挂到侧边栏

打开 `_sidebar.md`，加一行：

```markdown
- **实践笔记**
  - [部署前端 dist 产物](notes/deploy-dist)
  - [新加的这篇](notes/new-page)      <!-- 新增 -->
```

路径**不要**写 `.md` 后缀，直接在括号里写相对路径即可。

## 常见问题

**改完页面没有变化？**
浏览器缓存。按 `Ctrl + Shift + R`（Mac 用 `Cmd + Shift + R`）强制刷新。

**侧边栏点不动 / 跳 404？**
检查两点：一是链接路径拼写，二是仓库根目录有没有 `.nojekyll` 文件。

**想加图片？**
把图片传到仓库（比如放进 `assets/`），然后：

```markdown
![说明文字](assets/screenshot.png)
```

**支持 emoji 吗？**
支持，直接用就行 😄 🚀 ✅