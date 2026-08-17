# 常用代码示例

示例只提供可靠起点。使用前核对目标版本类型声明，并按项目的组件规范、主题、错误处理和测试策略改写。

## 声明式下拉浮层

```vue
<script setup lang="ts">
import {ref} from 'vue';
import {
  STANDARD_DROPDOWN_BELOW_POSITIONS,
  VConnectedOverlay,
  VOverlayOrigin,
} from 'vue-cdk/overlay';

const open = ref(false);
</script>

<template>
  <VOverlayOrigin>
    <button type="button" :aria-expanded="open" @click="open = !open">打开菜单</button>
    <VConnectedOverlay
      :open="open"
      :positions="STANDARD_DROPDOWN_BELOW_POSITIONS"
      @overlay-outside-click="open = false"
      @update:open="open = $event"
    >
      <div role="menu" class="menu">
        <button type="button" role="menuitem">菜单项</button>
      </div>
    </VConnectedOverlay>
  </VOverlayOrigin>
</template>
```

菜单的方向键管理、焦点进入与返回需要按产品交互补齐；复杂菜单组合 `vue-cdk/a11y`。

## 带类型结果的对话框

```vue
<!-- ConfirmDialog.vue -->
<script setup lang="ts">
import {useDialogData, useDialogRef} from 'vue-cdk/dialog';

const data = useDialogData<{title: string}>();
const dialogRef = useDialogRef<boolean>();
</script>

<template>
  <section aria-labelledby="confirm-title" class="confirm-dialog">
    <h2 id="confirm-title">{{ data.title }}</h2>
    <button type="button" @click="dialogRef.close(false)">取消</button>
    <button type="button" @click="dialogRef.close(true)">确认</button>
  </section>
</template>
```

```ts
import {useDialog} from 'vue-cdk/dialog';
import ConfirmDialog from './ConfirmDialog.vue';

const dialog = useDialog();

async function openConfirm(): Promise<void> {
  const ref = dialog.open<boolean, {title: string}>(ConfirmDialog, {
    data: {title: '确认删除？'},
    panelClass: 'confirm-dialog-panel',
  });

  const confirmed = await ref.closedPromise;
  if (confirmed) {
    // 只在用户明确确认后执行不可逆操作。
  }
}
```

`open()` 仍同步返回 `DialogRef`，可以先读取 `id` 或调用实例方法，再通过 `closedPromise` 等待一次性关闭结果。需要事件流组合或多个订阅者时使用 `closed.subscribe()`；两者携带相同结果。`closePredicate` 拒绝关闭时 Promise 不会提前解析，只有首次成功关闭才会结算。

## 列表拖拽排序

```vue
<script setup lang="ts">
import {ref} from 'vue';
import {moveItemInArray, VDrag, VDropList} from 'vue-cdk/drag-drop';
import type {VDragDrop} from 'vue-cdk/drag-drop';

const items = ref(['甲', '乙', '丙']);

function onDrop(event: VDragDrop<string>): void {
  moveItemInArray(items.value, event.previousIndex, event.currentIndex);
}
</script>

<template>
  <VDropList :data="items" @dropped="onDrop">
    <VDrag v-for="item in items" :key="item" :data="item">{{ item }}</VDrag>
  </VDropList>
</template>
```

同时提供上移、下移等非指针操作，避免排序功能只能通过拖拽完成。

## 固定高度虚拟列表

```vue
<script setup lang="ts">
import {ref} from 'vue';
import {VVirtualFor, VVirtualScrollViewport} from 'vue-cdk/scrolling';

const items = ref(Array.from({length: 10_000}, (_, index) => ({
  id: index,
  label: `条目 ${index + 1}`,
})));
</script>

<template>
  <VVirtualScrollViewport :item-size="40" style="height: 320px">
    <VVirtualFor :of="items" :track-by="(_index, item) => item.id" v-slot="{item}">
      <div style="height: 40px">{{ item.label }}</div>
    </VVirtualFor>
  </VVirtualScrollViewport>
</template>
```

条目高度固定时才能使用 `itemSize`；高度不固定时改用下面的 `autosize` 示例，不要套用固定尺寸方案。

## 不定高度虚拟列表

```vue
<script setup lang="ts">
import {ref} from 'vue';
import {VVirtualFor, VVirtualScrollViewport} from 'vue-cdk/scrolling';

const items = ref(Array.from({length: 5_000}, (_, index) => ({
  id: index,
  content: `条目 ${index + 1}`,
})));
</script>

<template>
  <VVirtualScrollViewport autosize :estimated-item-size="56" style="height: 320px">
    <VVirtualFor :of="items" :track-by="(_index, item) => item.id" v-slot="{item}">
      <article>{{ item.content }}</article>
    </VVirtualFor>
  </VVirtualScrollViewport>
</template>
```

`autosize` 当前仅支持纵向滚动。顶部或底部追加数据时，`trackBy` 必须为每个条目返回稳定且唯一的键，策略会按 key 缓存实测尺寸并恢复滚动锚点。

## 手风琴

