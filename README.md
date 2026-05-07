# yansong-ai.github.io

**Live**: https://yansong-ai.github.io/

## 技术栈

- 纯静态 HTML + CSS，无构建工具、无前端框架
- GitHub Pages 部署（`main` 分支根目录）
- 文章用 Markdown 写，由 Claude Code skill 转成 HTML 后发布

## 目录结构

```
.
├── index.html             # 首页（导航 + 简介 + 文章时间轴）
├── css/style.css          # 站点样式
├── posts/                 # 文章源（.md）和发布产物（.html）
│   ├── _template.md       # 新文章模板
│   ├── _template.html     # 文章页 HTML 模板
│   └── <slug>.{md,html}
├── assets/                # 图片等静态资源
├── docs/architecture.md   # 架构与技术选型记录
├── CLAUDE.md              # Claude Code agent 执行手册
├── STATUS.md              # 跨会话进度快照（agent 维护）
└── .claude/skills/        # 项目级 Claude Code skill
    └── new-post/SKILL.md  # /new-post 发布流水线
```

## 写一篇新文章

```bash
cp posts/_template.md posts/my-new-post.md
# 编辑 frontmatter (title/date/slug/summary/tags) 和正文
```

然后在 Claude Code 里：

```
/new-post posts/my-new-post.md
```

skill 会自动完成 Markdown → HTML 转换、首页时间轴插入、`git commit && push`。
详细规则见 [.claude/skills/new-post/SKILL.md](.claude/skills/new-post/SKILL.md)。

支持的 Markdown 语法：标题、段落、列表、引用、加粗/斜体、行内代码、链接、MathJax 公式（`$...$` 和 `$$...$$`）。暂不支持代码块、图片、表格——遇到时 skill 会提示。

## 本地预览

```bash
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

## 换机器后如何继续

仓库已自带：
- `CLAUDE.md` — agent 执行手册（Claude Code 启动时自动加载）
- `.claude/skills/new-post/` — 发布 skill
- `STATUS.md` — 上次离开时的进度

新机器 `git clone` 之后，在 Claude Code 里直接 `/new-post` 即可继续写。本地权限白名单（`.claude/settings.local.json`）每台机器各自维护，未纳入 git。
