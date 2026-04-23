# 项目状态文件 — Agent 跨会话恢复锚点

> 此文件由 agent 在每次 milestone 或调用 /notify 前更新。
> 新会话开始时，agent 必须优先读取此文件恢复状态，不依赖上下文记忆。

---

## 最后更新时间
2026-04-23 11:50

## 当前阶段
**Milestone 2：站点上线 GitHub Pages**

## 已完成的工作
- [x] 初始化项目仓库（原 Hexo 生成的静态站）
- [x] 切换 git config 到个人账号（yansong-ai / 1119551805@qq.com）
- [x] 确定目标风格：参照 pi.website 极简设计
- [x] 确定技术方案：纯静态 HTML + CSS，手写页面
- [x] 配置外置记忆工作流（CLAUDE.md + STATUS.md + docs/architecture.md + .claude/notify.md）
- [x] 创建 CSS 样式文件（pi.website 风格复刻）
- [x] 创建首页 index.html（标题/导航/时间轴文章列表）
- [x] 创建文章页模板并迁移「自动驾驶Planning就是个坑」
- [x] 清理旧 Hexo 产出文件（2026/、archives/、tags/、fancybox/、js/、css/images/）
- [x] 仓库改为 Public 并启用 GitHub Pages（Deploy from branch: main / root）
- [x] 站点上线：https://yansong-ai.github.io/ 返回 200
- [x] 移除导航死链 `/blog`
- [x] 填入首页简介（IEEE 作者介绍风格：HIT B.Eng. + BIT M.S. + AGI/具身/自驾）

## 下一步
1. 用 `/new-post posts/<slug>.md` 写新文章（已建好 skill + 模板）
2. 可选：补充简介细节（毕业年份、专业方向、当前职位）
3. 可选：加 email / GitHub / Google Scholar 链接
4. 可选：修 `posts/planning-is-a-trap.html` 里仍存在的 `/blog` 死链

## 发文工作流（skill-based）
1. 复制 `posts/_template.md` → `posts/<slug>.md`
2. 填 frontmatter（title/date/slug/summary/tags）+ 正文
3. 调用 `/new-post posts/<slug>.md`，skill 自动完成 MD→HTML、首页插入、commit、push

## 阻塞项
- 无

## 关键决策记录
| 时间 | 决策内容 | 原因 |
|------|----------|------|
| 2026-04-09 | 放弃 Hexo，改用纯静态 HTML + CSS | pi.website 风格简单，Hexo 过重 |
| 2026-04-09 | 不扒站，手写复刻风格 | pi.website 是 Next.js SPA，扒下来无法直接用 |
| 2026-04-09 | Hello World 文章不迁移 | Hexo 默认文章，无实质内容 |
| 2026-04-09 | 网站标题 Yansong's Blog，导航 Home / Blog | 用户选择 |
| 2026-04-23 | 仓库由 Private 改为 Public | 免费账号启用 GitHub Pages 所需 |
| 2026-04-23 | 暂时移除导航 Blog 链接（而非创建 /blog 页） | 首页已是时间轴，避免死链即可 |
| 2026-04-23 | 首页简介保留空占位（TODO） | 用户暂不希望公开个人信息 |
| 2026-04-23 | 新增 `/new-post` skill + posts 模板 | 用 Markdown 写，skill 负责转 HTML + 发布 |

## 环境信息
- 代码目录: `/Users/mi/Public/workspace/yansong-ai.github.io/`
- 远端 Git: `https://github.com/yansong-ai/yansong-ai.github.io.git`
- 部署方式: GitHub Pages（main 分支）
- 无需虚拟环境，纯静态项目

## Context 水位记录
| 时间 | 水位 | 备注 |
|------|------|------|
| 2026-04-09 11:30 | 中 | 项目初始化 + 工作流配置 |
| 2026-04-09 11:40 | 中 | Milestone 1 完成 |
