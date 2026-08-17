# 公共 API 索引

本索引用于发现入口，不替代版本化类型声明。公共 API 会随版本演进；生成代码前必须核对目标项目已安装的 `vue-cdk/<module>` 对应 `.d.ts`。

## a11y

- 键盘管理：`ListKeyManager`、`FocusKeyManager`、`ActiveDescendantKeyManager`、`TreeKeyManager`、`Typeahead`。
- 焦点陷阱：`FocusTrap`、`ConfigurableFocusTrap`、`ConfigurableFocusTrapFactory`、`useFocusTrap`、`vFocusTrap`。
- 焦点来源：`FocusMonitor`、`InputModalityDetector`、`useFocusMonitor`、`vFocusMonitor`。
- 常用约定：管理器和监视订阅都需要销毁；焦点陷阱区域可用 `vcdk-focus-initial` 标记初始焦点。

## accordion

- 组件：`CdkAccordion`（`multi`、`id`、`as`；实例方法 `openAll()` / `closeAll()`，`openAll` 仅在多选模式下生效）、`CdkAccordionItem`（`expanded` 支持 `v-model:expanded`，另有 `disabled`、`id`、`as`；默认插槽提供 `expanded` / `disabled` / `open()` / `close()` / `toggle()`）。
- 组合式：`useAccordion({multi, id})`、`useAccordionItem({expanded, disabled, id, emit})`。
- 事件：`update:expanded`、`expandedChange`、`opened`、`closed`、`destroyed`。
- 约定：单选模式下打开一项会收起其他项；`disabled` 阻止命令式与批量操作，但直接修改 `expanded` Ref 仍会同步状态。组件默认渲染 `div`，可用 `as` 更换宿主；标题、内容区、动画与无障碍标记由应用实现。

## bidi

- 上下文：`Directionality`（`value`、只读 `valueSignal`、`change` 事件流、`setDirection()`、`destroy()`）。
- 组件：`VDir`（`dir`、`as`、`dirChange` 事件；默认插槽提供 `{direction, directionality}`），`Dir` 是 Angular 同名别名。
- 组合式：`useDirectionality()`（在 setup 中注入最近上下文）、`provideDirectionality(refOrGetter)`（作用域销毁时自动清理）。
- 注入与工具：`DIR_DOCUMENT`（SSR 或 iframe 场景可提供 `null`）、`CDK_DIRECTIONALITY`、`getDirection(element)`、`resolveDirectionality()`。
- 类型：`Direction`、`DirectionInput`。
- 约定：`auto` 只按浏览器语言解析，不扫描文本内容；无 DOM 或非法值回退 `ltr`。局部方向必须使用 `VDir` 或 `provideDirectionality()`，Vue 指令无法为后代建立 provide 上下文。

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
- `DialogRef`：`close()`、`closed`、`closedPromise`、`backdropClick`、`keydownEvents`、`outsidePointerEvents`、位置、尺寸和面板类更新。`open()` 始终同步返回 `DialogRef`；`closedPromise` 在首次成功关闭后解析，结果与 `closed` 一致，`closePredicate` 拒绝关闭时不会结算。
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

## layout

- 断点常量：`Breakpoints`（`XSmall` / `Small` / `Medium` / `Large` / `XLarge` 及 `Handset` / `Tablet` / `Web` 的 Portrait / Landscape 变体）。
- 服务：`BreakpointObserver`（`isMatched()`、`observe()`、`destroy()`）、`MediaMatcher`、单例 `breakpointObserver`。
- 组合式：`useBreakpoints(value)` 返回 `{matches, breakpoints, state, stop}`，组件作用域内自动退订；另有 `useBreakpointObserver()`、`provideBreakpointObserver()`。
- 注入键：`CDK_BREAKPOINT_OBSERVER`。
- 类型：`BreakpointState`、`BreakpointStream`、`BreakpointSubscription`。
- 约定：SSR 下 `matchMedia` 回退为只读实现（空查询与 `all` 视为匹配，其余不匹配）；`observe()` 返回的订阅必须退订，或改用组合式入口自动清理。

## observers

- 服务：`ContentObserver`（`observe(element)`、`destroy()`）、单例 `contentObserver`、`MutationObserverFactory`。
- 组合式：`useObserveContent(target, callback, {disabled, debounce, observer})` 返回 `{stop}`；`target`、`disabled`、`debounce` 变化时自动重建订阅。
- 指令：`vCdkObserveContent`（回调简写或 `{callback, disabled, debounce, observer}` 对象），别名 `cdkObserveContent`。
- 注入：`useContentObserver()`、`provideContentObserver()`、`CDK_CONTENT_OBSERVER`。
- 约定：同一元素的多个订阅共享一个原生 MutationObserver；忽略纯注释节点变更（框架锚点）；SSR 可安全构造与订阅，但不产生变更事件。

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
- 滚动与输入：`getRtlScrollAxisType()`、`getSupportedInputTypes()`、`isTestEnvironment()`。

## portal

