# DedsiAlert 警告提示

## Rule

使用 DedsiAlert 组件展现需要关注的信息，支持多种类型和关闭操作。

## Impact

MEDIUM

## Problem

不正确使用 Alert 组件会导致信息展示不清晰或缺少必要的交互。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| title | string | - | 否 | 标题文本 |
| description | string | - | 否 | 辅助性文字描述 |
| type | 'success' \| 'info' \| 'warning' \| 'error' | 'info' | 否 | 警告类型，影响颜色和图标 |
| closable | boolean | false | 否 | 是否显示关闭按钮 |
| closeText | string | - | 否 | 关闭按钮的自定义文本 |
| showIcon | boolean | false | 否 | 是否显示左侧图标 |
| center | boolean | false | 否 | 文字是否居中显示 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| close | (event: MouseEvent) | 点击关闭按钮时触发 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 默认内容，通常放置描述文字 |
| title | 自定义标题内容 |
| icon | 自定义图标 |
| close | 自定义关闭按钮 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-alert type="success" title="成功提示" description="操作已完成" />

<!-- 可关闭 -->
<dedsi-alert
  type="warning"
  title="警告"
  description="请注意查看"
  :closable="true"
  @close="handleClose"
/>

<!-- 自定义内容 -->
<dedsi-alert type="error">
  <template #title>错误</template>
  <p>发生错误，请重试</p>
</dedsi-alert>

<!-- 带图标 -->
<dedsi-alert
  type="info"
  title="提示信息"
  description="这是一条带图标的提示"
  :showIcon="true"
  :center="true"
/>
```

### 类型定义

```typescript
interface Props {
  title?: string
  description?: string
  type?: 'success' | 'info' | 'warning' | 'error'
  closable?: boolean
  closeText?: string
  showIcon?: boolean
  center?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiMessage 全局提示](./component-message.md)
