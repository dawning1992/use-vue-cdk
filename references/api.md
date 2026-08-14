# 公共 API 索引

本索引用于发现入口，不替代版本化类型声明。公共 API 会随版本演进；生成代码前必须核对目标项目已安装的 `vue-cdk/<module>` 对应 `.d.ts`。

## a11y

- 键盘管理：`ListKeyManager`、`FocusKeyManager`、`ActiveDescendantKeyManager`、`TreeKeyManager`、`Typeahead`。
- 焦点陷阱：`FocusTrap`、`ConfigurableFocusTrap`、`ConfigurableFocusTrapFactory`、`useFocusTrap`、`vFocusTrap`。
- 焦点来源：`FocusMonitor`、`InputModalityDetector`、`useFocusMonitor`、`vFocusMonitor`。
- 常用约定：管理器和监视订阅都需要销毁；焦点陷阱区域可用 `vcdk-focus-initial` 标记初始焦点。

## clipboard

- `Clipboard`、`clipboard`、`useClipboard()`：同步复制小文本。
- `PendingCopy`：为大文本预加载并重试，结束后必须调用 `destroy()`。
- `vCopyToClipboard`：接收字符串、`Ref<string>` 或 `{text, attempts, onCopied}`。
- `provideCopyToClipboardConfig()`、`CDK_COPY_TO_CLIPBOARD_CONFIG`：提供默认重试配置。

## coercion

- `coerceArray()`、`coerceCssPixelValue()`、`coerceElement()`、`coerceNumberProperty()`。
- 类型：`ElementOrRef`。

## collections

- 数据源：`DataSource`、`ArrayDataSource`、`isDataSource()`。
- 选择状态：`SelectionModel`、`SelectionChange`。
- 视口契约：`CollectionViewer`、`ListRange`。

## dialog

- `useDialog()`、`dialogService`、`Dialog`：`open()`、`closeAll()`、`getDialogById()`、`openDialogs`、`afterOpened`、`afterAllClosed`。
- `DialogRef`：`close()`、`closed`、`backdropClick`、`keydownEvents`、`outsidePointerEvents`、位置、尺寸和面板类更新。
- `DialogConfig`：数据、关闭策略、遮罩、尺寸、焦点、滚动、方向、容器和内容 props。
- 内容组件：`useDialogData()`、`useDialogRef()`；底层注入键为 `DIALOG_DATA`、`DIALOG_REF`。
- 默认容器：`VDialogContainer`；只有自定义容器确有必要时才替换。

## drag-drop

- 声明式：`VDropList`、`VDrag`、`VDropListGroup`、`vDragHandle`。
- 事件类型：`VDragDrop`、`VDragStart`、`VDragMove`、`VDragEnd`、`VDragEnter`、`VDragExit`、`VDragSortEvent`。
- 数据工具：`moveItemInArray()`、`transferArrayItem()`、`copyArrayItem()`。
- 命令式底层：`createDragRef()`、`createDropListRef()`、`DragRef`、`DropListRef`、`DragDropRegistry`。
- 业务数组不会自动更新；必须在 `dropped` 处理器中修改数据。

## emitter

- `Emitter<T>`：`subscribe()` 返回退订函数；使用方应在生命周期结束时退订。

## overlay

- 声明式：`VOverlayOrigin`、`VConnectedOverlay`。
- 组合式：`useOverlay()` 提供 `create()`、`position()` 和 `scrollStrategies`。
- 实例：`OverlayConfig`、`OverlayRef`、`OverlayContainer`、`BackdropRef`。
- 定位：`GlobalPositionStrategy`、`FlexibleConnectedPositionStrategy`、`ConnectedPosition`、`STANDARD_DROPDOWN_BELOW_POSITIONS`、`STANDARD_DROPDOWN_ADJACENT_POSITIONS`。
- 滚动策略：`noop()`、`close()`、`block()`、`reposition()` 及对应策略类。
- `OverlayRef` 的事件流订阅返回退订函数；结束时调用 `detach()` 或 `dispose()`。

## platform

- 环境：`isBrowser()`、`Platform`、`platform`、`usePlatform()`、`createPlatform()`、`providePlatform()`。
- 能力检测：`supportsPopover()`、`supportsShadowDom()`、`supportsPassiveEventListeners()`、`supportsScrollBehavior()`。
- DOM 与事件：`getEventTarget()`、`getEventTargetPierceShadowDom()`、`getFocusedElementPierceShadowDom()`、`getShadowRoot()`、`hasModifierKey()`。
- 滚动与输入：`getRtlScrollAxisType()`、`getSupportedInputTypes()`。

## portal

- 内容源：`ComponentPortal`、`TemplatePortal`、`DomPortal`。
- 声明式：`VPortal`、`VPortalOutlet`。
- 命令式出口：`DomPortalOutlet`、`PortalOutlet`、`BasePortalOutlet`。
- 原生 DOM Portal 会移动现有节点，必须明确其恢复和销毁时机。

## scrolling

- 滚动分发：`ScrollDispatcher`、`scrollDispatcher`。
- 视口测量：`ViewportRuler`、`viewportRuler`。
- 滚动容器：`Scrollable`、`useScrollable()`、`vScrollable`。
- 虚拟滚动：`VVirtualScrollViewport`、`VVirtualFor`、`FixedSizeVirtualScrollStrategy`。
- 外部滚动容器：`vVirtualScrollableElement`、`VirtualScrollableElement`、`VirtualScrollableWindow`。
- 固定尺寸策略要求 `itemSize` 与实际行高一致；程序滚动使用视口实例公开的方法。

## tree

- 组件：`VTree`、`VTreeNode`、`VNestedTreeNode`。
- 指令：`vTreeNodePadding`、`vTreeNodeToggle`。
- 控制器：`TreeControl`、`BaseTreeControl`、`FlatTreeControl`、`NestedTreeControl`。
- 优先使用 `levelAccessor` 或 `childrenAccessor`；节点插槽负责业务展示。

## virtual-tree

- `VVirtualScrollTree`、`VVirtualScrollTreePublicApi`。
- 懒加载：`LoadChildren`、`LoadChildrenResult`、`PageInfo`。
- 配置与插槽：`VirtualScrollTreeAccessors`、`VirtualScrollTreeSlotContext`。
- 仅支持固定行高；`itemSize` 必须与节点实际高度一致。
