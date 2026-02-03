# DedsiCountdown 倒计时

## Rule

使用 DedsiCountdown 组件展示实时倒计时。

## Impact

MEDIUM

## Problem

不使用 Countdown 组件会导致倒计时功能实现复杂。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| title | string | - | 否 | 标题 |
| value | number \| string \| Date | 是 | - | 目标时间（时间戳或日期对象） |
| format | string | 'HH:mm:ss' | 否 | 时间格式化模板 |
| prefix | string | - | 否 | 前缀文本 |
| suffix | string | - | 否 | 后缀文本 |
| valueStyle | Record<string, string> | - | 否 | 数值的自定义样式 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| finish | () | 倒计时结束时触发 |
| change | (value: number) | 时间变化时触发，返回剩余毫秒数 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| title | 自定义标题 |
| prefix | 自定义前缀 |
| suffix | 自定义后缀 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-countdown :value="Date.now() + 10000000" />

<!-- 自定义格式 -->
<dedsi-countdown
  title="活动倒计时"
  :value="targetTime"
  format="DD天 HH:mm:ss"
  suffix="后结束"
  @finish="handleFinish"
/>

<!-- 自定义样式 -->
<dedsi-countdown
  :value="Date.now() + 60000"
  title="倒计时"
  :valueStyle="{ color: '#f5222d', fontSize: '24px' }"
/>

<!-- 监听变化 -->
<dedsi-countdown
  :value="targetTime"
  @change="handleChange"
/>

<script setup lang="ts">
import { ref } from 'vue'

const targetTime = ref(Date.now() + 10000000)

const handleFinish = () => {
  console.log('倒计时结束')
}

const handleChange = (value: number) => {
  console.log('剩余毫秒数', value)
}
</script>
```

### 格式说明

| 格式 | 说明 | 示例 |
|------|------|------|
| HH:mm:ss | 时:分:秒 | 23:59:59 |
| DD天 HH:mm:ss | 天 时:分:秒 | 1天 23:59:59 |
| mm:ss | 分:秒 | 59:59 |

### 类型定义

```typescript
interface Props {
  title?: string
  value: number | string | Date
  format?: string
  prefix?: string
  suffix?: string
  valueStyle?: Record<string, string>
}
```

## Related

- [组件命名规范](./component-naming.md)
