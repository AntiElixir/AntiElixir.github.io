# Quartz 笔记站点搭建

在 WSL 中搭建 Quartz，将 Obsidian 笔记发布到 `https://antielixir.github.io/notes/`。

## 前置条件

- GitHub 上创建空仓库 `AntiElixir/notes`（不初始化 README）
- 仓库 Settings → Pages → Source 选 **GitHub Actions**
- Obsidian vault 位于 `/home/hml/workspace/obsidian/vault/`
- 网络不通时：`export https_proxy=http://127.0.0.1:7892`

## Step 1: 克隆 Quartz

```bash
cd /home/hml/workspace
git clone https://github.com/jackyzha0/quartz.git notes
cd notes
git remote set-url origin git@github.com:AntiElixir/notes.git
```

## Step 2: 软链接内容

```bash
rm -rf content
ln -s /home/hml/workspace/obsidian/vault/TopicNotes content/TopicNotes
ln -s /home/hml/workspace/obsidian/vault/BookReview content/BookReview
# 可选：SourceNotes 等
```

同文件系统，软链接原生可用，Git 会正常跟踪文件内容。

## Step 3: 配置 `quartz.config.ts`

```typescript
site: {
  title: "Mingliang's Notes",
  description: "笔记、读书心得与研究记录",
  baseUrl: "antielixir.github.io/notes",  // 无协议，无末尾斜杠
  lang: "zh-CN",
}
```

## Step 4: 确认插件

`quartz/plugins.ts` 中确保以下 transformer 启用（大部分默认已开）：

- `Plugin.ObsidianFlavoredMarkdown({ comments: true, highlight: true, wikilinks: true, callouts: true })`
- `Plugin.Latex({ renderAll: true })`
- `Plugin.CrawlLinks({ markdownLinkResolution: "shortest" })`

## Step 5: 本地验证

```bash
npm install
npx quartz build --serve
# 访问 http://localhost:8080
```

检查：wiki-links 跳转、callouts 渲染、LaTeX 显示、中文无乱码。

## Step 6: 部署

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Quartz to Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: "pages"
  cancel-in-progress: false
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npx quartz build
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/deploy-pages@v4
```

```bash
git add .
git commit -m "Initial Quartz setup"
git push origin main
```

验证：`https://antielixir.github.io/notes/` 可访问。

## 日常维护

```bash
# Obsidian 写完笔记后
cd /home/hml/workspace/notes
git add -A && git commit -m "Update notes" && git push
```

## 故障排查

| 问题 | 解决 |
|------|------|
| 构建失败 | `rm -rf node_modules package-lock.json && npm install` |
| Pages 404 | 检查 `baseUrl` 格式：`antielixir.github.io/notes`（无协议无斜杠） |
| 软链接文件未跟踪 | `git add content/` 后 `git status` 确认 |
