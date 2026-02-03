# DedsiBadge 徽标数

## Rule

使用 DedsiBadge 组件展示通知数量或状态标记。

## Impact

LOW

## Problem

不使用 Badge 组件会导致通知展示不清晰。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| value | string \| number | '' | 否 | 显示的数值或文字 |
| max | number | 99 | 否 | 最大值，超过显示为 max+ |
| isDot | boolean | false | 否 | 是否显示为小红点 |
| hidden | boolean | false | 否 | 是否隐藏 Badge |
| type | 'primary' \| 'success' \| 'warning' \| 'danger' | 'danger' | 否 | 徽标类型，影响颜色 |

### 使用示例

```vue
<!-- 数字徽标 -->
<dedsi-badge :value="5">
  <dedsi-button>消息</dedsi-button>
</dedsi-badge>

<!-- 最大值 -->
<dedsi-badge :value="100" :max="99">
  <dedsi-button>通知</dedsi-button>
</dedsi-badge>

<!-- 小圆点 -->
<dedsi-badge isDot>
  <dedsi-button>状态</dedsi-button>
</dedsi-badge>

<!-- 自定义文本 -->
<dedsi-badge value="New">
  <dedsi-button>新品</dedsi-button>
</dedsi-badge>

<!-- 不同类型 -->
<dedsi-space>
  <dedsi-badge :value="5" type="primary">主要</dedsi-badge>
  <dedsi-badge :value="5" type="success">成功</dedsi-badge>
  <dedsi-badge :value="5" type="warning">警告</dedsi-badge>
  <dedsi-badge :value="5" type="danger">危险</dedsi-badge>
</dedsi-space>
```

### 类型定义

```typescript
interface Props {
  value?: string | number
  max?: number
  isDot?: boolean
  hidden?: boolean
  type?: 'primary' | 'success' | 'warning' | 'danger'
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiAvatar 头像](./component-avatar.md)
