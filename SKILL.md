---
name: interactive-explainer-site
description: >-
  Build interactive, first-principles educational & engineering explainer websites
  (inspired by Ohmwork / Gearwork) with 100% reactive inline SVG animations,
  playable 16:9 lesson explainers, unified interactive lab shells, engineering
  calculators, reference catalogs, and dark/light editorial engineering styling.
  Use when the user asks to build an interactive科普/原理教学/构造介绍/工程可视化网站,
  create an Ohmwork-style learning web app, or explain complex mechanical,
  electronic, scientific, or systemic concepts with hands-on animations.
---

# Interactive Explainer Site (第一性原理交互式科普教学站)

一套用于从零打造“克制、高直觉、强互动”原理科普与工程教学网站的端到端方法论与架构规范。适用于任何需要通过**可视化动画 + 动手实验**讲透底层因果机制的学科（如汽车构造、航空原理、电路电子、芯片架构、光学、人体生理、金融机制等）。

## 核心设计铁律

1. **第一性原理直白表达（禁止术语堆砌）**
   - 每个概念必须先给出**生活直觉锚点**（读者已有的身体/日常经验），再展开物理或逻辑因果链（力、流、能量、信号路径），最后才引出专业术语与公式。
   - 动画画面必须直接回答“谁在推谁、能量往哪流、为什么会这样”，禁止只画静态框图加几个闪烁圆点充数。
2. **双层动画分工**
   - **被动导学层（16:9 `ExplainerPlayer`）**：每课顶部配 20–40 秒可拖动/倍速动画 + 字幕 + 可点击跳转的时间轴文稿，30 秒内建立全局直觉。
   - **主动实验层（`LabShell`）**：每课核心嵌入一个可实时调参的互动实验室，强制包含**“极端工况 / 玩坏系统”**体验（例如零阻尼共振、单轮失滑、刚性撞击 vs 溃缩对比、注入强干扰对比差分还原），让用户亲手把系统推到失效边界，从而瞬间理解某项设计为什么存在。
3. **100% React 驱动内联 SVG（零 Canvas）**
   - 全部互动图示与导学动画使用内联 `<svg>`，由 React State + `useRafLoop` 驱动。
   - 图形坐标、物理数值、控件状态、底部动态白话结论共享同一份状态源，保证 100% 同步；颜色全部走 CSS 变量，天然适配浅/深色主题且矢量缩放零锯齿。
4. **克制的工程手账美学（Editorial Engineering）**
   - 中性纯白/微灰画布 + 发丝线边框，**把彩色严格留给物理语义**（`--cat-*`）；禁用大面积霓虹渐变、重投影、玻璃拟态套娃卡片和装饰性 Emoji。

---

## 标准 4 阶段工作流

### 阶段 1：学科拆解与第一性原理大纲设计

在写代码前，先完成课程与实验映射表（默认 **4 阶段 × 3 课 = 12 课**，也可按用户需求缩减或扩展）：
1. **划分学习阶梯**：按“能量/源头 → 传递/转换 → 控制/执行 → 系统/协同”或由浅入深的因果链分为 3–4 个 Stage。
2. **每课定义 4 个核心元素**：
   - **一句话生活类比**（例如“差速器像两人抬担架转弯，内侧碎步、外侧大步”）；
   - **16:9 导学动画的 4–5 个时间节点（`ExplainerCue`）**；
   - **核心互动实验室的 2–3 个控制变量 + 至少 1 个极端/对照工况**；
   - **2 道随堂判断/选择题（`InlineQuiz`）**，选项需针对常见认知误区，解析必须回归第一性原理。
3. **规划横向辅助页**：
   - **工程计算器（Tools）**：3–4 个真实量化直觉工具（实时滑块 + 可视化对比条）；
   - **实体图鉴 / 结构速查（Parts/Catalog）**：12–20 个核心组件，支持分类筛选并直达对应课程；
   - **术语表（Glossary）**：15–25 个高频术语，含一句话白话定义与典型数值范围。

