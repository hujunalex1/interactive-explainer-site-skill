# 应用架构与组件契约（Architecture & Components）

## 1. 推荐项目目录结构

保持“数据与框架彻底解耦”：换学科时主要改动 `src/data/`、`src/labs/` 和 `ExplainerVisuals.tsx`，外壳组件直接复用。

```text
src/
├── main.tsx
├── App.tsx                       # 全局双栏 App Shell + 折叠/移动顶栏 + 路由分发 + ⌘K 挂载
├── index.css                     # 设计系统 Token + Tailwind v4 @theme inline
├── types.ts                      # 课程、实验、图鉴、术语统一 TypeScript 类型
├── hooks/
│   ├── useHashRoute.ts           # Hash 路由 (#/, #/learn/:slug, #/tools, #/parts, #/glossary)
│   ├── useLearningState.ts       # localStorage 持久化 completedLessons / theme / sidebarCollapsed
│   └── useRafLoop.ts             # requestAnimationFrame 封装（切后台自动暂停 + dt <= 0.05s 限幅）
├── utils/
│   └── geometry.ts               # 纯数学 SVG 路径生成器（如齿轮、波形、箭头、弹簧）
├── components/
│   ├── Sidebar.tsx               # 264px 粘性侧栏（桌面可折叠 + 移动抽屉）
│   ├── CommandPalette.tsx        # ⌘K / Ctrl+K 全局键盘导航检索
│   ├── LabShell.tsx              # 统一互动实验台外壳 (<figure data-lab="true">)
│   ├── LabControls.tsx           # LabSlider / LabSwitch / LabSegmentedControl
│   ├── ExplainerPlayer.tsx       # 16:9 可播/可拖/可变速导学播放器 + 可折叠时间戳文稿
│   ├── ExplainerVisuals.tsx      # 各课 16:9 导学 SVG 动画实现
│   ├── InlineQuiz.tsx            # 随堂自测（单选 + 即时正误反馈 + 原理剖析）
│   ├── Callout.tsx               # 四类提示框（intuition / warning / engineering / myth）
│   ├── Formula.tsx               # 工程公式展示卡（带符号颜色图例）
│   └── DataTable.tsx             # 简洁对比表格
├── labs/
│   ├── index.tsx                 # LabId -> 组件注册表
│   └── *Lab.tsx                  # 每课一个独立 SVG 互动实验室
├── data/
│   ├── curriculum.ts             # STAGES 与 LESSONS 汇总导出
│   ├── curriculumStage*.ts       # 分阶段课程正文、公式、表格、Quiz、导学 Cue
│   ├── parts.ts                  # 核心部件/实体图鉴数据
│   └── glossary.ts               # 术语词典数据
└── pages/
    ├── HomePage.tsx              # Hero（内嵌核心紧凑版 Lab）+ 阶段路线图 + 工具入口
    ├── LessonPage.tsx            # 线性学习页模板
    ├── ToolsPage.tsx             # 3–4 个真实交互计算器
    ├── PartsPage.tsx             # 分类筛选图鉴网格
    └── GlossaryPage.tsx          # 即时搜索术语表
```

---

## 2. 全局双栏布局与避坑规则

### 左侧粘性侧栏（`Sidebar.tsx`）
- **关键避坑**：桌面端 `<aside>` 必须直接挂 `sticky top-0 h-screen overflow-hidden`，不要把 `sticky` 放在带 `overflow-hidden` 的 `<aside>` 内部子元素上，否则侧栏会随页面滚走。
- 折叠宽度过渡：`w-[264px]` ↔ `w-0 border-r-0`，内部包裹固定 `w-[264px] h-full` 容器避免收起时文字折行闪烁。
- 移动端使用 `fixed inset-0 z-50` 抽屉 + 半透明遮罩，点击任意导航项自动关闭抽屉。
- 底部固定区展示：总学习进度条（`已完成 / 总课数`）+ 浅/深色 `radiogroup` 切换。

### 右侧主区与顶栏（`App.tsx`）
- 移动端（`< lg`）或桌面端侧栏收起（`collapsed === true`）时，渲染正常的 `sticky top-0 z-30 h-12 bg-background/95 backdrop-blur-xs border-b` 顶栏；**禁止使用负 margin（如 `lg:-mt-12`）去抵消高度**，否则会遮挡页面顶部标题或面包屑。
- 课程与文档正文区域保持单栏最佳阅读行宽（`max-w-3xl mx-auto px-4 sm:px-6 lg:px-8`）。

---

## 3. 五类页面结构标准

1. **首页（`HomePage`）**：
   - Hero 区两栏网格：左侧主标语 + 一句话定位 + “从第 1 课开始 / 继续上次进度”按钮；右侧直接渲染全站最具代表性的 `<CoreLab compact />`，零门槛上手。
   - 阶段路线图卡片网格：展示每个 Stage 的目标、完成计数和直达课程列表。
   - 底部横向辅助入口：工程计算器、结构速查、术语表。
