# DedsiModal 模态框

## Rule

使用 DedsiModal 组件显示基础模态框，比 Dialog 更轻量。

## Impact

MEDIUM

## Problem

不正确使用 Modal 组件会导致模态框无法正确显示或交互失效。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| visible | boolean | false | 是 | 是否可见 |
| title | string | - | 否 | 标题 |
| width | number \| string | 520 | 否 | 宽度 |
| centered | boolean | false | 否 | 是否垂直居中 |
| closable | boolean | true | 否 | 是否显示关闭按钮 |
| mask | boolean | true | 否 | 是否显示遮罩层 |
| maskClosable | boolean | true | 否 | 点击遮罩是否关闭 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:visible | (visible: boolean) | 显示状态变化 |
| cancel | () | 点击取消或关闭 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 内容 |
| title | 自定义标题 |

### 使用示例

```vue
<template>
  <dedsi-button @click="visible = true">打开模态框</dedsi-button>

  <dedsi-modal
    v-model:visible="visible"
    title="模态框标题"
    width="600px"
    :centered="true"
    @cancel="handleCancel"
  >
    <p>模态框内容</p>
  </dedsi-modal>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const visible = ref(false)

const handleCancel = () => {
  visible.value = false
}
</script>
```

### 与 Dialog 的区别

| 特性 | Modal | Dialog |
|------|-------|--------|
| 底部按钮 | 无默认按钮 | 有确定/取消按钮 |
| 类型图标 | 不支持 | 支持 info/success/warning/error |
| loading 状态 | 不支持 | 支持 confirmLoading |
| 适用场景 | 简单内容展示 | 需要确认操作的场景 |

### 双向绑定

```vue
<dedsi-modal v-model:visible="visible" />
<!-- 等价于 -->
<dedsi-modal :visible="visible" @update:visible="val => visible = val" />
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiDialog 对话框](./component-dialog.md)
- [DedsiDrawer 抽屉](./component-drawer.md)
