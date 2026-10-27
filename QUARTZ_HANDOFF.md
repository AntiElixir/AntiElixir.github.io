# Quartz 笔记站点搭建 — WSL Agent Handoff

> 目标：在 WSL 中搭建 Quartz 静态站点，将 Obsidian 笔记发布到 `https://antielixir.github.io/notes/`，并修改个人主页导航指向该站点。

---

## 〇、Windows 侧已完成的变更

> 以下变更已在 Windows 侧完成，WSL agent **无需重复执行**。

| 文件 | 变更 |
|------|------|
| `_data/settings.yml` | Notes 导航改为外部链接 `https://antielixir.github.io/notes/`，添加 `external: true`；删除 `notes:` 配置块 |
| `_layouts/default.html` | header 和 footer 导航支持 `external` 标记（`target="_blank"`）；移除 notes 集合相关 SEO 逻辑和 og:type 引用 |
| `_config.yml` | 删除 `permalink`、`jekyll-paginate`、`paginate`/`paginate_path`、`notes` 集合定义、notes 默认 layout |
| `Gemfile` | 删除 `jekyll-paginate` 依赖 |
| `_notes/` | 整个目录已删除（7 个笔记文件） |
| `notes.md` | 已删除 |
| `_layouts/note.html` | 已删除 |

**Windows 侧待执行**：`bundle install`（移除 jekyll-paginate 后更新 Gemfile.lock）、commit 并 push。

---

## 一、整体架构

```
┌─────────────────────────────────────────────────────────┐
│  个人主页 (Jekyll)                              [已完成] │
│  repo: AntiElixir/AntiElixir.github.io                  │
│  部署: https://antielixir.github.io/                     │
│  Notes 导航 → 外部链接，新标签页打开                      │
└────────────────────────┬────────────────────────────────┘
                         │ 点击 Notes
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Quartz 笔记站点                              [待搭建]   │
│  repo: AntiElixir/notes                                  │
│  部署: https://antielixir.github.io/notes/               │
│  内容源: 软链接 → /home/hml/workspace/obsidian/vault/    │
│    ├── TopicNotes/   (主题笔记)                          │
│    └── BookReview/   (书评)                              │
└─────────────────────────────────────────────────────────┘
```

**关键优势**：Quartz 和 Obsidian vault 同在 WSL Linux 文件系统中，软链接完全原生支持，不存在跨文件系统问题。Git 会将软链接指向的 `.md` 文件作为普通文件跟踪和提交。

---

## 二、Quartz 仓库搭建（在 WSL 中执行）

### 2.1 创建仓库

```bash
cd /home/hml/workspace

# 克隆 Quartz 官方模板
git clone https://github.com/jackyzha0/quartz.git notes
cd notes

# 修改 remote 指向你的仓库
git remote set-url origin git@github.com:AntiElixir/notes.git
git push -u origin main
```

> 如果 GitHub 连接失败，设置代理：`export https_proxy=http://127.0.0.1:7892`

### 2.2 配置 Quartz

编辑 `quartz.config.ts`，关键配置项：

```typescript
const config: QuartzConfig = {
  site: {
    title: "Mingliang's Notes",
    description: "笔记、读书心得与研究记录",
    baseUrl: "antielixir.github.io/notes",  // ← 关键：子路径部署，无协议无末尾斜杠
    lang: "zh-CN",
  },
}
```

### 2.3 配置内容源（软链接）

Quartz 默认读取仓库内的 `content/` 目录。由于 Quartz 和 Obsidian vault 同在 WSL 中，直接用软链接：

```bash
cd /home/hml/workspace/notes

# 删除默认的 content 目录
rm -rf content

# 创建软链接指向 Obsidian vault 中的笔记目录
ln -s /home/hml/workspace/obsidian/vault/TopicNotes content/TopicNotes
ln -s /home/hml/workspace/obsidian/vault/BookReview content/BookReview

# 可选：链接其他目录
ln -s /home/hml/workspace/obsidian/vault/SourceNotes content/SourceNotes
```

**为什么软链接可行**：
- Quartz 和 Obsidian vault 都在 WSL 的 ext4 文件系统上，软链接完全原生
- Git 会将软链接解析为实际文件内容并提交（不是只存一个链接指针）
- Obsidian 中修改笔记 → 保存 → 在 Quartz 仓库 `git add . && git commit && git push` 即可同步

### 2.4 本地测试

```bash
cd /home/hml/workspace/notes

# 安装依赖（首次）
npm install

# 启动开发服务器
npx quartz build --serve
# 访问 http://localhost:8080 查看效果
```

