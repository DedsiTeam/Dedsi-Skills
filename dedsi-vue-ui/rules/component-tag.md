# DedsiTag 标签

## Rule

使用 DedsiTag 组件进行标记和分类，支持多种类型和颜色自定义。

## Impact

MEDIUM

## Problem

不正确使用 Tag 组件会导致标记不清晰或样式不符合设计规范。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| type | 'default' \| 'primary' \| 'success' \| 'warning' \| 'danger' | 'default' | 否 | 标签类型 |
| size | 'large' \| 'default' \| 'small' | 'default' | 否 | 标签尺寸 |
| closable | boolean | false | 否 | 是否可关闭 |
| round | boolean | false | 否 | 是否为圆角 |
| color | string | - | 否 | 自定义文字颜色 |
| backgroundColor | string | - | 否 | 自定义背景色 |
| borderColor | string | - | 否 | 自定义边框颜色 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| close | (event: MouseEvent) | 关闭时触发 |

### 使用示例

```vue
<!-- 基础标签 -->
<dedsi-tag>默认</dedsi-tag>
<dedsi-tag type="primary">主要</dedsi-tag>
<dedsi-tag type="success">成功</dedsi-tag>
<dedsi-tag type="warning">警告</dedsi-tag>
<dedsi-tag type="danger">危险</dedsi-tag>

<!-- 不同尺寸 -->
<dedsi-tag size="large">大标签</dedsi-tag>
<dedsi-tag>默认标签</dedsi-tag>
<dedsi-tag size="small">小标签</dedsi-tag>

<!-- 可关闭 -->
<dedsi-tag closable @close="handleClose">可关闭标签</dedsi-tag>

<!-- 圆角 -->
<dedsi-tag round>圆角标签</dedsi-tag>

<!-- 自定义颜色 -->
<dedsi-tag color="#f50" backgroundColor="#fff1f0">自定义颜色</dedsi-tag>

<!-- 在表格中使用 -->
<dedsi-table :data="data" :columns="columns">
  <template #status="{ value }">
    <dedsi-tag :type="value === 1 ? 'success' : 'danger'">
      {{ value === 1 ? '启用' : '禁用' }}
    </dedsi-tag>
  </template>
</dedsi-table>
```

### 类型定义

```typescript
interface Props {
  type?: 'default' | 'primary' | 'success' | 'warning' | 'danger'
  size?: 'large' | 'default' | 'small'
  closable?: boolean
  round?: boolean
  color?: string
  backgroundColor?: string
  borderColor?: string
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTable 表格](./component-table.md)
