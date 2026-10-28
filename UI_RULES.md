# UI_RULES · 企业 MCP 集成管理平台

## 1. 设计原则

信息优先：用清晰层级突出数据与操作；克制装饰：低饱和灰绿、暖灰底、白色面板；一致性：本文是设计决策与数值的唯一事实来源，CSS 是其同步实现；可访问性：颜色与文字、图标共同表达状态，优先保证对比度与键盘焦点。

## 2. 平台与项目识别

仅启用管理后台（企业集成工具型产品）；仓库根目录目前只有 SPEC 与技术设计，无应用源码、package.json、组件库、主题配置、Tailwind 配置或样式入口。Next.js + TypeScript + Tailwind CSS + pnpm 是已确认的技术方向，尚未安装，框架及库版本不作假设；用户模板中的 Vue / React 18 / Element Plus 等选项不视为实际配置。
后台采用默认信息密度，设计稿 1440px、最小宽度 1280px；尺寸及字号均用 px。窄于 1280px 横向滚动，不压缩布局；1920px 及以上数据区可铺满，表单/详情区最大 960px。目标浏览器为 Chrome / Edge / Safari 最近两个大版本，尚未做浏览器矩阵验收。
本期不做小程序、Web 前台、深色模式、字号缩放方案或多主题切换；保留浏览器缩放能力，不改 html 字号。边框 1px、焦点线宽/间隔 2px 不属于间距阶梯；阴影偏移也不按布局间距计。
首次扫描：样式文件 0，颜色/字号/间距/圆角/阴影硬编码频次均为 0，无旧主题可合并，无历史视觉需迁移。不存在 UI_RULES.md，本次首次建立；未改业务逻辑、页面结构及既有文件。

## 3. 色彩、两层 token 与总表

主色由候选 #6B7D6B 同色相降明度到 #5F6F5F：原白字对比度 4.4017:1、原主色在浅底 #EBF0EB 上为 3.8140:1，均不足 4.5:1；新主色分别为 5.3495:1、4.6352:1。实心按钮采用更深的 #4D5A4D。暖木候选 #B8A489 与白色仅 2.4112:1，保留为装饰辅色，不用于正文、白字按钮或信息图标。
基础层是品牌/功能色与尺寸阶梯；语义层是文字、表面、按钮、输入框、布局及业务状态角色。业务只引用角色 token，不直接使用基础间距/圆角阶梯或品牌装饰色。新增角色先更新本文，再同步 tokens.css；单个 CSS 值只在 tokens.css 定义。
分组示例：`:root { --color-primary-deep: #4D5A4D; --color-btn-primary-bg: var(--color-primary-deep); }`；表内每行 token 与值自上而下一一对应，`var()` 保留引用关系；仅列启用的管理后台。

