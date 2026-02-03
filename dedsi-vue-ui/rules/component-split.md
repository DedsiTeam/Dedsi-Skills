# DedsiSplit 分割布局

## Rule

使用 DedsiSplit 组件实现左右分割的布局容器。

## Impact

LOW

## Problem

不使用 Split 组件会导致分割布局实现复杂。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| align | 'top' \| 'center' \| 'bottom' \| 'baseline' | 'center' | 否 | 左右内容的对齐方式 |
| padding | string \| number | - | 否 | 内边距 |
| gap | string \| number | - | 否 | 左右间距 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 左侧内容（无 right 插槽时使用） |
| left | 左侧内容 |
| right | 右侧内容 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-split :gap="20">
  <template #left>
    <p>左侧内容</p>
  </template>
  <template #right>
    <p>右侧内容</p>
  </template>
</dedsi-split>

<!-- 不同对齐方式 -->
<dedsi-split align="top">
  <template #left>
    <div style="height: 100px">顶部对齐</div>
  </template>
  <template #right>
    <div style="height: 60px">右侧内容</div>
  </template>
</dedsi-split>

<dedsi-split align="center">
  <template #left>
    <div style="height: 100px">居中对齐</div>
  </template>
  <template #right>
    <div style="height: 60px">右侧内容</div>
  </template>
</dedsi-split>

<!-- 带内边距 -->
<dedsi-split :gap="20" padding="20px">
  <template #left>
    <p>左侧内容</p>
  </template>
  <template #right>
    <p>右侧内容</p>
  </template>
</dedsi-split>

<!-- 使用默认插槽 -->
<dedsi-split :gap="20">
  <p>左侧内容</p>
  <p>右侧内容</p>
</dedsi-split>
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiSpace 间距](./component-space.md)
