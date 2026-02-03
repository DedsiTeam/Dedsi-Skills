# DedsiScrollbar 滚动条

## Rule

使用 DedsiScrollbar 组件自定义滚动条样式。

## Impact

MEDIUM

## Problem

不使用 Scrollbar 组件会导致原生滚动条样式不一致或无法自定义。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| height | string \| number | - | 否 | 容器高度 |
| maxHeight | string \| number | - | 否 | 容器最大高度 |
| native | boolean | false | 否 | 是否使用原生滚动条 |
| wrapStyle | any | - | 否 | 容器的自定义样式 |
| viewStyle | any | - | 否 | 视图的自定义样式 |
| wrapClass | string | - | 否 | 容器的自定义类名 |
| viewClass | string | - | 否 | 视图的自定义类名 |
| always | boolean | false | 否 | 是否一直显示滚动条 |
| minSize | number | 20 | 否 | 滚动条最小尺寸 |

### 暴露方法（通过 ref）

| 方法名 | 参数 | 说明 |
|--------|------|------|
| wrapRef | - | 获取容器 DOM 引用 |
| update | () | 手动更新滚动条 |
| scrollTo | (options: ScrollToOptions) | 滚动到指定位置 |
| setScrollTop | (val: number) | 设置垂直滚动距离 |
| setScrollLeft | (val: number) | 设置水平滚动距离 |

### 使用示例

```vue
<template>
  <dedsi-scrollbar ref="scrollbar" height="400px">
    <div v-for="i in 100" :key="i">内容 {{ i }}</div>
  </dedsi-scrollbar>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const scrollbar = ref()

// 滚动到顶部
const scrollToTop = () => {
  scrollbar.value?.setScrollTop(0)
}

// 滚动到指定位置
const scrollToPosition = () => {
  scrollbar.value?.scrollTo({ top: 500, behavior: 'smooth' })
}
</script>
```

```vue
<!-- 固定高度 -->
<dedsi-scrollbar height="300px">
  <div>很多内容...</div>
</dedsi-scrollbar>

<!-- 最大高度 -->
<dedsi-scrollbar maxHeight="300px">
  <div>内容...</div>
</dedsi-scrollbar>

<!-- 始终显示滚动条 -->
<dedsi-scrollbar :always="true" height="200px">
  <div>内容...</div>
</dedsi-scrollbar>

<!-- 自定义样式 -->
<dedsi-scrollbar
  height="300px"
  :wrapStyle="{ border: '1px solid #ddd' }"
>
  <div>内容...</div>
</dedsi-scrollbar>

<!-- 原生滚动条 -->
<dedsi-scrollbar :native="true" height="200px">
  <div>内容...</div>
</dedsi-scrollbar>
```

## Related

- [组件命名规范](./component-naming.md)
