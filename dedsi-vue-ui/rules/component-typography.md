# DedsiTypography 排版

## Rule

使用 DedsiTypography 组件展示文本，支持复制、编辑、省略等功能。

## Impact

LOW

## Problem

不使用 Typography 组件会导致文本样式和功能实现不统一。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| tag | string | 'span' | 否 | 渲染的 HTML 标签 |
| type | 'secondary' \| 'success' \| 'warning' \| 'danger' | - | 否 | 文字颜色类型 |
| disabled | boolean | false | 否 | 是否禁用样式 |
| mark | boolean | false | 否 | 是否标记样式（黄色背景） |
| code | boolean | false | 否 | 是否行内代码样式 |
| delete | boolean | false | 否 | 是否删除线 |
| underline | boolean | false | 否 | 是否下划线 |
| strong | boolean | false | 否 | 是否加粗 |
| copyable | boolean \| string | false | 否 | 是否可复制，可设置复制提示文本 |
| editable | boolean | false | 否 | 是否可编辑 |
| ellipsis | boolean \| { rows: number } | false | 否 | 是否省略，可设置省略行数 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:modelValue | (value: string) | 内容值变化 |
| change | (value: string) | 编辑完成时触发 |

### 使用示例

```vue
<!-- 基础文本 -->
<dedsi-typography>普通文本</dedsi-typography>

<!-- 次要文本 -->
<dedsi-typography type="secondary">次要文本</dedsi-typography>

<!-- 不同颜色类型 -->
<dedsi-typography type="success">成功文本</dedsi-typography>
<dedsi-typography type="warning">警告文本</dedsi-typography>
<dedsi-typography type="danger">危险文本</dedsi-typography>

<!-- 文本样式 -->
<dedsi-typography strong>加粗文本</dedsi-typography>
<dedsi-typography underline>下划线文本</dedsi-typography>
<dedsi-typography delete>删除线文本</dedsi-typography>
<dedsi-typography code>行内代码</dedsi-typography>
<dedsi-typography mark>标记文本</dedsi-typography>

<!-- 可复制 -->
<dedsi-typography copyable>这段文字可以复制</dedsi-typography>

<!-- 可编辑 -->
<dedsi-typography editable v-model="text">{{ text }}</dedsi-typography>

<!-- 省略 -->
<dedsi-typography :ellipsis="{ rows: 2 }">
  这是一段很长的文字内容，超过两行后会显示省略号...
</dedsi-typography>

<!-- 组合使用 -->
<dedsi-typography strong underline>
  <dedsi-typography code>重要</dedsi-typography>
  提示
</dedsi-typography>

<!-- 禁用状态 -->
<dedsi-typography :disabled="true">禁用的文本</dedsi-typography>

<!-- 自定义标签 -->
<dedsi-typography tag="h1">标题1</dedsi-typography>
<dedsi-typography tag="h2">标题2</dedsi-typography>
<dedsi-typography tag="p">段落</dedsi-typography>
```

### 类型定义

```typescript
interface Props {
  tag?: string
  type?: 'secondary' | 'success' | 'warning' | 'danger'
  disabled?: boolean
  mark?: boolean
  code?: boolean
  delete?: boolean
  underline?: boolean
  strong?: boolean
  copyable?: boolean | string
  editable?: boolean
  ellipsis?: boolean | { rows: number }
}
```

## Related

- [组件命名规范](./component-naming.md)
