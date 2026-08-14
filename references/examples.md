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

function openConfirm(): void {
  const ref = dialog.open<boolean, {title: string}>(ConfirmDialog, {
    data: {title: '确认删除？'},
    panelClass: 'confirm-dialog-panel',
  });

  ref.closed.subscribe(confirmed => {
    if (confirmed) {
      // 只在用户明确确认后执行不可逆操作。
    }
  });
}
```

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

若实际行高不是固定的 `40px`，不要继续使用这个示例；先检查当前版本是否提供动态尺寸策略。

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
