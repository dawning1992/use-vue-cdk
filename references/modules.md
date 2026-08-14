# 模块选择

仅在需要选择能力时读取本文件。具体导出名称必须以目标项目安装版本的类型声明为准。

| 需求 | 子路径 | 常用入口 | 关键注意事项 |
| --- | --- | --- | --- |
| 键盘导航、焦点陷阱、焦点来源 | `vue-cdk/a11y` | `ListKeyManager`、`useFocusTrap`、`useFocusMonitor` | 模态交互需要恢复焦点；提供结构 CSS |
| 复制文本 | `vue-cdk/clipboard` | `useClipboard`、`vCopyToClipboard` | 指令需要注册；SSR 无 DOM 时会降级或报出明确错误 |
| 值与类型规范化 | `vue-cdk/coercion` | `coerceArray` 等工具 | 适合组件输入边界 |
| 数据源与选择状态 | `vue-cdk/collections` | `DataSource`、`SelectionModel` | 可与树、虚拟滚动组合 |
| 模态对话框 | `vue-cdk/dialog` | `useDialog` | 动态内容、遮罩、焦点和关闭结果；提供结构 CSS |
| 排序、跨容器移动、自由拖动 | `vue-cdk/drag-drop` | `VDropList`、`VDrag` | 业务数据由使用方更新；提供结构 CSS |
| 类型化事件流 | `vue-cdk/emitter` | `Emitter` | 用于轻量事件协作 |
| 下拉、菜单、气泡、Tooltip | `vue-cdk/overlay` | `VConnectedOverlay`、`useOverlay` | 声明式优先；提供结构 CSS |
| 浏览器能力与 SSR 检测 | `vue-cdk/platform` | `usePlatform`、`isBrowser` | 避免直接假设 DOM 存在 |
| 将组件、模板或 DOM 挂到出口 | `vue-cdk/portal` | `VPortal`、`ComponentPortal` | Overlay 和 Dialog 的底层能力 |
| 滚动监听、视口测量、虚拟列表 | `vue-cdk/scrolling` | `vScrollable`、`VVirtualScrollViewport` | 尺寸和稳定 key 必须正确；提供结构 CSS |
| 扁平树或嵌套树 | `vue-cdk/tree` | `VTree`、TreeControl 系列 | 通过作用域插槽定义展示 |
| 大数据树或分层懒加载树 | `vue-cdk/virtual-tree` | `VVirtualScrollTree` | 先确认普通树是否已经足够 |

## 常见组合

- 可访问菜单或下拉框：`overlay` + `a11y`，必要时加 `platform`。
- 模态业务流程：优先使用 `dialog`，不要用 `overlay` 重建已有对话框能力。
- 大列表选择：`scrolling` + `collections`，再按交互需求加入 `a11y`。
- 普通树：`tree` + `collections`；数据量大或需分层懒加载时使用 `virtual-tree`。
- 自定义动态宿主：使用 `portal`；需要定位、遮罩和滚动策略时使用 `overlay`。
