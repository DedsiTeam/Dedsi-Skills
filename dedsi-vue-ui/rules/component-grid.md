# DedsiRow / DedsiCol 栅格布局

## Rule

使用 DedsiRow 和 DedsiCol 组件实现 24 栅格响应式布局系统。

## Impact

MEDIUM

## Problem

不正确使用栅格布局会导致响应式效果失效或布局混乱。

## Solution

### DedsiRow Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| gutter | number \| [number, number] | - | 否 | 栅格间隔，可设为 [水平间距, 垂直间距] |
| justify | 'start' \| 'end' \| 'center' \| 'space-around' \| 'space-between' | - | 否 | 水平排列方式 |
| align | 'top' \| 'middle' \| 'bottom' | - | 否 | 垂直对齐方式 |

### DedsiCol Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| span | number | 24 | 否 | 栅格占位格数（0-24） |
| offset | number | 0 | 否 | 栅格左侧间隔格数 |
| xs | number \| object | - | 否 | <576px 响应式栅格 |
| sm | number \| object | - | 否 | ≥576px 响应式栅格 |
| md | number \| object | - | 否 | ≥768px 响应式栅格 |
| lg | number \| object | - | 否 | ≥992px 响应式栅格 |

### 响应式对象格式

```typescript
{
  span: number,    // 栅格占位格数
  offset: number   // 栅格左侧间隔格数
}
```

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-row :gutter="16">
  <dedsi-col :span="12">col-12</dedsi-col>
  <dedsi-col :span="12">col-12</dedsi-col>
</dedsi-row>

<!-- 偏移 -->
<dedsi-row>
  <dedsi-col :span="8" :offset="8">col-8 offset-8</dedsi-col>
</dedsi-row>

<!-- 响应式 -->
<dedsi-row>
  <dedsi-col :xs="24" :sm="12" :md="8">响应式</dedsi-col>
  <dedsi-col :xs="24" :sm="12" :md="8">响应式</dedsi-col>
  <dedsi-col :xs="24" :sm="12" :md="8">响应式</dedsi-col>
</dedsi-row>

<!-- 水平间距和垂直间距 -->
<dedsi-row :gutter="[16, 24]">
  <dedsi-col :span="8">
    <div>内容 1</div>
  </dedsi-col>
  <dedsi-col :span="8">
    <div>内容 2</div>
  </dedsi-col>
  <dedsi-col :span="8">
    <div>内容 3</div>
  </dedsi-col>
</dedsi-row>

<!-- 居中对齐 -->
<dedsi-row justify="center" align="middle">
  <dedsi-col :span="8">居中内容</dedsi-col>
</dedsi-row>
```

### 栅格系统说明

- 栅格系统基于 24 列
- `span` 属性指定栅格占用的列数（1-24）
- `offset` 属性指定栅格左侧偏移的列数
- 响应式断点：
  - `xs`: <576px
  - `sm`: ≥576px
  - `md`: ≥768px
  - `lg`: ≥992px

## Related

- [组件命名规范](./component-naming.md)
- [DedsiCard 卡片](./component-card.md)
