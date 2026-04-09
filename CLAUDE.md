# Agent 执行手册

此文档是该项目的 agent 执行手册，LLM 可以以此文档为锚点。

---

## 上下文恢复协议（每次新会话 / clear 后必读）

**触发条件：** 用户执行 `/clear` 或新会话开始时，agent 必须立即执行以下步骤，不得依赖上下文记忆：

1. 读取 `STATUS.md` —— 了解当前阶段、已完成工作、下一步任务、阻塞项
2. 根据 STATUS.md 的"下一步"章节，确认即将执行的任务
3. 向用户简要汇报恢复结果："已恢复到 [当前阶段]，准备执行 [下一步]"，然后继续工作

**两个锚点文档的分工：**
- `CLAUDE.md`（本文件）：规则 / 框架 / 工作方式，**由用户维护**，喂给 agent 作为入口
- `STATUS.md`：当前进度快照，**由 agent 维护**，每次 milestone 后更新

---

## 任务描述

> 参照 [Physical Intelligence 官网](https://www.pi.website/) 的极简风格，为用户构建一个个人博客静态网站。
>
> - 纯静态 HTML + CSS，部署在 GitHub Pages（仓库：yansong-ai.github.io）
> - 风格：等宽字体 + 衬线标题、左侧时间轴、极简黑白配色、最大宽度约 600px
> - 内容以 Markdown 博客文章为主，不涉及复杂交互
> - 需要一套简单的 Markdown → HTML 的构建方式，方便用户日常更新

具体技术路线参考 `docs/architecture.md`。架构非一成不变，agent 认为不合理时可向用户反馈，用户决策后修改。

---

## 工作规则

---

## 工作模式

框架搭建完全由 agent 自驱完成。工作流为：

```
规划方案 → 实施方案 → 运行代码/指令 → 获取执行反馈 → 更新 STATUS.md → 根据结果决定下一步
```

每次执行任务尽量多迭代循环，只有在遇到**必须的决策点**或**无法独立解决的环境问题**时才向用户反馈。

### 状态外化规则

每次取得阶段性进展或即将调用 `/notify` 时，必须将当前状态写入 `STATUS.md`（格式见该文件）。
此文件是 agent 跨会话恢复状态的唯一锚点，每次新会话开始时优先读取，不依赖上下文记忆。

---

## 向用户反馈的方式

在 `.claude/notify.md` 中定义了 `/notify` skill，可自定义消息内容发送给用户。
发送后停止当前任务，等待用户上线给出新的指示。

**每次调用 `/notify` 时，消息必须包含：**
1. 本次通知的主要内容（进展 / 问题 / 决策点）
2. 当前 context 水位评估（低 / 中 / 高），若为"高"则提示用户考虑执行 `/compact` 或 `/clear`

### 何时向用户反馈

1. 遇到网络 / 环境问题，agent 无法独立解决
2. 遇到重大决策点（架构选择、方向调整）
3. 取得阶段性 milestone 进展
4. context 水位评估为"高"时，建议用户执行 `/compact`

---

## 代码仓库

### 本地代码路径
`/Users/mi/Public/workspace/yansong-ai.github.io/`

### 远端仓库
`https://github.com/yansong-ai/yansong-ai.github.io.git`

每次取得 milestone 时，将代码 commit 并 push，然后通过 `/notify` 通知用户。
代码空间可认为无限，可随意创建与删除分支。

---

## 运行环境

本项目为纯静态网站（HTML + CSS），无需 Python 虚拟环境。

构建/预览工具（按需安装）：
```bash
# 本地预览（可选）
python3 -m http.server 8000
# 或
npx serve .
```

---

## 网络环境

当前为公网环境，无特殊网络限制。可正常访问 GitHub、CDN、npm 等外部资源。

---

## 运行规则

1. 每次尽可能完成更多任务，退出运行状态时**必须**使用 `/notify` 通知用户
2. 优先将进展 / 现状 / 结论写入 `STATUS.md`，以文件存储换取昂贵的 context 空间
3. `/compact` 和 `/clear` 是 CLI 内部命令，agent 无法自主调用，只能由用户手动执行。agent 的职责是：在每次 `/notify` 消息中报告 context 水位，由用户决定是否压缩
4. 上下文恢复：见文档顶部"上下文恢复协议"章节

---

## 工程规范

### 文件结构

```
yansong-ai.github.io/
├── 文档（根目录）
│   ├── CLAUDE.md          ← Agent 执行手册（本文件）
│   ├── STATUS.md          ← 进度快照（Agent 维护）
│   ├── docs/
│   │   └── architecture.md ← 架构设计
│   └── README.md          ← 项目概览
│
├── index.html             ← 首页
├── css/
│   └── style.css          ← 样式文件
├── posts/                 ← 博客文章（Markdown 或 HTML）
└── assets/                ← 图片等静态资源
```

**规则：**
- 新增博客文章 → `posts/`
- 静态资源（图片等）→ `assets/`
- **禁止**在根目录散落临时文件