| token | 管理后台 | 用途 |
|---|---|---|
| `--color-primary`<br>`--color-primary-hover`<br>`--color-primary-pressed`<br>`--color-primary-deep` | `#5F6F5F`<br>`#445044`<br>`#3B453B`<br>`#4D5A4D` | 品牌基础色；secondary 仅装饰 |
| `--color-primary-light`<br>`--color-secondary` | `#EBF0EB`<br>`#B8A489` | 品牌基础色；secondary 仅装饰 |
| `--color-success`<br>`--color-success-light`<br>`--color-warning`<br>`--color-warning-light` | `#3C654E`<br>`#EDF4EF`<br>`#85602D`<br>`#FAF2E7` | 功能色及浅底 |
| `--color-danger`<br>`--color-danger-light`<br>`--color-info`<br>`--color-info-light` | `#A44747`<br>`#FAEEEE`<br>`#426983`<br>`#EDF3F7` | 功能色及浅底 |
| `--color-text-primary`<br>`--color-text-secondary`<br>`--color-text-weak`<br>`--color-text-inverse` | `#263128`<br>`#4F5A51`<br>`#5E685F`<br>`#FFFFFF` | 文字语义色 |
| `--color-text-link`<br>`--color-text-disabled` | `var(--color-primary-deep)`<br>`var(--color-text-weak)` | 文字语义色 |
| `--color-bg-page`<br>`--color-bg-card`<br>`--color-bg-hover`<br>`--color-bg-pressed` | `#F5F6F4`<br>`#FFFFFF`<br>`#EFF2ED`<br>`#E3E9E2` | 表面与非文字 UI；divider 仅装饰 |
| `--color-bg-disabled`<br>`--color-divider`<br>`--color-border`<br>`--color-border-strong` | `#E7EAE5`<br>`#DBE1D9`<br>`#748075`<br>`#626C63` | 表面与非文字 UI；divider 仅装饰 |
| `--color-mask`<br>`--color-focus-ring` | `rgb(38 49 40 / 40%)`<br>`var(--color-primary-deep)` | 表面与非文字 UI；divider 仅装饰 |
| `--color-chart-category-alpha`<br>`--color-chart-category-beta`<br>`--color-chart-category-gamma`<br>`--color-chart-category-delta` | `#4D5A4D`<br>`#426983`<br>`#85602D`<br>`#76578A` | 分类图表色；必须配合标签、符号或线型 |
| `--color-chart-category-epsilon`<br>`--color-chart-category-zeta` | `#A44747`<br>`#2D7171` | 分类图表色；必须配合标签、符号或线型 |
| `--color-chart-sequential-xs`<br>`--color-chart-sequential-sm`<br>`--color-chart-sequential-md`<br>`--color-chart-sequential-lg` | `#E3EBE3`<br>`#B7C9B7`<br>`#8DA78D`<br>`#627F62` | 顺序图表色；xs 至 xl 表示低至高 |
| `--color-chart-sequential-xl` | `#3E563E` | 顺序图表色；xs 至 xl 表示低至高 |
| `--font-family-base`<br>`--font-weight-regular`<br>`--font-weight-medium`<br>`--font-weight-semibold` | `system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif`<br>`400`<br>`500`<br>`600` | 字体栈与字重 |
| `--font-size-display`<br>`--font-size-h1`<br>`--font-size-h2`<br>`--font-size-h3` | `30px`<br>`20px`<br>`16px`<br>`14px` | 字号与行高；均为管理后台值 |
| `--font-size-body`<br>`--font-size-sm`<br>`--font-size-xs`<br>`--line-height-body` | `14px`<br>`12px`<br>`12px`<br>`1.5715` | 字号与行高；均为管理后台值 |
| `--line-height-heading` | `1.4` | 字号与行高；均为管理后台值 |
| `--space-xs`<br>`--space-sm`<br>`--space-md`<br>`--space-lg` | `4px`<br>`8px`<br>`12px`<br>`16px` | 基础间距阶梯；业务使用语义别名 |
| `--space-xl`<br>`--space-2xl`<br>`--space-3xl`<br>`--space-4xl` | `20px`<br>`24px`<br>`32px`<br>`48px` | 基础间距阶梯；业务使用语义别名 |
| `--radius-sm`<br>`--radius-md`<br>`--radius-lg`<br>`--radius-full` | `2px`<br>`4px`<br>`8px`<br>`999px` | 基础圆角与控件阶梯 |
| `--control-height-sm`<br>`--control-height-md`<br>`--control-height-lg` | `24px`<br>`32px`<br>`40px` | 基础圆角与控件阶梯 |
| `--icon-size-sm`<br>`--icon-size-md`<br>`--icon-size-lg`<br>`--border-width` | `14px`<br>`16px`<br>`20px`<br>`1px` | 图标阶梯、边框、焦点、装饰禁用透明度 |
| `--focus-ring-width`<br>`--focus-ring-offset`<br>`--opacity-disabled` | `2px`<br>`2px`<br>`0.4` | 图标阶梯、边框、焦点、装饰禁用透明度 |
| `--duration-fast`<br>`--duration-base`<br>`--ease-standard` | `150ms`<br>`250ms`<br>`cubic-bezier(0.2, 0, 0, 1)` | 克制动效；只动画 transform / opacity |
| `--shadow-card`<br>`--shadow-float`<br>`--shadow-modal` | `0 1px 2px rgb(38 49 40 / 4%)`<br>`0 4px 12px rgb(38 49 40 / 8%)`<br>`0 8px 24px rgb(38 49 40 / 12%)` | 阴影；面板默认不用阴影 |
| `--z-index-base`<br>`--z-index-sticky`<br>`--z-index-dropdown`<br>`--z-index-mask` | `1`<br>`100`<br>`200`<br>`900` | 层级 |
| `--z-index-modal`<br>`--z-index-toast` | `1000`<br>`1100` | 层级 |
| `--layout-design-width`<br>`--layout-min-width`<br>`--layout-form-max-width`<br>`--layout-sider-width` | `1440px`<br>`1280px`<br>`960px`<br>`220px` | 后台布局与密度 |
| `--layout-sider-collapsed-width`<br>`--layout-header-height`<br>`--layout-content-padding`<br>`--layout-panel-gap` | `64px`<br>`56px`<br>`var(--space-2xl)`<br>`var(--space-lg)` | 后台布局与密度 |
| `--table-row-height`<br>`--table-row-height-compact`<br>`--form-label-width` | `48px`<br>`40px`<br>`96px` | 后台布局与密度 |
| `--modal-width-sm`<br>`--modal-width-md`<br>`--modal-width-lg`<br>`--modal-width-xl` | `480px`<br>`640px`<br>`800px`<br>`1000px` | 弹窗与抽屉宽度 |
| `--drawer-width-md`<br>`--drawer-width-lg` | `480px`<br>`720px` | 弹窗与抽屉宽度 |
| `--radius-card`<br>`--radius-button`<br>`--radius-input`<br>`--radius-tag` | `var(--radius-lg)`<br>`var(--radius-md)`<br>`var(--radius-md)`<br>`var(--radius-sm)` | 语义尺寸；基础类与业务优先引用 |
| `--radius-modal`<br>`--card-padding`<br>`--card-gap`<br>`--space-page` | `var(--radius-lg)`<br>`var(--space-lg)`<br>`var(--space-md)`<br>`var(--layout-content-padding)` | 语义尺寸；基础类与业务优先引用 |
| `--button-height`<br>`--button-height-sm`<br>`--button-height-lg`<br>`--button-padding-inline` | `var(--control-height-md)`<br>`var(--control-height-sm)`<br>`var(--control-height-lg)`<br>`var(--space-lg)` | 语义尺寸；基础类与业务优先引用 |
| `--button-gap`<br>`--input-height`<br>`--input-padding-inline`<br>`--form-item-gap` | `var(--space-sm)`<br>`var(--control-height-md)`<br>`var(--space-md)`<br>`var(--space-lg)` | 语义尺寸；基础类与业务优先引用 |
| `--form-label-gap`<br>`--table-cell-padding`<br>`--table-cell-padding-compact`<br>`--tag-padding-inline` | `var(--space-sm)`<br>`var(--space-md)`<br>`var(--space-sm)`<br>`var(--space-sm)` | 语义尺寸；基础类与业务优先引用 |
| `--tag-height`<br>`--icon-size-control` | `var(--control-height-sm)`<br>`var(--icon-size-md)` | 语义尺寸；基础类与业务优先引用 |
| `--color-btn-primary-bg`<br>`--color-btn-primary-hover`<br>`--color-btn-primary-pressed`<br>`--color-btn-primary-text` | `var(--color-primary-deep)`<br>`var(--color-primary-hover)`<br>`var(--color-primary-pressed)`<br>`var(--color-text-inverse)` | 按钮、输入框与面板语义色 |
| `--color-btn-secondary-bg`<br>`--color-btn-secondary-text`<br>`--color-btn-danger-bg`<br>`--color-btn-disabled-bg` | `var(--color-bg-card)`<br>`var(--color-text-link)`<br>`var(--color-danger)`<br>`var(--color-bg-disabled)` | 按钮、输入框与面板语义色 |
| `--color-btn-disabled-text`<br>`--color-input-bg`<br>`--color-input-border`<br>`--color-input-border-hover` | `var(--color-text-disabled)`<br>`var(--color-bg-card)`<br>`var(--color-border)`<br>`var(--color-border-strong)` | 按钮、输入框与面板语义色 |
| `--color-input-border-error`<br>`--color-input-placeholder`<br>`--color-card-bg`<br>`--color-card-border` | `var(--color-danger)`<br>`var(--color-text-weak)`<br>`var(--color-bg-card)`<br>`var(--color-divider)` | 按钮、输入框与面板语义色 |
| `--color-bg-table-header`<br>`--color-text-table-header`<br>`--color-bg-table-row-hover`<br>`--color-bg-table-row-selected` | `var(--color-bg-hover)`<br>`var(--color-text-primary)`<br>`var(--color-bg-hover)`<br>`var(--color-primary-light)` | 表格、导航及中性标签语义色 |
| `--color-bg-sider`<br>`--color-text-sider`<br>`--color-bg-sider-selected`<br>`--color-text-sider-selected` | `var(--color-bg-card)`<br>`var(--color-text-secondary)`<br>`var(--color-primary-light)`<br>`var(--color-primary-deep)` | 表格、导航及中性标签语义色 |
| `--color-tag-neutral-bg`<br>`--color-tag-neutral-text` | `var(--color-bg-disabled)`<br>`var(--color-text-secondary)` | 表格、导航及中性标签语义色 |
| `--color-status-progress`<br>`--color-status-progress-bg`<br>`--color-status-success`<br>`--color-status-success-bg` | `var(--color-info)`<br>`var(--color-info-light)`<br>`var(--color-success)`<br>`var(--color-success-light)` | 业务状态；文本 / 背景成对引用 |
| `--color-status-pending`<br>`--color-status-pending-bg`<br>`--color-status-error`<br>`--color-status-error-bg` | `var(--color-warning)`<br>`var(--color-warning-light)`<br>`var(--color-danger)`<br>`var(--color-danger-light)` | 业务状态；文本 / 背景成对引用 |
| `--color-status-inactive`<br>`--color-status-inactive-bg` | `var(--color-tag-neutral-text)`<br>`var(--color-tag-neutral-bg)` | 业务状态；文本 / 背景成对引用 |

