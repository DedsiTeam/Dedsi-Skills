# DedsiMarquee 跑马灯

## Rule

使用 DedsiMarquee 组件实现文字/内容滚动展示。

## Impact

LOW

## Problem

不使用 Marquee 组件会导致滚动效果实现复杂。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| speed | number | 50 | 否 | 滚动速度（px/s） |
| direction | 'ltr' \| 'rtl' | 'rtl' | 否 | 滚动方向：ltr 从左到右，rtl 从右到左 |
| pauseOnHover | boolean | true | 否 | 鼠标悬停时是否暂停 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-marquee :speed="100" direction="ltr">
  <span>这是一条滚动的公告信息</span>
</dedsi-marquee>

<!-- 不同方向 -->
<dedsi-space direction="vertical" style="width: 100%">
  <dedsi-marquee direction="ltr">
    从左到右滚动
  </dedsi-marquee>
  <dedsi-marquee direction="rtl">
    从右到左滚动
  </dedsi-marquee>
</dedsi-space>

<!-- 不同速度 -->
<dedsi-space direction="vertical" style="width: 100%">
  <dedsi-marquee :speed="30">
    慢速滚动（30px/s）
  </dedsi-marquee>
  <dedsi-marquee :speed="100">
    快速滚动（100px/s）
  </dedsi-marquee>
</dedsi-space>

<!-- 暂停悬停 -->
<dedsi-marquee :pauseOnHover="true">
  <span>鼠标悬停时暂停滚动</span>
</dedsi-marquee>

<!-- 不暂停 -->
<dedsi-marquee :pauseOnHover="false">
  <span>鼠标悬停时继续滚动</span>
</dedsi-marquee>

<!-- 多内容 -->
<dedsi-marquee :speed="80" direction="ltr">
  <dedsi-space>
    <span>第一条公告</span>
    <dedsi-divider direction="vertical" />
    <span>第二条公告</span>
    <dedsi-divider direction="vertical" />
    <span>第三条公告</span>
  </dedsi-space>
</dedsi-marquee>
```

### 类型定义

```typescript
interface Props {
  speed?: number
  direction?: 'ltr' | 'rtl'
  pauseOnHover?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
