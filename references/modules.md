# 模块选择

仅在需要选择能力时读取本文件。具体导出名称必须以目标项目安装版本的类型声明为准。

| 需求 | 子路径 | 常用入口 | 关键注意事项 |
| --- | --- | --- | --- |
| 键盘导航、焦点陷阱、焦点来源 | `vue-cdk/a11y` | `ListKeyManager`、`useFocusTrap`、`useFocusMonitor` | 模态交互需要恢复焦点；提供结构 CSS |
| 手风琴、展开面板状态协调 | `vue-cdk/accordion` | `CdkAccordion`、`CdkAccordionItem`、`useAccordion`、`useAccordionItem` | 只协调展开状态，标题、内容与无障碍标记由应用实现；`multi` 开启多选 |
| LTR/RTL 文字方向上下文 | `vue-cdk/bidi` | `VDir`、`Directionality`、`useDirectionality`、`provideDirectionality` | 局部方向用 `VDir` 组件而非指令；`auto` 按浏览器语言解析 |
| 复制文本 | `vue-cdk/clipboard` | `useClipboard`、`vCopyToClipboard` | 指令需要注册；SSR 无 DOM 时会降级或报出明确错误 |
| 值与类型规范化 | `vue-cdk/coercion` | `coerceArray` 等工具 | 适合组件输入边界 |
| 数据源与选择状态 | `vue-cdk/collections` | `DataSource`、`SelectionModel` | 可与树、虚拟滚动组合 |
| 模态对话框 | `vue-cdk/dialog` | `useDialog` | 动态内容、焦点和关闭结果；同一服务的多层对话框共享遮罩并自动管理层级；提供结构 CSS |
| 排序、跨容器移动、自由拖动 | `vue-cdk/drag-drop` | `VDropList`、`VDrag` | 业务数据由使用方更新；提供结构 CSS |
| 类型化事件流 | `vue-cdk/emitter` | `Emitter` | 用于轻量事件协作 |
| 响应式断点与媒体查询 | `vue-cdk/layout` | `Breakpoints`、`BreakpointObserver`、`useBreakpoints`、`MediaMatcher` | 组合式入口随作用域自动退订；SSR 回退为不匹配 |
| 内容变化观察 | `vue-cdk/observers` | `ContentObserver`、`useObserveContent`、`vCdkObserveContent` | 同一元素共享 MutationObserver；只报告有效 DOM 变更 |
| 下拉、菜单、气泡、Tooltip | `vue-cdk/overlay` | `VConnectedOverlay`、`useOverlay` | 声明式优先；提供结构 CSS |
| 浏览器能力与 SSR 检测 | `vue-cdk/platform` | `usePlatform`、`isBrowser` | 避免直接假设 DOM 存在 |
| 将组件、模板或 DOM 挂到出口 | `vue-cdk/portal` | `VPortal`、`ComponentPortal` | Overlay 和 Dialog 的底层能力 |
| 滚动监听、视口测量、虚拟列表 | `vue-cdk/scrolling` | `vScrollable`、`VVirtualScrollViewport` | 固定高度用 `itemSize`；纵向不定高度用 `autosize`；追加数据需稳定唯一 `trackBy`；提供结构 CSS |
| 多步骤流程与线性校验 | `vue-cdk/stepper` | `CdkStepper`、`CdkStep`、`CdkStepHeader`、`useStepper` | 校验通过 `StepControl` 契约适配任意表单库；视觉样式自备 |
| 文本域自动伸缩与自动填充监控 | `vue-cdk/text-field` | `useTextareaAutosize`、`vTextareaAutosize`、`useAutofill`、`vAutofill` | 提供结构 CSS 与 Sass 入口；Firefox 不触发自动填充事件 |
| 扁平树或嵌套树 | `vue-cdk/tree` | `VTree`、TreeControl 系列 | 通过作用域插槽定义展示 |
| 大数据树或分层懒加载树 | `vue-cdk/virtual-tree` | `VVirtualScrollTree` | 先确认普通树是否已经足够 |

## 常见组合

- 可访问菜单或下拉框：`overlay` + `a11y`，必要时加 `platform`。
- 模态业务流程：优先使用 `dialog`，不要用 `overlay` 重建已有对话框能力。
- 大列表选择：`scrolling` + `collections`，再按交互需求加入 `a11y`。
- 普通树：`tree` + `collections`；数据量大或需分层懒加载时使用 `virtual-tree`。
- 自定义动态宿主：使用 `portal`；需要定位、遮罩和滚动策略时使用 `overlay`。
- 多步骤表单：`stepper` + 表单库 `StepControl` 适配；RTL 场景再加 `bidi`。
- 响应式交互：`layout`（断点观察）+ `platform`（能力检测）。
- 文本输入增强：`text-field`（自动伸缩/自动填充）+ `a11y`。