业务状态固定映射：进行中 → progress（info）；已完成/成功 → success；待处理/即将到期 → pending（warning）；失败/驳回/异常 → error（danger）；草稿/已关闭/已停用 → inactive（中性）。使用对应 `--color-status-*` / `--color-status-*-bg`，同时显示文字，不得单凭颜色传达含义。
图表预留 6 个分类色（alpha 至 zeta）与 5 档顺序色（xs 至 xl，低至高）。分类色依次配圆/方/三角/菱形/十字/六边形或不同线型及直接标签，不能只靠红绿；顺序浅色不能当文字色，图形边界用高对比描边，提供刻度与数值。色板尚未在真实图表及色觉模拟中验收。

### 3.1 实测对比度

按 [WCAG 文本对比度](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html) 和 [非文字对比度](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html) 计算：sRGB 通道先除以 255；≤0.04045 时除以 12.92，否则取 ((c+0.055)/1.055)^2.4；L=0.2126R+0.7152G+0.0722B；比值=(较亮 L+0.05)/(较暗 L+0.05)。判定使用未舍入值，表格显示四位小数。
正文/次要/链接 ≥4.5:1；弱文字及占位符也提高到 ≥4.5:1，不能承载关键信息；24px 常规或 19px 加粗的大字最低 3:1，本色板仍按 4.5:1 验证；边框/图标/选中指示/焦点 ≥3:1。反白文字只配深底，禁止配页面/卡片浅底；因此表中反白测试用全部实际深底，不将白对白标成通过。divider 仅装饰，遮罩不承载文字。
额外调整：#626C63 在禁用底 #E7EAE5 上仅 4.499469:1，弱/禁用文字改为同色相 #5E685F；#7C887D 在按下底 #E3E9E2 上仅 2.999207:1，边框改为同色相 #748075。禁用文字不叠加透明度，0.4 仅用于无信息的禁用装饰。
以下简称均省略前缀 `--color-`，可直接对应上表；“保持”表示方案首次选定即达标。62 组检查覆盖正文与卡片、品牌/功能色、表格、侧栏、图标/边框/焦点及图表。

