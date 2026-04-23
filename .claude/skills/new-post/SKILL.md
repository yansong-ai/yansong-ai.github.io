---
name: new-post
description: Publish a Markdown draft from posts/*.md to the live blog. Converts MD to HTML, updates the homepage timeline, then commits and pushes. Trigger when the user types /new-post, or says "publish this draft", "发这篇", "推送新文章".
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Bash(git add *)
  - Bash(git commit *)
  - Bash(git push *)
  - Bash(git status)
  - Bash(git diff *)
  - Bash(curl -sI *)
  - Bash(ls *)
---

# /new-post — Markdown 草稿发布

本 skill 把 `posts/<slug>.md` 转成 `posts/<slug>.html`，插入首页时间轴，提交并推送到 GitHub。

## 参数

`$ARGUMENTS` 应为草稿路径，例如 `posts/my-new-post.md`。

若未提供：列出 `posts/*.md`（排除 `_template.md`），让用户选一个。

## 执行步骤

### 1. 读草稿 + 解析 frontmatter

必填字段（缺任何一项：报错并告诉用户补什么）：
- `title` — 文章标题
- `date` — 发表日期，格式 `April 23, 2026`
- `slug` — URL 片段（英文，小写，用 `-` 分隔），决定 HTML 文件名
- `summary` — 1–2 句摘要
- `tags` — 数组，至少 1 个

### 2. 生成 HTML 文件

- 读 `posts/_template.html`
- 替换占位符:
  - `{{TITLE}}` → title
  - `{{SUMMARY}}` → summary（转义 HTML 特殊字符: `< > & " '`）
  - `{{DATE}}` → date
  - `{{TAGS}}` → tags 用 ` / ` 拼接，例如 `自动驾驶 / Planning / 行业观察`
  - `{{CONTENT}}` → MD body 转换后的 HTML
- 写入 `posts/<slug>.html`。若已存在，询问用户是否覆盖。

### 3. Markdown → HTML 转换规则

支持的语法（逐行/逐块处理，不用外部工具）:

| MD | HTML |
|---|---|
| `## 文本` | `<h3>文本</h3>` |
| `### 文本` | `<h4>文本</h4>` |
| `> 文本` | `<blockquote><p>文本</p></blockquote>`（连续 `>` 行合并成一个 blockquote） |
| `- 项` | `<ul><li>项</li>...</ul>`（连续 `-` 行合并成一个 ul） |
| `1. 项` | `<ol><li>项</li>...</ol>` |
| 空行分隔的段落 | `<p>文本</p>` |
| `**x**` | `<strong>x</strong>` |
| `*x*` 或 `_x_` | `<em>x</em>` |
| `` `code` `` | `<code>code</code>` |
| `[text](url)` | `<a href="url">text</a>` |

**暂不支持**（遇到时提醒用户、询问处理方式）:
- 图片 `![alt](url)`
- 代码块 ` ``` ` 
- 表格
- 脚注
- HTML 原样嵌入

缩进：每一行 HTML 前加 8 个空格，与模板内已有的缩进对齐（参考 `posts/planning-is-a-trap.html` 的风格）。

### 4. 更新首页 `index.html`

用 Edit 工具在 `<ul class="timeline-list">` 开始标签之后、最前面插入新的 `<li>`（最新文章在上）:

```html
        <li>
          <a class="timeline-item" href="/posts/<slug>.html">
            <div class="timeline-item-header">
              <span class="timeline-item-title">{{TITLE}}</span>
              <span class="timeline-item-date">{{DATE}}</span>
            </div>
            <div class="timeline-item-summary">{{SUMMARY}}</div>
            <div class="timeline-item-tags">
              <span class="tag">{{TAG1}}</span>
              <span class="tag">{{TAG2}}</span>
              ...
            </div>
          </a>
        </li>
```

### 5. 提交并推送

```bash
git add posts/<slug>.md posts/<slug>.html index.html
git commit -m "New post: <title>"
git push origin main
```

commit 信息用英文或中文都行，保持 1 行，不带 Claude 签名的情况下直接提交。如果用户在 CLAUDE.md 里要求了 Co-Authored-By 签名，再加上。

### 6. 验证

推送后告诉用户：「已推送，1-2 分钟后访问 https://yansong-ai.github.io/posts/<slug>.html 验证。」

可选：`curl -sI https://yansong-ai.github.io/posts/<slug>.html` 检查是否 200（注意 Pages 有构建延迟，首次可能还是 404，等一会再试）。

## 草稿保留策略

`posts/*.md` 源文件**一并提交**到仓库，方便日后编辑/回溯。不要删除。
以 `_` 开头的文件（如 `_template.md`, `_template.html`）是模板，跳过，不要当作文章处理。

## 错误处理

- frontmatter 缺字段 → 列出缺什么，不继续
- slug 有冲突 → 询问覆盖或改名
- MD 含不支持的语法 → 列出位置，问用户怎么办
- git push 失败 → 不自动回滚，告诉用户错误信息
