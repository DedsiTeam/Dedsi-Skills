# DedsiTooltip 文字提示

## Rule

使用 DedsiTooltip 组件显示简单的文字提示气泡。

## Impact

LOW

## Problem

不使用 Tooltip 组件会导致用户无法理解某些元素的功能。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| content | string | 是 | - | 提示内容 |
| placement | 'top' \| 'bottom' \| 'left' \| 'right' \| ... | 'top' | 否 | 位置 |
| disabled | boolean | false | 否 | 是否禁用 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-tooltip content="这是提示文字">
  <dedsi-button>悬停查看提示</dedsi-button>
</dedsi-tooltip>

<!-- 不同位置 -->
<dedsi-space>
  <dedsi-tooltip placement="top" content="顶部提示">
    <dedsi-button>顶部</dedsi-button>
  </dedsi-tooltip>
  <dedsi-tooltip placement="bottom" content="底部提示">
    <dedsi-button>底部</dedsi-button>
  </dedsi-tooltip>
  <dedsi-tooltip placement="left" content="左侧提示">
    <dedsi-button>左侧</dedsi-button>
  </dedsi-tooltip>
  <dedsi-tooltip placement="right" content="右侧提示">
    <dedsi-button>右侧</dedsi-button>
  </dedsi-tooltip>
</dedsi-space>

<!-- 禁用 -->
<dedsi-tooltip content="禁用的提示" :disabled="true">
  <dedsi-button>禁用</dedsi-button>
</dedsi-tooltip>

<!-- 图标提示 -->
<dedsi-tooltip content="点击复制">
  <CopyOutlined style="cursor: pointer" />
</dedsi-tooltip>
```

### 位置选项

`placement` 支持以下值：
- `top`, `topLeft`, `topRight`
- `bottom`, `bottomLeft`, `bottomRight`
- `left`, `leftTop`, `leftBottom`
- `right`, `rightTop`, `rightBottom`

### 类型定义

```typescript
type Placement =
  | 'top' | 'topLeft' | 'topRight'
  | 'bottom' | 'bottomLeft' | 'bottomRight'
  | 'left' | 'leftTop' | 'leftBottom'
  | 'right' | 'rightTop' | 'rightBottom'

interface Props {
  content: string
  placement?: Placement
  disabled?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiPopper 基础气泡](./component-popper.md)
- [DedsiPopover 气泡卡片](./component-popover.md)