| 前景 | 背景 | 实际比值 | 要求 | 是否通过 | 调整记录 |
|---|---|---|---|---|---|
| text-primary | bg-page | 12.4784:1 | ≥4.5:1 | 通过 | 保持 |
| text-primary | bg-card | 13.5276:1 | ≥4.5:1 | 通过 | 保持 |
| text-secondary | bg-page | 6.6482:1 | ≥4.5:1 | 通过 | 保持 |
| text-secondary | bg-card | 7.2072:1 | ≥4.5:1 | 通过 | 保持 |
| text-weak | bg-page | 5.3492:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-weak | bg-card | 5.7990:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-link | bg-page | 6.7140:1 | ≥4.5:1 | 通过 | 保持 |
| text-link | bg-card | 7.2786:1 | ≥4.5:1 | 通过 | 保持 |
| text-disabled | bg-page | 5.3492:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-disabled | bg-card | 5.7990:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-disabled | bg-disabled | 4.7768:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-inverse | primary | 5.3495:1 | ≥4.5:1 | 通过 | #6B7D6B → #5F6F5F |
| text-inverse | primary-deep | 7.2786:1 | ≥4.5:1 | 通过 | 保持 |
| text-inverse | primary-hover | 8.4789:1 | ≥4.5:1 | 通过 | 保持 |
| text-inverse | primary-pressed | 9.9983:1 | ≥4.5:1 | 通过 | 保持 |
| text-inverse | success | 6.6380:1 | ≥4.5:1 | 通过 | 保持 |
| text-inverse | warning | 5.6629:1 | ≥4.5:1 | 通过 | 保持 |
| text-inverse | danger | 5.8811:1 | ≥4.5:1 | 通过 | 保持 |
| text-inverse | info | 5.8659:1 | ≥4.5:1 | 通过 | 保持 |
| success | success-light | 5.9386:1 | ≥4.5:1 | 通过 | 保持 |
| warning | warning-light | 5.1019:1 | ≥4.5:1 | 通过 | 保持 |
| danger | danger-light | 5.1891:1 | ≥4.5:1 | 通过 | 保持 |
| info | info-light | 5.2414:1 | ≥4.5:1 | 通过 | 保持 |
| primary | primary-light | 4.6352:1 | ≥4.5:1 | 通过 | #6B7D6B → #5F6F5F |
| primary-deep | primary-light | 6.3067:1 | ≥4.5:1 | 通过 | 保持 |
| text-table-header | bg-table-header | 11.9777:1 | ≥4.5:1 | 通过 | 保持 |
| text-primary | bg-table-row-hover | 11.9777:1 | ≥4.5:1 | 通过 | 保持 |
| text-primary | bg-table-row-selected | 11.7214:1 | ≥4.5:1 | 通过 | 保持 |
| text-secondary | bg-table-row-hover | 6.3815:1 | ≥4.5:1 | 通过 | 保持 |
| text-secondary | bg-table-row-selected | 6.2449:1 | ≥4.5:1 | 通过 | 保持 |
| text-weak | bg-table-row-hover | 5.1346:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-weak | bg-table-row-selected | 5.0247:1 | ≥4.5:1 | 通过 | #626C63 → #5E685F |
| text-link | bg-table-row-hover | 6.4446:1 | ≥4.5:1 | 通过 | 保持 |
| text-link | bg-table-row-selected | 6.3067:1 | ≥4.5:1 | 通过 | 保持 |
| text-sider | bg-sider | 7.2072:1 | ≥4.5:1 | 通过 | 保持 |
| text-sider-selected | bg-sider-selected | 6.3067:1 | ≥4.5:1 | 通过 | 保持 |
| border | bg-page | 3.8079:1 | ≥3:1 | 通过 | #7C887D → #748075 |
| border | bg-card | 4.1281:1 | ≥3:1 | 通过 | #7C887D → #748075 |
| border | bg-hover | 3.6551:1 | ≥3:1 | 通过 | #7C887D → #748075 |
| border | bg-pressed | 3.3457:1 | ≥3:1 | 通过 | #7C887D → #748075 |
| border | bg-table-row-selected | 3.5769:1 | ≥3:1 | 通过 | #7C887D → #748075 |
| border-strong | bg-page | 5.0386:1 | ≥3:1 | 通过 | 保持 |
| border-strong | bg-card | 5.4623:1 | ≥3:1 | 通过 | 保持 |
| border-strong | bg-hover | 4.8365:1 | ≥3:1 | 通过 | 保持 |
| border-strong | bg-pressed | 4.4271:1 | ≥3:1 | 通过 | 保持 |
| border-strong | bg-table-row-selected | 4.7330:1 | ≥3:1 | 通过 | 保持 |
| focus-ring | bg-page | 6.7140:1 | ≥3:1 | 通过 | 保持 |
| focus-ring | bg-card | 7.2786:1 | ≥3:1 | 通过 | 保持 |
| focus-ring | bg-hover | 6.4446:1 | ≥3:1 | 通过 | 保持 |
| focus-ring | bg-pressed | 5.8991:1 | ≥3:1 | 通过 | 保持 |
| focus-ring | bg-table-row-selected | 6.3067:1 | ≥3:1 | 通过 | 保持 |
| text-secondary | bg-page | 6.6482:1 | ≥3:1 | 通过 | 保持 |
| text-secondary | bg-card | 7.2072:1 | ≥3:1 | 通过 | 保持 |
| text-secondary | bg-hover | 6.3815:1 | ≥3:1 | 通过 | 保持 |
| text-secondary | bg-pressed | 5.8413:1 | ≥3:1 | 通过 | 保持 |
| text-secondary | bg-table-row-selected | 6.2449:1 | ≥3:1 | 通过 | 保持 |
| chart-category-alpha | bg-card | 7.2786:1 | ≥3:1 | 通过 | 保持 |
| chart-category-beta | bg-card | 5.8659:1 | ≥3:1 | 通过 | 保持 |
| chart-category-gamma | bg-card | 5.6629:1 | ≥3:1 | 通过 | 保持 |
| chart-category-delta | bg-card | 5.9990:1 | ≥3:1 | 通过 | 保持 |
| chart-category-epsilon | bg-card | 5.8811:1 | ≥3:1 | 通过 | 保持 |
| chart-category-zeta | bg-card | 5.6572:1 | ≥3:1 | 通过 | 保持 |

