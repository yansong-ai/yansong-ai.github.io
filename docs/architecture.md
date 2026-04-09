# 技术架构设计

> 此文件由用户填写/确认，描述项目的技术路线与架构决策。
> Agent 在执行前应阅读此文件，理解整体方向后再制定实施计划。

---

## 项目目标

构建一个参照 [pi.website](https://www.pi.website/) 风格的个人博客，极简、有逼格、易维护。

## 整体架构

```
Markdown 文章 → (手动/脚本转换) → HTML 页面 → GitHub Pages 部署
```

纯静态站，无框架依赖，无构建工具。

## 技术选型

| 组件 | 选型 | 原因 |
|------|------|------|
| 页面结构 | 纯 HTML | 最简单，GitHub Pages 原生支持 |
| 样式 | 手写 CSS | pi.website 风格极简，不需要 Tailwind 等框架 |
| 字体 | 系统等宽字体 + 衬线字体 | 复刻 pi.website 的字体风格 |
| 部署 | GitHub Pages (main 分支) | 已有仓库 yansong-ai.github.io |
| 文章格式 | Markdown → HTML | 用户日常用 Markdown 写作 |

## 设计规范（参照 pi.website）

- **布局**：单栏，max-width 约 600px，左对齐
- **配色**：黑白为主，极少装饰色
- **字体**：正文等宽字体（monospace），标题衬线字体（serif）
- **导航**：顶部简洁导航栏（下划线链接风格）
- **文章列表**：左侧时间轴 + 标题 + 日期 + 摘要
- **响应式**：移动端适配

## 实现路径（里程碑拆解）

- **Milestone 1**：首页实现（导航 + 简介 + 文章时间轴列表）
- **Milestone 2**：文章详情页模板
- **Milestone 3**：迁移现有 Hexo 文章内容
- **Milestone 4**：Markdown → HTML 构建脚本（可选，按需）

## 关键约束

- 不使用任何前端框架（React、Vue 等）
- 不使用构建工具（webpack、vite 等）
- CSS 不依赖 Tailwind 等工具库，手写即可
- 保持文件结构简洁

## 待定决策

- 用户个人信息（网站标题、简介文案、导航结构）—— 等用户确认
- 是否需要 Markdown 自动构建脚本 —— 文章数量多了再考虑
