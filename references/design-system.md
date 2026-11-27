# 设计系统与样式规范（Design System）

本规范源自 Ohmwork / Gearwork 的“克制工程手账（Editorial Engineering）”视觉体系：极简中性底色、发丝线层级、等宽数值排印，以及严格绑定物理含义的语义色。

---

## 1. 视觉美学红线（Anti-Slop 规则）

- **禁止**大面积蓝紫/霓虹渐变背景、渐变文字标题、重阴影悬浮卡片、无意义的玻璃拟态（Glassmorphism）。
- **禁止**在正文或导航栏大量使用装饰性 Emoji；图标统一使用 `lucide-react`（线条粗细 `strokeWidth={1.75}`）。
- **色彩克制**：页面 90% 面积保持中性白/灰/深灰，彩色（`--cat-*`）只用于表达物理量、状态、曲线、力箭头和控件色点，让读者建立“色即语义”的条件反射。
- **数字不抖动**：所有随动画或滑块变化的读数、时间轴、比值必须加 `.tabular`（`font-variant-numeric: tabular-nums` + 等宽字体）。

---

## 2. 物理语义色板分配约定

为任意新学科配置实验室颜色时，建议保持全站一致的语义映射：

| Token | Light 值 | Dark 值 | 推荐物理 / 工程语义 |
| :--- | :--- | :--- | :--- |
| `--cat-blue` | `#1f6cc0` | `#7db9ff` | 进气 / 冷流体 / 电压 / 主控信号 / 输入端 |
| `--cat-orange` | `#ad4600` | `#ffa25c` | 高温 / 燃烧做功 / 热量 / 摩擦损耗 / 排气 |
| `--cat-teal` | `#00796a` | `#3cd3ba` | 冷却 / 密实介质 / 能量回收 / 阻尼吸收 |
| `--cat-green` | `#1d7a4f` | `#5ed59a` | 正常稳态 / 理想路径 / 学习完成 / 安全区间 |
| `--cat-red` | `#c8302f` | `#ff9590` | 过载 / 失速 / 抱死 / 冲击峰值 / 危险边界 |
| `--cat-purple` | `#7a45ae` | `#c9a2f5` | 磁场 / 控制律 / 差分信号 / 虚拟参考轨迹 |
| `--cat-amber` | `#915a00` | `#ffcb47` | 警告 / 半联动 / 临界状态 / 扭矩载荷 |
| `--cat-gray` | `#5f6168` | `#b4b6bc` | 结构件 / 壳体 / 参考基准线 / 未激活路径 |

---

## 3. 完整 `src/index.css` 模板（Tailwind CSS v4 验证版）

> **关键避坑**：在 Tailwind v4 中，仅仅在 `:root` 中定义 CSS 变量不会自动生成 `bg-border`、`bg-sunken/95`、`hover:bg-muted`、`border-input` 等带透明度或状态变体的工具类，**必须通过 `@theme inline` 显式映射**。

```css
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));

@theme inline {
  --color-background: var(--background);
  --color-card: var(--card);
  --color-sunken: var(--sunken);
  --color-muted: var(--muted);
  --color-popover: var(--popover);
  --color-foreground: var(--foreground);
  --color-muted-foreground: var(--muted-foreground);
  --color-faint-foreground: var(--faint-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --font-sans: var(--font-sans);
  --font-mono: var(--font-mono);
}

:root {
  --background: #ffffff;
  --card: #ffffff;
  --sunken: #f7f7f7;
  --muted: #f0f0f0;
  --popover: #ffffff;
  --foreground: #1a1a1a;
  --muted-foreground: #5c5c5c;
  --faint-foreground: #6b6b6b;
  --border: #e8e8e8;
  --input: #d4d4d4;
  --primary: #1a1a1a;
  --primary-foreground: #ffffff;

  --success: #1a7f37;
  --warning: #8a5c00;
  --destructive: #cf222e;
  --info: #0969da;

  --cat-blue: #1f6cc0;
  --cat-orange: #ad4600;
  --cat-teal: #00796a;
  --cat-green: #1d7a4f;
  --cat-purple: #7a45ae;
  --cat-red: #c8302f;
  --cat-amber: #915a00;
  --cat-pink: #b42f7e;
  --cat-gray: #5f6168;

  --font-sans: "Inter", -apple-system, BlinkMacSystemFont, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "Noto Sans CJK SC", system-ui, sans-serif;
  --font-mono: ui-monospace, "SF Mono", SFMono-Regular, Menlo, Consolas, monospace;
}

.dark {
  --background: #191919;
  --card: #1e1e1e;
  --sunken: #202020;
  --muted: #2a2a2a;
  --popover: #252525;
  --foreground: #ececec;
  --muted-foreground: #a6a6a6;
  --faint-foreground: #8f8f8f;
  --border: #2e2e2e;
  --input: #3d3d3d;
  --primary: #ececec;
  --primary-foreground: #191919;

  --success: #4cc26b;
  --warning: #e0a83a;
  --destructive: #ff6b61;
  --info: #5aa2ff;

  --cat-blue: #7db9ff;
  --cat-orange: #ffa25c;
  --cat-teal: #3cd3ba;
  --cat-green: #5ed59a;
  --cat-purple: #c9a2f5;
  --cat-red: #ff9590;
  --cat-amber: #ffcb47;
  --cat-pink: #ff8fcb;
  --cat-gray: #b4b6bc;
}

@layer base {
  html,
  body {
    background-color: var(--background);
    color: var(--foreground);
    font-family: var(--font-sans);
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  * {
    border-color: var(--border);
  }
}

.tabular {
  font-family: var(--font-mono);
  font-variant-numeric: tabular-nums;
}

/* Tactile range slider */
input[type="range"].lab-slider {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 20px;
  background: transparent;
  cursor: pointer;
}

input[type="range"].lab-slider:focus {
  outline: none;
}

input[type="range"].lab-slider::-webkit-slider-runnable-track {
  height: 4px;
  border-radius: 999px;
  background: linear-gradient(
    to right,
    var(--foreground) 0%,
    var(--foreground) var(--slider-pct, 50%),
    var(--border) var(--slider-pct, 50%),
    var(--border) 100%
  );
}

input[type="range"].lab-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 16px;
  height: 16px;
  border-radius: 9999px;
  background: var(--background);
  border: 2.5px solid var(--foreground);
  margin-top: -6px;
  transition: transform 0.12s ease;
}

input[type="range"].lab-slider:hover::-webkit-slider-thumb,
input[type="range"].lab-slider:active::-webkit-slider-thumb {
  transform: scale(1.12);
}

input[type="range"].lab-slider::-moz-range-track {
  height: 4px;
  border-radius: 999px;
  background: var(--border);
}

input[type="range"].lab-slider::-moz-range-progress {
  height: 4px;
  border-radius: 999px;
  background: var(--foreground);
}

input[type="range"].lab-slider::-moz-range-thumb {
  width: 16px;
  height: 16px;
  border-radius: 9999px;
  background: var(--background);
  border: 2.5px solid var(--foreground);
}

::-webkit-scrollbar {
  width: 7px;
  height: 7px;
}
::-webkit-scrollbar-track {
  background: transparent;
}
::-webkit-scrollbar-thumb {
  background: var(--input);
  border-radius: 9999px;
}
::-webkit-scrollbar-thumb:hover {
  background: var(--faint-foreground);
}
```
