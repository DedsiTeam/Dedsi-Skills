# DedsiPopover 气泡卡片

## Rule

使用 DedsiPopover 组件显示气泡卡片，支持复杂内容。

## Impact

MEDIUM

## Problem

不使用 Popover 组件会导致无法在悬停或点击时显示额外的详细信息。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| title | string | - | 否 | 标题 |
| content | string | - | 否 | 内容 |
| placement | 'top' \| 'bottom' \| 'left' \| 'right' \| ... | 'top' | 否 | 位置 |
| trigger | 'hover' \| 'click' | 'hover' | 否 | 触发方式 |
| disabled | boolean | false | 否 | 是否禁用 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 触发元素 |
| content | 自定义内容 |
| title | 自定义标题 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-popover content="这是内容" title="标题">
  <dedsi-button>悬停显示</dedsi-button>
</dedsi-popover>

<!-- 点击触发 -->
<dedsi-popover trigger="click" title="点击提示">
  <dedsi-button>点击显示</dedsi-button>
</dedsi-popover>

<!-- 自定义内容 -->
<dedsi-popover>
  <template #title>
    <span>自定义标题</span>
  </template>
  <template #content>
    <div style="width: 200px">
      <p>这是自定义的气泡内容</p>
      <dedsi-button type="primary">操作</dedsi-button>
    </div>
  </template>
  <dedsi-button>自定义内容</dedsi-button>
</dedsi-popover>

<!-- 不同位置 -->
<dedsi-space>
  <dedsi-popover placement="top" content="顶部">
    <dedsi-button>顶部</dedsi-button>
  </dedsi-popover>
  <dedsi-popover placement="bottom" content="底部">
    <dedsi-button>底部</dedsi-button>
  </dedsi-popover>
  <dedsi-popover placement="left" content="左侧">
    <dedsi-button>左侧</dedsi-button>
  </dedsi-popover>
  <dedsi-popover placement="right" content="右侧">
    <dedsi-button>右侧</dedsi-button>
  </dedsi-popover>
</dedsi-space>
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
  title?: string
  content?: string
  placement?: Placement
  trigger?: 'hover' | 'click'
  disabled?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiPopper 基础气泡](./component-popper.md)
- [DedsiTooltip 文字提示](./component-tooltip.md)
