# DedsiPopper 基础气泡

## Rule

使用 DedsiPopper 组件作为基础气泡组件，用于构建其他气泡类组件。

## Impact

MEDIUM

## Problem

不正确使用 Popper 组件会导致气泡位置计算错误或样式不符合预期。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| placement | 'top' \| 'bottom' \| 'left' \| 'right' \| ... | 'top' | 否 | 位置 |
| trigger | 'hover' \| 'click' | 'hover' | 否 | 触发方式 |
| disabled | boolean | false | 否 | 是否禁用 |
| offset | number | 12 | 否 | 偏移量（px） |
| transitionName | string | 'dedsi-popper-fade' | 否 | 过渡动画名称 |
| popperClass | string | - | 否 | 自定义类名 |
| showArrow | boolean | true | 否 | 是否显示箭头 |
| background | string | '#fff' | 否 | 背景色 |
| color | string | - | 否 | 文字颜色 |
| borderRadius | string | '8px' | 否 | 圆角 |
| boxShadow | string | - | 否 | 阴影 |
| padding | string | '12px' | 否 | 内边距 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:visible | (visible: boolean) | 显示状态变化 |

### 暴露方法

| 方法名 | 说明 |
|--------|------|
| close | 关闭气泡 |
| updatePosition | 更新位置 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 触发元素 |
| content | 气泡内容 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-popper>
  <template #content>
    <div>气泡内容</div>
  </template>
  <dedsi-button>悬停显示</dedsi-button>
</dedsi-popper>

<!-- 点击触发 -->
<dedsi-popper trigger="click">
  <template #content>
    <div>点击显示气泡</div>
  </template>
  <dedsi-button>点击</dedsi-button>
</dedsi-popper>

<!-- 不同位置 -->
<dedsi-space>
  <dedsi-popper placement="top">
    <template #content>顶部</template>
    <dedsi-button>顶部</dedsi-button>
  </dedsi-popper>
  <dedsi-popper placement="bottom">
    <template #content>底部</template>
    <dedsi-button>底部</dedsi-button>
  </dedsi-popper>
  <dedsi-popper placement="left">
    <template #content>左侧</template>
    <dedsi-button>左侧</dedsi-button>
  </dedsi-popper>
  <dedsi-popper placement="right">
    <template #content>右侧</template>
    <dedsi-button>右侧</dedsi-button>
  </dedsi-popper>
</dedsi-space>

<!-- 自定义样式 -->
<dedsi-popper
  background="#1890ff"
  color="#fff"
  padding="16px"
  borderRadius="4px"
>
  <template #content>
    <div>自定义样式气泡</div>
  </template>
  <dedsi-button>自定义样式</dedsi-button>
</dedsi-popper>

<!-- 禁用 -->
<dedsi-popper :disabled="true">
  <template #content>无法显示</template>
  <dedsi-button>禁用</dedsi-button>
</dedsi-popper>

<!-- 无箭头 -->
<dedsi-popper :showArrow="false">
  <template #content>无箭头气泡</template>
  <dedsi-button>无箭头</dedsi-button>
</dedsi-popper>
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
  placement?: Placement
  trigger?: 'hover' | 'click'
  disabled?: boolean
  offset?: number
  transitionName?: string
  popperClass?: string
  showArrow?: boolean
  background?: string
  color?: string
  borderRadius?: string
  boxShadow?: string
  padding?: string
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTooltip 文字提示](./component-tooltip.md)
- [DedsiPopover 气泡卡片](./component-popover.md)