验证要点：
- Wiki-links `[[xxx]]` 能正确跳转
- Callouts `> [!note]` 能正确渲染
- LaTeX 公式能正确显示
- 中文内容无乱码

### 2.5 提交并推送

```bash
git add .
git commit -m "Initial Quartz setup with Obsidian vault symlinks"
git push origin main
```

---

## 三、GitHub Pages 部署配置

### 3.1 创建 GitHub Actions 工作流

在 Quartz 仓库中创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Quartz site to Pages

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
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build Quartz
        run: npx quartz build

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public  # Quartz 构建输出目录

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

### 3.2 配置 GitHub 仓库设置

1. 先在 GitHub 上创建 `AntiElixir/notes` 仓库（空仓库，不要初始化 README）
2. 访问 `https://github.com/AntiElixir/notes/settings/pages`
3. **Source** 选择：**GitHub Actions**（不是 "Deploy from a branch"）
4. 保存后，下次 push 会自动部署

### 3.3 验证部署

部署完成后访问：`https://antielixir.github.io/notes/`

---

## 四、Quartz 插件配置（可选定制）

编辑 `quartz/plugins.ts`，确保 Obsidian 特有语法被正确处理：

```typescript
export const plugins: QuartzPluginConfiguration = {
  transformers: [
    Plugin.FrontMatter(),
    Plugin.HardLineBreaks(),
    Plugin.ObsidianFlavoredMarkdown({
      comments: true,        // %%注释%%
      highlight: true,       // ==高亮==
      wikilinks: true,       // [[链接]]
      callouts: true,        // > [!type]
    }),
    Plugin.Latex({ renderAll: true }),
    Plugin.GitHubFlavoredMarkdown(),
    Plugin.CrawlLinks({ markdownLinkResolution: "shortest" }),
  ],
  filters: [Plugin.RemoveDrafts()],
}
```

> Quartz 默认已启用大部分 Obsidian 兼容插件，以上仅为确认清单。

---

## 五、验证清单

### Quartz 站点（本地）
- [ ] `npx quartz build --serve` 无报错
- [ ] Wiki-links `[[xxx]]` 正确跳转
- [ ] Callouts `> [!note]` 正确渲染
- [ ] LaTeX 公式正确显示
- [ ] 中文内容无乱码
- [ ] 图片正常加载

### GitHub Pages 部署
- [ ] Push 后 Actions 构建成功
- [ ] `https://antielixir.github.io/notes/` 可访问
- [ ] 子页面链接正确（非 404）
- [ ] CSS/JS 资源加载正常

### 个人主页联动
- [ ] 导航栏 Notes 链接指向 `https://antielixir.github.io/notes/`
- [ ] 点击后在新标签页打开
- [ ] Jekyll 构建无报错（`bundle exec jekyll build`）

---

## 六、后续维护

### 添加新笔记
1. 在 Obsidian 中写入 `TopicNotes/` 或 `BookReview/`
2. 在 WSL 中进入 Quartz 仓库：`cd /home/hml/workspace/notes`
3. `git add . && git commit -m "Add new notes" && git push`
4. GitHub Actions 自动重新部署

### 自动同步脚本（可选）
```bash
# 在 WSL 中设置 cron，每小时自动同步
crontab -e
# 添加：
0 * * * * cd /home/hml/workspace/notes && git add -A && git diff --cached --quiet || (git commit -m "Auto sync $(date +\%Y-\%m-\%d\ \%H:\%M)" && git push)
```

---

## 七、故障排查

### Quartz 构建失败
```bash
rm -rf node_modules package-lock.json
npm install
```

### GitHub Pages 404
- 检查 `quartz.config.ts` 中 `baseUrl` 是否为 `antielixir.github.io/notes`（无协议、无末尾斜杠）
- 检查 Actions 日志中的构建错误

### 软链接中的文件未被 Git 跟踪
```bash
# 确认软链接正确
ls -la content/
# 应该显示 -> /home/hml/workspace/obsidian/vault/TopicNotes

# Git 会解析软链接并将文件内容提交，确认：
git add content/
git status
```

---

## 八、参考资源

- [Quartz 官方文档](https://quartz.jzhao.io/)
- [Quartz GitHub 仓库](https://github.com/jackyzha0/quartz)
- [GitHub Pages 子路径部署](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)

---

**Handoff 时间**: 2026-10-28
**Windows 侧状态**: 已完成（导航改动 + 笔记集合清理）
**WSL 侧预期耗时**: 1-2 小时（含调试）