```vue
<script setup lang="ts">
import {CdkAccordion, CdkAccordionItem} from 'vue-cdk/accordion';
</script>

<template>
  <CdkAccordion>
    <CdkAccordionItem v-slot="item">
      <h3>
        <button type="button" :aria-expanded="item.expanded" @click="item.toggle">
          面板标题
        </button>
      </h3>
      <div v-show="item.expanded" role="region">面板内容</div>
    </CdkAccordionItem>
    <!-- 更多 CdkAccordionItem；需要同时展开多项时给 CdkAccordion 加 multi -->
  </CdkAccordion>
</template>
```

标题、内容区、动画与视觉样式由应用实现；组件只负责单选/多选的展开状态协调。

## 多步骤流程

```vue
<script setup lang="ts">
import {computed, ref} from 'vue';
import {
  CdkStep,
  CdkStepHeader,
  CdkStepper,
  CdkStepperNext,
  CdkStepperPrevious,
  type CdkStepPublicApi,
} from 'vue-cdk/stepper';

const stepper = ref<{steps: readonly CdkStepPublicApi[]} | null>(null);
const steps = computed(() => stepper.value?.steps ?? []);
</script>

<template>
  <CdkStepper ref="stepper" linear>
    <nav role="tablist">
      <CdkStepHeader v-for="step in steps" :key="step.id" :step="step">
        {{ step.label.value }}
      </CdkStepHeader>
    </nav>
    <CdkStep v-slot="step" label="填写资料">
      <section v-show="step.isSelected">
        第一步内容
        <CdkStepperNext type="button">下一步</CdkStepperNext>
      </section>
    </CdkStep>
    <CdkStep v-slot="step" label="确认提交">
      <section v-show="step.isSelected">
        第二步内容
        <CdkStepperPrevious type="button">上一步</CdkStepperPrevious>
        <CdkStepperNext type="button">完成</CdkStepperNext>
      </section>
    </CdkStep>
  </CdkStepper>
</template>
```

线性校验通过 `CdkStep` 的 `step-control` 传入任意表单库的 `StepControl` 适配对象（需要 `valid` 与 `reset()`）；步骤头、内容区和切换动画由应用实现。

## 文本域自动伸缩与自动填充监控

```vue
<script setup lang="ts">
import {ref} from 'vue';
import {useAutofill, useTextareaAutosize} from 'vue-cdk/text-field';

const textarea = ref<HTMLTextAreaElement | null>(null);
const input = ref<HTMLInputElement | null>(null);
const {isAutofilled} = useAutofill(input);

useTextareaAutosize(textarea, {minRows: 2, maxRows: 8});
</script>

<template>
  <textarea ref="textarea" />
  <input ref="input" autocomplete="email" />
  <span v-if="isAutofilled">浏览器已自动填充</span>
</template>
```

自动填充探针依赖 `:-webkit-autofill`，主要支持 Chromium/WebKit，Firefox 不会产生事件；需要指令形态时可改用 `v-textarea-autosize` / `v-autofill`。

## 局部 RTL 方向

```vue
<script setup lang="ts">
import {VDir, useDirectionality} from 'vue-cdk/bidi';

const directionality = useDirectionality();
</script>

<template>
  <p>应用方向：{{ directionality.valueSignal.value }}</p>
  <VDir dir="rtl" v-slot="{direction}">
    局部方向：{{ direction }}
  </VDir>
</template>
```

Vue 指令无法为后代提供方向上下文，局部 RTL 必须使用 `VDir` 或 `provideDirectionality()`，不能依赖自定义指令实现。

## 嵌套树

```vue
<script setup lang="ts">
import {ref} from 'vue';
import {VNestedTreeNode, VTree, vTreeNodeToggle} from 'vue-cdk/tree';

interface Node {
  id: string;
  name: string;
  children: Node[];
}

const roots = ref<Node[]>([
  {id: 'root', name: '根节点', children: [{id: 'child', name: '子节点', children: []}]},
]);
</script>

<template>
  <VTree :data-source="roots" :children-accessor="(node: Node) => node.children">
    <template #node="{node}">
      <VNestedTreeNode :node="node" :is-expandable="node.children.length > 0">
        <button v-if="node.children.length" type="button" v-tree-node-toggle>切换</button>
        <span>{{ node.name }}</span>
      </VNestedTreeNode>
    </template>
  </VTree>
</template>
```

## 剪贴板指令

```ts
import {createApp} from 'vue';
import {vCopyToClipboard} from 'vue-cdk/clipboard';
import App from './App.vue';

const app = createApp(App);
app.directive('copy-to-clipboard', vCopyToClipboard);
app.mount('#app');
```

```vue
<script setup lang="ts">
import {ref} from 'vue';

const text = ref('要复制的文本');
const copied = ref<boolean | null>(null);
</script>

<template>
  <button
    type="button"
    v-copy-to-clipboard="{text, attempts: 3, onCopied: value => copied = value}"
  >
    复制
  </button>
  <span aria-live="polite">{{ copied === null ? '' : copied ? '复制成功' : '复制失败' }}</span>
</template>
```

## SSR 客户端边界

```ts
import {onMounted} from 'vue';
import {isBrowser} from 'vue-cdk/platform';

onMounted(() => {
  if (!isBrowser()) {
    return;
  }

  // 只在客户端挂载后执行依赖真实 DOM 的命令式调用。
});
```

优先使用 Nuxt 等框架原生的客户端组件、客户端插件和生命周期边界；`isBrowser()` 是能力保护，不替代框架级水合设计。
