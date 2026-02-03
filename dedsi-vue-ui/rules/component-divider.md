# DedsiDivider 分割线

## Rule

使用 DedsiDivider 组件区隔内容的分割线，可包含文字。

## Impact

LOW

## Problem

不使用 Divider 组件会导致页面内容分区不清晰。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| direction | 'horizontal' \| 'vertical' | 'horizontal' | 否 | 分割线方向 |
| contentPosition | 'left' \| 'center' \| 'right' | 'center' | 否 | 文本内容位置（仅水平） |
| dashed | boolean | false | 否 | 是否为虚线 |

### 使用示例

```vue
<!-- 水平分割线 -->
<dedsi-divider>文本</dedsi-divider>

<!-- 不同位置 -->
<dedsi-divider content-position="left">左侧文本</dedsi-divider>
<dedsi-divider content-position="center">居中文本</dedsi-divider>
<dedsi-divider content-position="right">右侧文本</dedsi-divider>

<!-- 虚线 -->
<dedsi-divider dashed>虚线分割</dedsi-divider>

<!-- 垂直分割线 -->
<dedsi-space>
  <span>文本1</span>
  <dedsi-divider direction="vertical" />
  <span>文本2</span>
  <dedsi-divider direction="vertical" />
  <span>文本3</span>
</dedsi-space>

<!-- 无文本 -->
<dedsi-divider />
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiSpace 间距](./component-space.md)