## 4. 字体与排版

后台字号见 token 总表：display 30、h1 20、h2 16、h3 14（600）、body 14、sm/xs 12px；正文行高 1.5715，标题 1.4；默认字重 400，强调 500，标题 600。仅用系统字体，不加载字体资源；数字和金额用 `.text-num` 的 tabular-nums，金额右对齐。`.text-secondary` 用于次要信息，`.text-weak` 仅作辅助；链接带下划线。

## 5. 间距、圆角、阴影、边框、层级与动效

各项精确值与引用关系见总表。间距阶梯 4/8/12/16/20/24/32/48px，全部为 4 的倍数；页面 24、卡片 16、卡片内部间隔 12、面板间隔 16px。尺寸保留任务默认值，无历史值需要微调。圆角使用角色别名；卡片只用装饰边框，默认无阴影，浮层/弹窗按对应阴影档。层级依次为 1/100/200/900/1000/1100。
交互动效使用 150/250ms 与统一曲线，仅动画 transform、opacity；颜色状态即时切换。`prefers-reduced-motion: reduce` 关闭非必要动画与过渡，恢复自动滚动；不使用 transition: all。后台无触控最小区域 token 要求，未来触屏适配需单独设计。

## 6. 开发约束（Do / Don't）

Do：业务 CSS 只用语义 token；新增 token 先改本文及实现，未来多平台同名颜色必须同值；状态类统一 `.is-active` / `.is-disabled` / `.is-error`；原生禁用控件使用 disabled，aria-disabled 和 class 本身不能阻止键盘/点击，业务层需按语义实现；保持可见 label、原生 HTML 语义与完整键盘操作。
Don't：禁止业务样式硬编码颜色、字号、间距、圆角；禁止另建主题源、页面自选状态色、覆盖组件库内部类或修改 node_modules；禁止删除焦点、仅用颜色表达错误、全局 !important、动画布局属性、新增依赖/字体/图标。本次不含用户输入、上传、API、SQL、数据字段或密钥处理。

