# DedsiDialog 对话框

## Rule

使用 DedsiDialog 组件显示模态对话框，带有确认/取消按钮。

## Impact

HIGH

## Problem

不正确使用 Dialog 组件会导致对话框无法正确显示或交互失效。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| visible | boolean | false | 是 | 是否可见 |
| title | string | - | 否 | 对话框标题 |
| width | number \| string | 520 | 否 | 对话框宽度 |
| centered | boolean | false | 否 | 是否垂直居中 |
| closable | boolean | true | 否 | 是否显示关闭按钮 |
| maskClosable | boolean | true | 否 | 点击蒙层是否关闭对话框 |
| okText | string | '确定' | 否 | 确认按钮文本 |
| cancelText | string | '取消' | 否 | 取消按钮文本 |
| confirmLoading | boolean | false | 否 | 确认按钮的 loading 状态 |
| footerHidden | boolean | false | 否 | 是否隐藏底部按钮区 |
| type | 'info' \| 'success' \| 'warning' \| 'error' | - | 否 | 对话框类型，显示对应图标 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:visible | (visible: boolean) | 显示状态变化 |
| ok | () | 点击确认按钮 |
| cancel | () | 点击取消按钮 |
| close | () | 关闭对话框时 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 对话框内容 |
| title | 自定义标题 |
| footer | 自定义底部 |

### 使用示例

```vue
<template>
  <dedsi-button @click="visible = true">打开对话框</dedsi-button>

  <dedsi-dialog
    v-model:visible="visible"
    title="对话框"
    width="600px"
    :confirmLoading="loading"
    @ok="handleOk"
    @cancel="handleCancel"
  >
    <p>对话框内容</p>
  </dedsi-dialog>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const visible = ref(false)
const loading = ref(false)

const handleOk = () => {
  loading.value = true
  setTimeout(() => {
    loading.value = false
    visible.value = false
  }, 1000)
}

const handleCancel = () => {
  visible.value = false
}
</script>
```

### 类型对话框

```vue
<!-- 信息对话框 -->
<dedsi-dialog v-model:visible="visible" type="info" title="提示">
  <p>这是一条信息</p>
</dedsi-dialog>

<!-- 成功对话框 -->
<dedsi-dialog v-model:visible="visible" type="success" title="成功">
  <p>操作成功</p>
</dedsi-dialog>

<!-- 警告对话框 -->
<dedsi-dialog v-model:visible="visible" type="warning" title="警告">
  <p>请注意</p>
</dedsi-dialog>

<!-- 错误对话框 -->
<dedsi-dialog v-model:visible="visible" type="error" title="错误">
  <p>发生错误</p>
</dedsi-dialog>
```

### 自定义底部

```vue
<dedsi-dialog v-model:visible="visible" :footerHidden="true">
  <p>对话框内容</p>
  <template #footer>
    <dedsi-space>
      <dedsi-button @click="visible = false">取消</dedsi-button>
      <dedsi-button type="primary" @click="handleOk">确定</dedsi-button>
    </dedsi-space>
  </template>
</dedsi-dialog>
```

### 双向绑定

使用 `v-model:visible` 实现双向绑定：

```vue
<dedsi-dialog v-model:visible="visible" />
<!-- 等价于 -->
<dedsi-dialog :visible="visible" @update:visible="val => visible = val" />
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiModal 模态框](./component-modal.md)
- [DedsiDrawer 抽屉](./component-drawer.md)