2. **课程页（`LessonPage`）**：严格按单向认知流排列：
   1. 阶段面包屑 + 课程编号/难度/时长标签 + 标题 + 一句话摘要
   2. `<ExplainerPlayer>`（16:9 导学动画 + 可折叠时间戳文稿）
   3. 结构化 `ContentBlock[]` 渲染流（引言直觉 Callout → 分节标题与段落 → 对比表/公式卡 → **`<Lab>` 互动实验台** → 2 道 `<InlineQuiz>`）
   4. 本课核心收获（`takeaways` 列表）+ “标记为已完成”大按钮 + 上一课/下一课导航卡
3. **工具箱（`ToolsPage`）**：3–4 个独立计算器卡片，复用 `LabSlider` / `LabSegmentedControl`，结果区同时给精确 `tabular` 数值与直观可视化条（如分段距离条、成本对比条）。注意窄屏（`390px`）低数值分段条不要把文字挤出条块外（文字可置于条块上方或外侧图例）。
4. **结构/实体速查（`PartsPage`）**：顶部按 Stage 分类的过滤按钮 + 响应卡片网格（中英文名、核心作用、典型参数范围、点击直达对应课程）。
5. **术语表（`GlossaryPage`）**：顶部关键词搜索框 + 分类标签 + 高信息密度词条卡片（术语、英文、白话定义、公式或量级、关联课程链接）。

---

## 4. 核心组件契约

### `LabShell`（统一实验台外壳）
每个实验室必须渲染在 `<figure data-lab="true">` 内，包含 5 段标准结构：
1. **Header**：实验标题 + 右侧“恢复初始值”按钮（`onReset`）。
2. **Instruction**（非 `compact` 时显示）：1 句话直接告诉用户“拖哪个滑块、观察什么现象”。
3. **Sunken Stage**（`bg-sunken border-b`）：内联 `<svg>` 主舞台。
4. **Controls**：由 `LabSlider`、`LabSwitch`、`LabSegmentedControl` 组合。
5. **Live Metrics + Dynamic Summary**：
   - 指标条使用 `grid gap-px bg-border` 形成 1px 发丝网格分隔；
   - 底部 `<figcaption>` 必须是**随参数实时变化的动态白话总结**（例如指出当前处于高效区、打滑临界点还是失效状态），不可写死静态文案。

### `LabControls` 关键避坑
- **`LabSlider`**：外层容器不要加 `h-full`（CSS Grid 本身会自动对齐网格项，加 `h-full` 反而会导致在垂直 `space-y-3` 容器里撑爆布局）；右侧数值加 `whitespace-nowrap shrink-0`，且左侧标签简洁（不超过 10 个中文字），避免与数值挤压换行。
- **`LabSwitch`**：不要依赖非标准 Tailwind 偏移类（如 `translate-x-5.5`）或可能与底色同色的圆点，直接使用显式内联样式：
  ```tsx
  style={{
    transform: checked ? 'translateX(23px)' : 'translateX(3px)',
    backgroundColor: checked ? 'var(--primary-foreground)' : 'var(--foreground)',
  }}
  ```
- **Lucide 图标映射类型**：定义图标字典时使用 `Record<IconKey, LucideIcon>`（从 `lucide-react` 引入 `type LucideIcon`），避免 `React.FC` 与 `strokeWidth` 产生 TypeScript 报错。

### `useRafLoop` 动画循环契约
```ts
export function useRafLoop(callback: (dt: number) => void, active = true) {
  const callbackRef = useRef(callback);
  callbackRef.current = callback;

  useEffect(() => {
    if (!active) return;
    let rafId = 0;
    let lastTime = performance.now();

    const loop = (now: number) => {
      const rawDt = (now - lastTime) / 1000;
      lastTime = now;
      if (!document.hidden) {
        const dt = Math.min(Math.max(rawDt, 0), 0.05);
        callbackRef.current(dt);
      }
      rafId = requestAnimationFrame(loop);
    };

    rafId = requestAnimationFrame(loop);
    return () => cancelAnimationFrame(rafId);
  }, [active]);
}
```
对于刚度较高的物理微分方程（如弹簧阻尼系统），在 `callback(dt)` 内拆分为固定子步（如 `subDt = 0.005`，最多 10 步）进行积分，防止帧率波动时数值爆炸。

---

## 5. 核心数据结构骨架（`src/types.ts`）

```ts
export type ContentBlock =
  | { type: 'heading'; text: string }
  | { type: 'paragraph'; text: string }
  | { type: 'table'; data: { caption?: string; headers: string[]; rows: string[][] } }
  | { type: 'lab'; labId: LabId }
  | { type: 'callout'; data: { variant: 'intuition' | 'warning' | 'engineering' | 'myth'; title: string; body: string } }
  | { type: 'formula'; data: { latexLike: string; lhs: string; numerator?: string; denominator?: string; rhsNote?: string; legend: { symbol: string; meaning: string; colorVar?: string }[] } }
  | { type: 'quiz'; data: { id: string; question: string; options: string[]; correctIndex: number; explanation: string } };

export interface ExplainerCue {
  time: number; // 秒
  title: string;
  text: string;
}

export interface LessonData {
  slug: LabId;
  stageId: StageId;
  orderInStage: number;
  globalIndex: number;
  title: string;
  shortTitle: string;
  difficulty: '入门' | '进阶';
  durationMin: number;
  summary: string;
  keyPoints: string[];
  explainerDuration: number;
  explainerCues: ExplainerCue[];
  blocks: ContentBlock[];
  takeaways: string[];
}
```