## 7. 管理后台接入与组件规范

交付入口 `src/styles/theme.css` 依次导入 tokens.css、base.css，业务入口未来只导入它一次（示例：src/app/layout.tsx 中 `import "../styles/theme.css";`）。本期没有根组件可接入，未创建应用、入口或 tailwind.config，也未修改 theme.extend。CSS 使用原生 base/components 层，未来接入 Tailwind 时确认层顺序和 Preflight，避免重复 reset；具体官方映射待实际版本确定。本期组件库=无，因此无供应商变量、ConfigProvider 或 overrides.css。

| token → 目标 | 实际消费 / 组件库变量 |
|---|---|
| `--color-btn-primary-bg/hover/pressed/text` → 主按钮 | `.btn-primary` 默认/hover/active；库变量不适用 |
| `--color-btn-disabled-bg/text`、`--button-height*`、`--radius-button` → 控件 | `.btn` 及 sm/lg；禁用状态覆盖 hover/active |
| `--color-input-bg/border/border-hover/border-error/placeholder` → 输入框 | `.input`、`.is-error` / aria-invalid；禁用优先 |
| `--color-bg-table-*`、`--table-row-height*` → 表格 | `.table`、`.table-compact`、`.is-active` / aria-selected；选中行保留焦点色左边线 |
| `--color-status-*` / `--color-status-*-bg` → 状态标签 | `.tag-info/success/warning/danger`；`.tag` 为中性，`.tag-primary` 为非业务品牌分类 |
| `--color-card-bg/border`、`--radius-card`、`--card-padding` → 面板 | `.card`；`.modal-panel` 共用表面色及文字，使用 modal 圆角/阴影/宽度 |
| `--font-*`、`--line-height-*`、`--color-text-*` → 排版 | body、标题与文字工具类；`.text-num`、`.ellipsis` |