### 阶段 2：搭建工程骨架与设计系统

- **推荐技术栈**：Vite + React 18 + TypeScript + Tailwind CSS v4 + Lucide React，Hash 路由（`#/`, `#/learn/:slug`, `#/tools`, `#/parts`, `#/glossary`），`localStorage` 持久化完成进度、浅/深色主题与侧边栏折叠状态。
- **必读参考**：
  - 完整 CSS Token、Tailwind v4 `@theme inline` 映射与滑块样式 → [references/design-system.md](./references/design-system.md)
  - 目录树、双栏 App Shell、5 类页面模板与核心组件契约 → [references/architecture-and-components.md](./references/architecture-and-components.md)
- **首页设计要点**：Hero 区域采用左文右图布局，右侧直接内嵌全站最具代表性的 1 个核心实验室（`compact` 模式），让用户进站 3 秒就能上手拖动体验。

### 阶段 3：实现 SVG 互动实验室与课程内容

- **必读参考**：SVG 第一性原理视觉翻译、几何防重叠规则与工程避坑清单 → [references/svg-labs-and-qa.md](./references/svg-labs-and-qa.md)
- **强约束检查**：
  - 所有实验室统一包裹在 `<LabShell>` 中（标题 + 重置 → 下沉式 SVG 舞台 → 语义色点控件 → `tabular-nums` 指标条 → 随参数实时改写的白话总结句）。
  - 物理/运动学动画使用 `useRafLoop`（切后台自动暂停 + `dt <= 0.05s` 限幅；二阶微分方程如弹簧阻尼使用固定子步积分防止发散）。
  - SVG 内部严禁硬编码 `#000` / `#fff` 作为前景文字或描边，必须使用 `var(--foreground)` / `var(--card)` / `var(--cat-*)`。

### 阶段 4：构建校验、多视口浏览器 QA 与直白度复审

完成编码后**禁止直接交付**，必须执行以下闭环验收：
1. **静态与打包校验**：运行 `npm run build`（含 `tsc -b && vite build`），确认 0 TypeScript 错误；用 grep 核查打包 CSS 中确实生成了 `@theme inline` 对应的语义类名（如 `bg-border`、`bg-sunken`、`hover:bg-muted`）。
2. **真实浏览器视觉 QA**：启动开发服务器，按 [references/svg-labs-and-qa.md](./references/svg-labs-and-qa.md) 的 4 视口清单检查：
   - 桌面展开 `1440×900`（浅色）
   - 桌面收起侧栏 `1080×800`
   - 深色模式全页与 SVG 对比度
   - 移动端 `390×844`（抽屉菜单、滑块与图表防溢出）
   - 控制台 0 Console Error / 0 Page Error
3. **第一性原理二次审视**：把自己当成零基础读者，逐课拖动每个滑块到最小/最大值与切换所有开关，检查：
   - 不看文字，光看动画能否一眼看懂因果方向与受力变化？
   - 极值状态下是否存在 SVG 标签遮挡、截断、超出 `viewBox` 或图形穿模？
   - 底部动态总结句是否准确反映了当前极端工况？

---

## 下次启动新项目的极简提示词模板

本 Skill 已承载所有架构、视觉、组件与 QA 规范，下次开发同类网站时，只需提供类似下面的 4–5 行主题需求即可：

```text
请使用 interactive-explainer-site 技能，创建一个介绍「【主题名称，如：飞机飞行原理与航空构造】」的交互式科普网站。
- 品牌名：【英文名 + 中文名】
- 目标读者：【如：零基础科技爱好者 / 理工科初学者】
- 课程规模：【默认 4 阶段 12 课，或自定义阶段与核心实验室列表】
- 重点互动实验：【列举 3–5 个最想做深的核心机制，如机翼升力与失速、涡扇引擎、飞控舵面】
```
