# DedsiDrawer 抽屉

## Rule

使用 DedsiDrawer 组件显示从屏幕边缘滑出的浮层面板。

## Impact

MEDIUM

## Problem

不正确使用 Drawer 组件会导致抽屉无法正确显示或位置不符合预期。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| visible | boolean | false | 是 | 是否可见 |
| title | string | - | 否 | 标题 |
| placement | 'left' \| 'right' \| 'top' \| 'bottom' | 'right' | 否 | 位置 |
| width | number \| string | 378 | 否 | 宽度（left/right） |
| height | number \| string | 378 | 否 | 高度（top/bottom） |
| closable | boolean | true | 否 | 是否显示关闭按钮 |
| mask | boolean | true | 否 | 是否显示遮罩 |
| maskClosable | boolean | true | 否 | 点击遮罩是否关闭 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:visible | (visible: boolean) | 显示状态变化 |
| close | () | 关闭时触发 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 内容 |
| title | 自定义标题 |
| footer | 底部内容 |

### 使用示例

```vue
<template>
  <dedsi-space>
    <dedsi-button @click="visible = true">打开右侧抽屉</dedsi-button>
    <dedsi-button @click="visibleLeft = true">打开左侧抽屉</dedsi-button>
    <dedsi-button @click="visibleTop = true">打开顶部抽屉</dedsi-button>
    <dedsi-button @click="visibleBottom = true">打开底部抽屉</dedsi-button>
  </dedsi-space>

  <!-- 右侧抽屉 -->
  <dedsi-drawer
    v-model:visible="visible"
    title="抽屉标题"
    placement="right"
    :width="600"
    @close="handleClose"
  >
    <p>抽屉内容</p>
    <template #footer>
      <dedsi-space style="width: 100%; justify-content: flex-end">
        <dedsi-button @click="visible = false">取消</dedsi-button>
        <dedsi-button type="primary" @click="handleOk">确定</dedsi-button>
      </dedsi-space>
    </template>
  </dedsi-drawer>

  <!-- 左侧抽屉 -->
  <dedsi-drawer
    v-model:visible="visibleLeft"
    title="左侧抽屉"
    placement="left"
    :width="400"
  >
    <p>左侧抽屉内容</p>
  </dedsi-drawer>

  <!-- 顶部抽屉 -->
  <dedsi-drawer
    v-model:visible="visibleTop"
    title="顶部抽屉"
    placement="top"
    :height="300"
  >
    <p>顶部抽屉内容</p>
  </dedsi-drawer>

  <!-- 底部抽屉 -->
  <dedsi-drawer
    v-model:visible="visibleBottom"
    title="底部抽屉"
    placement="bottom"
    :height="400"
  >
    <p>底部抽屉内容</p>
  </dedsi-drawer>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const visible = ref(false)
const visibleLeft = ref(false)
const visibleTop = ref(false)
const visibleBottom = ref(false)

const handleClose = () => {
  console.log('抽屉关闭')
}

const handleOk = () => {
  console.log('确定')
  visible.value = false
}
</script>
```

### 双向绑定

```vue
<dedsi-drawer v-model:visible="visible" />
<!-- 等价于 -->
<dedsi-drawer :visible="visible" @update:visible="val => visible = val" />
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiDialog 对话框](./component-dialog.md)
- [DedsiModal 模态框](./component-modal.md)
