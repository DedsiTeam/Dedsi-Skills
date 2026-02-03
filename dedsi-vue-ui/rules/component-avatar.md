# DedsiAvatar 头像

## Rule

使用 DedsiAvatar 组件展示用户或事物的头像，支持图片、图标或字符。

## Impact

LOW

## Problem

不使用 Avatar 组件会导致头像样式不一致。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| src | string | - | 否 | 图片类资源的地址 |
| size | 'large' \| 'default' \| 'small' \| number | 'default' | 否 | 头像大小，可预设或自定义像素 |
| shape | 'circle' \| 'square' | 'circle' | 否 | 头像形状 |
| icon | any | - | 否 | 设置头像的图标组件 |
| alt | string | - | 否 | 图片无法显示时的替代文本 |
| fit | 'fill' \| 'contain' \| 'cover' \| 'none' \| 'scale-down' | 'cover' | 否 | 图片填充模式 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| error | (event: Event) | 图片加载失败时触发 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 自定义头像内容（如文字） |
| icon | 自定义图标 |

### 使用示例

```vue
<!-- 图片头像 -->
<dedsi-avatar src="https://example.com/avatar.png" size="large" />

<!-- 不同尺寸 -->
<dedsi-avatar size="small">小</dedsi-avatar>
<dedsi-avatar size="default">默认</dedsi-avatar>
<dedsi-avatar size="large">大</dedsi-avatar>

<!-- 自定义尺寸 -->
<dedsi-avatar :size="64">64</dedsi-avatar>

<!-- 文字头像 -->
<dedsi-avatar shape="square">User</dedsi-avatar>

<!-- 图标头像 -->
<dedsi-avatar>
  <template #icon><UserOutlined /></template>
</dedsi-avatar>

<!-- 图片填充模式 -->
<dedsi-avatar
  src="https://example.com/image.jpg"
  shape="square"
  fit="contain"
/>
```

### 类型定义

```typescript
interface Props {
  src?: string
  size?: 'large' | 'default' | 'small' | number
  shape?: 'circle' | 'square'
  icon?: any
  alt?: string
  fit?: 'fill' | 'contain' | 'cover' | 'none' | 'scale-down'
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiBadge 徽标数](./component-badge.md)
