# interactive-explainer-site

一个面向 AI Coding Agent（如 Antigravity / Claude Code / Codex 等）的自定义 Skill，用于从零打造**第一性原理交互式科普与工程教学网站**（灵感源自 Ohmwork 与 Gearwork）。

## 核心特性

- **第一性原理教学闭环**：生活直觉类比 → 16:9 可播导学动画 → 因果解构 → 动手互动实验台（含“玩坏系统”的极端工况）→ 随堂自测。
- **100% React 驱动内联 SVG**：零 Canvas，动画坐标、滑块控件、实时指标与动态白话结论共享同一份 React State，原生适配浅/深色主题。
- **克制的工程手账视觉（Editorial Engineering）**：完整验证的 Tailwind CSS v4 `@theme inline` 与 `--cat-*` 物理语义色体系，杜绝霓虹渐变和 AI 模板味。
- **内置工程避坑与多视口 QA 验收清单**：沉淀真实项目踩坑录（Tailwind v4 变量映射、粘性侧栏、SVG 防重叠与 `<clipPath>` 裁剪、`LabSwitch` 浅色显影、4 视口浏览器实测验收）。

## 目录结构

```text
interactive-explainer-site/
├── SKILL.md                                # 主工作流、第一性原理铁律与项目启动模板
└── references/
    ├── design-system.md                    # 视觉规范、语义色板与完整 index.css 模板
    ├── architecture-and-components.md      # 目录骨架、5 类页面模板与核心组件契约
    └── svg-labs-and-qa.md                  # SVG 视觉翻译、实战避坑录与 4 视口 QA 清单
```

## 安装方法

### 全局安装（Antigravity）

```bash
git clone git@github.com:hujunalex1/interactive-explainer-site-skill.git \
  ~/.gemini/config/skills/interactive-explainer-site
```

### 项目级安装

```bash
git clone git@github.com:hujunalex1/interactive-explainer-site-skill.git \
  .agents/skills/interactive-explainer-site
```

## 使用方式

安装完成后，在新对话里只需提供 4–5 行的题材需求即可触发：

```text
请使用 interactive-explainer-site 技能，创建一个介绍「飞机飞行原理与航空构造」的交互式科普网站。
- 品牌名：Airframe（翼构志）
- 目标读者：零基础航空爱好者
- 课程规模：4 阶段共 12 课（含计算器、部件速查与术语表）
- 重点互动实验：机翼伯努利与迎角失速、涡扇发动机气流循环、副翼/升降舵/方向舵三轴姿态、起落架减震与反推制动
```
