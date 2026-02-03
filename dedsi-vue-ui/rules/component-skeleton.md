# DedsiSkeleton 骨架屏

## Rule

使用 DedsiSkeleton 组件在数据加载时展示占位效果。

## Impact

MEDIUM

## Problem

不使用 Skeleton 组件会导致加载体验不佳。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| loading | boolean | true | 否 | 是否显示骨架屏 |
| active | boolean | true | 否 | 是否开启动画效果 |
| avatar | boolean | false | 否 | 是否显示头像占位 |
| avatarSize | number \| 'small' \| 'large' \| 'default' | 'default' | 否 | 头像大小 |
| avatarShape | 'circle' \| 'square' | 'circle' | 否 | 头像形状 |
| title | boolean | true | 否 | 是否显示标题占位 |
| titleWidth | number \| string | - | 否 | 标题占位宽度 |
| paragraph | boolean \| object | true | 否 | 段落配置，true 或 { rows: number, width?: array } |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 加载完成后的实际内容 |
| template | 自定义骨架屏模板 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-skeleton :loading="loading">
  <div>实际内容</div>
</dedsi-skeleton>

<!-- 复杂配置 -->
<dedsi-skeleton
  :loading="loading"
  :avatar="true"
  avatarSize="large"
  :paragraph="{ rows: 4 }"
>
  <UserProfile />
</dedsi-skeleton>

<!-- 自定义段落 -->
<dedsi-skeleton
  :loading="loading"
  :paragraph="{ rows: 3, width: ['100%', '80%', '60%'] }"
>
  <div>实际内容</div>
</dedsi-skeleton>

<!-- 不显示标题 -->
<dedsi-skeleton :loading="loading" :title="false">
  <div>实际内容</div>
</dedsi-skeleton>

<!-- 自定义模板 -->
<dedsi-skeleton :loading="loading">
  <template #template>
    <dedsi-space direction="vertical" style="width: 100%">
      <dedsi-skeleton-item style="width: 100%; height: 40px" />
      <dedsi-skeleton-item style="width: 60%; height: 20px" />
      <dedsi-skeleton-item style="width: 80%; height: 20px" />
    </dedsi-space>
  </template>
  <div>实际内容</div>
</dedsi-skeleton>
```

### 类型定义

```typescript
interface ParagraphConfig {
  rows?: number
  width?: number | string | Array<number | string>
}

interface Props {
  loading?: boolean
  active?: boolean
  avatar?: boolean
  avatarSize?: number | 'small' | 'large' | 'default'
  avatarShape?: 'circle' | 'square'
  title?: boolean
  titleWidth?: number | string
  paragraph?: boolean | ParagraphConfig
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiEmpty 空状态](./component-empty.md)
