# 集成模式

仅在实现或排查外部项目集成时读取本文件。

## 确认版本

先从项目自身获得事实：

```sh
# 选择与项目一致的包管理器执行其中一个命令。
npm ls vue-cdk vue
pnpm why vue-cdk
yarn why vue-cdk
```

检查已安装包的 `package.json`、`exports` 和 `.d.ts`。只有尚未安装或需要评估升级时，才查询 registry：

```sh
npm view vue-cdk version peerDependencies dist-tags --json
```

预发布版本可能被标记为 `latest`。不要仅凭 dist-tag 判断稳定性；同时检查 SemVer 中是否包含预发布标识，并把风险告知用户。

## 安装

使用项目现有包管理器：

```sh
npm install vue-cdk
pnpm add vue-cdk
yarn add vue-cdk
bun add vue-cdk
```

不要把四条命令全部执行。若 Vue peer dependency 不满足，先说明兼容问题，不要静默强制安装。

## 导入与样式

业务 API 使用子路径：

```ts
import {VConnectedOverlay, VOverlayOrigin} from 'vue-cdk/overlay';
import type {ConnectedPosition} from 'vue-cdk/overlay';
```

需要构建期显式管理结构样式时，可以导入对应 CSS：

```ts
import 'vue-cdk/overlay/style.css';
```

目前 `a11y`、`dialog`、`drag-drop`、`overlay`、`scrolling`、`text-field` 六个模块提供 `style.css` 导出；`text-field` 另有 Sass 入口 `vue-cdk/text-field/index` 与预构建样式 `vue-cdk/text-field/text-field-prebuilt.scss`。不要为不提供 CSS 导出的模块构造不存在的 `style.css` 路径。无论是否显式导入结构 CSS，都需要在应用侧编写颜色、间距、字体、阴影、动画等主题样式。

## SSR 与生命周期

- 模块导入应保持 SSR 安全，但命令式 DOM 操作必须推迟到客户端挂载之后。
- Nuxt 等框架中使用其客户端边界和生命周期，不要在服务端 setup 路径直接打开 Dialog 或创建 Overlay。
- 组件卸载时关闭命令式实例、取消订阅、移除监听并释放 Portal/Overlay 宿主。
- 浏览器专属能力应提供服务端占位状态，避免服务端与客户端首屏结构不一致。

## 排查顺序

1. 核对导入子路径是否存在于已安装包 `exports`。
2. 核对导入名称是否存在于对应 `.d.ts`。
3. 核对 Vue peer dependency 和单例安装，排除重复 Vue 实例。
4. 核对结构 CSS、应用主题 CSS、层叠上下文和容器挂载位置。
5. 核对 SSR 执行阶段、Teleport 目标和 DOM 可用性。
6. 核对卸载清理、焦点恢复、滚动锁定和事件传播。

不要通过深层导入 `vue-cdk/dist/...` 绕过 `exports`；这会依赖非公共构建结构，并可能在补丁版本中失效。