布局从上到下：面包屑、页面标题与主操作、筛选区、数据区、分页。侧栏/折叠栏/顶栏使用总表布局 token；本期提供数值，不生成布局 DOM。表单与详情最大宽度 960px；宽表不限制 960px。低于 1280px 保持横向滚动，不承诺移动重排或整站 WCAG AA 认证。
按钮：同一区域最多一个主按钮；弹窗取消在左、确定在右；danger 用于危险操作且必须二次确认并说明后果；提交显示 loading 并防重入。`.btn-primary` 为主操作，`.btn-secondary` 次操作，`.btn-text` 文字操作，`.btn-danger` 危险操作；hover 仅在支持悬停的设备上生效。
表格：文字左对齐，数字/金额右对齐并用 tabular-nums；空值为“-”；长文本省略并以键盘也可触达的 tooltip 展示；操作列固定右侧，最多 3 个文字按钮，其余进“更多”；提供空/加载/错误状态；可点击行有 hover 与键盘入口，不只给 tr 绑定鼠标事件。
筛选：每行 3–4 字段，超过 2 行可展开/收起；查询/重置置最后一列右对齐。表单统一右对齐标签、宽 96px；必填 danger 星号同时提供语义说明；错误提示在字段下方并关联 aria-describedby；字段较多时按标题分组。样式不承担表单校验逻辑。
弹窗只用 sm/md/lg/xl 四档；`.modal-panel` 默认 md，配 `.modal-sm/lg/xl`；只滚动内容区、标题与底部固定，复杂表单用抽屉（md/lg）。DOM、关闭行为、焦点陷阱/返回、Esc 与滚动管理由后续组件实现，CSS 表面类本身不提供对话框可访问性。
反馈：成功用 Message，异步任务用 Notification，不可逆操作用 Modal，整页结果用 Result/Empty，首次加载用骨架屏、局部刷新用 spinner；403/404/500 共用标题层级、弱文案、留白与返回操作。未来选组件库后经官方主题机制映射，不自行覆盖内部类名。
所有交互元素提供 2px focus-visible 描边和 2px 间隔；焦点指示对周围浅色表面 ≥3:1；不要把深色按钮直接放在同色深底。选中状态有颜色之外的左边线和语义属性；浮层内焦点不可被裁切。禁用标签、按钮、表格和弹窗的状态含义不同，不强行让非交互表面“按下”。
基础类示例：`<button class="btn btn-primary" type="button">查询</button>`；`<label for="name">名称</label><input id="name" class="input" />`；`<span class="tag tag-info">进行中</span>`；`<section class="card"><h2>调用日志</h2></section>`。`.table` 用在语义 table 上；`.modal-panel` 是表面类，必须搭配完整的对话框组件。