- 内容源：`ComponentPortal`、`TemplatePortal`、`DomPortal`。
- 声明式：`VPortal`、`VPortalOutlet`。
- 命令式出口：`DomPortalOutlet`、`PortalOutlet`、`BasePortalOutlet`。
- 原生 DOM Portal 会移动现有节点，必须明确其恢复和销毁时机。

## scrolling

- 滚动分发：`ScrollDispatcher`、`scrollDispatcher`。
- 视口测量：`ViewportRuler`、`viewportRuler`。
- 滚动容器：`Scrollable`、`useScrollable()`、`vScrollable`。
- 固定尺寸虚拟滚动：`VVirtualScrollViewport`、`VVirtualFor`、`FixedSizeVirtualScrollStrategy`；`itemSize` 必须与条目实际高度一致。
- 不定高度虚拟滚动：`AutoSizeVirtualScrollStrategy`、`ItemSizeAverager`、`provideAutoSizeVirtualScrollStrategy()`；视口使用 `autosize` + `estimatedItemSize`（默认 50），不要与 `itemSize` 同时传入，当前仅支持纵向。
- 视口 props：`orientation`（`vertical` / `horizontal`）、`itemSize`、`autosize`、`estimatedItemSize`、`appendOnly`、`scrollWindow`、`minBufferPx`（默认 100）、`maxBufferPx`（默认 200）；事件 `scrolledIndexChange` 对应 Angular 的 `scrolledIndexChange`。
- 数据与 key：`VVirtualFor` 的 `of` 支持数组、`Ref` 或 `DataSource`；`trackBy` 接收 `(index, item)` 并返回稳定唯一键，顶部或底部追加数据时必须有 `trackBy`（不定高度策略按 key 缓存条目尺寸并恢复滚动锚点，运行时高度变化由 ResizeObserver 响应）。
- 策略注入：`VIRTUAL_SCROLL_STRATEGY`、`VirtualScrollStrategy` 接口；`CDK_VIRTUAL_SCROLL_VIEWPORT` 是 `VVirtualFor` 获取所属视口的注入键。
- 外部滚动容器：`vVirtualScrollableElement`、`VirtualScrollableElement`、`VirtualScrollableWindow`。

## stepper

- 组件：`CdkStepper`（`linear`、`orientation`、`selectedIndex`、`showError`、`displayDefaultIndicatorType`、`id`、`as`；事件 `selectionChange`、`selectedIndexChange`、`update:selectedIndex`）、`CdkStep`（`label`、`optional`、`editable`、`completed`、`hasError`、`state`、`stepControl`、`showError`、`displayDefaultIndicatorType`；事件 `interacted`）、`CdkStepHeader`（`step`）、`CdkStepperNext`（默认 `type="submit"`）、`CdkStepperPrevious`（默认 `type="button"`）。
- 组合式：`useStepper()`、`useStep()`、`useStepHeader()`、`useStepperContext()`。
- 表单契约：`StepControl`（`valid`，可选 `invalid` / `pending`，必须 `reset()`）；字段可传普通值、`Ref`、computed 或 getter，可适配 VeeValidate、原生表单等方案。
- 类型与常量：`STEP_STATE`（`number` / `edit` / `done` / `error`）、`StepState`、`StepperOrientation`、`StepperSelectionEvent`、`StepContentPositionState`；公开实例类型 `CdkStepperPublicApi` / `CdkStepPublicApi`。
- 约定：步骤头部的 roving tabindex 与方向键导航由库维护；线性模式下，前置步骤未完成且非 `optional` 时会阻止前进。

## text-field

- textarea 自动伸缩：`TextareaAutosize`（`enabled`、`minRows`、`maxRows`、`setPlaceholder()`、`resizeToFitContent()`、`reset()`、`destroy()`）、`useTextareaAutosize(target, {enabled, minRows, maxRows, placeholder})`、`vTextareaAutosize`（布尔值或 `{enabled, minRows, maxRows, placeholder, onReady}` 对象）。
- 自动填充监控：`AutofillMonitor`、`autofillMonitor`、`useAutofillMonitor()`、`provideAutofillMonitor()`、`CDK_AUTOFILL_MONITOR`；`useAutofill(target, {monitor, onAutofill})` 返回 `{isAutofilled, event, stop}`；指令 `vAutofill="onAutofill"`。
- 类型：`AutofillEvent`（`target`、`isAutofilled`）、`AutofillStream`、`AutofillSubscription`、`TextareaAutosizeOptions`。
- 样式：结构 CSS 自动注入，也可显式导入 `vue-cdk/text-field/style.css`；Sass 入口为 `vue-cdk/text-field/index` 与 `vue-cdk/text-field/text-field-prebuilt.scss`。
- 约定：自动填充探针依赖 `:-webkit-autofill` 触发的 animationstart，主要支持 Chromium/WebKit，Firefox 不产生事件；SSR 可安全导入，无 DOM 时 `monitor()` 返回立即完成的空流。命令式 `TextareaAutosize` 会写入并恢复宿主的 rows、class 与行内高度，使用完必须 `destroy()`。

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