## 8. 验证与待办

本次仅新增 UI_RULES.md、src/styles/tokens.css、src/styles/base.css、src/styles/theme.css；未使用入口/JS 主题/Tailwind 配置例外。无依赖变化、无数据库/业务/生产操作、无 !important 或内部类覆盖，overrides 条数为 0。计划仅提交这四个文件，信息为 `style: 建立设计 token 与全局基础样式（UI_RULES）`；当前会话对 .git 只读，git add 创建 .git/index.lock 返回 Operation not permitted，尚未暂存或提交，无 commit hash，未 push。
验证结果：临时 Python 标准库测试 12 项通过，覆盖 62 组对比度、163 个 token 的唯一命名/引用闭包/文档同步、色相保持、尺寸阶梯/紧凑行高、导入、状态规则顺序、文件范围及 ≤200 行限制；缺失引用为 0。脚本留在临时目录、不入库（任务仅允许样式/规范文件）；静态检查不是浏览器 CSS 解析或渲染测试。没有 npm/pnpm scripts，不能运行项目 lint/build、Vitest/RTL 或声明覆盖率 ≥80%。
自查：所有 token 为带 -- 前缀的 kebab-case，无纯数值尺寸名或以颜色名命名；仅启用一端，跨端同名同值检查不适用；间距全部为 4 的倍数；无本期排除项。无组件库，主题通过 CSS 语义变量消费，未使用私有覆盖；按钮 hover/active/disabled、输入错误/禁用、表格选中规则已静态检查，真实组件状态效果尚待验收。
无法进行真实页面 1280/1440/1920 桌面及 375 移动截图和改前后比对：仓库尚无应用且本机无项目 Playwright。1280px 不破版、主题在真实组件生效、Safari/Edge/Chrome 验收均待应用接入，不记为通过；临时检查不能替代这些验收。
遗留硬编码清单：现有业务源码为空，待迁移项 0；缺失 token 0（以新增样式引用闭包检查为准）。缺失的是应用入口接入、实际组件库/Tailwind 版本映射与运行验收，不是虚构的 token。小程序 app.json 配色及 Web 断点不适用。

## 9. 变更记录

2026-10-28：首次建立管理后台样式底座；主色同色相加深，暖木仅装饰；增加 163 个 token、无组件库基础类、62 组对比度记录；应用接入及视觉验收待项目实现。
