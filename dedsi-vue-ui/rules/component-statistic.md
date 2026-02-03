# DedsiStatistic 统计数值

## Rule

使用 DedsiStatistic 组件展示统计数值，支持数字动画。

## Impact

MEDIUM

## Problem

不使用 Statistic 组件会导致数值展示不够直观。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| title | string | - | 否 | 数值的标题 |
| value | number | 是 | - | 数值内容 |
| precision | number | 0 | 否 | 数值精度（小数位数） |
| prefix | string | - | 否 | 前缀 |
| suffix | string | - | 否 | 后缀 |
| showGroup | boolean | true | 否 | 是否显示千分位分隔符 |
| duration | number | 1500 | 否 | 数字动画持续时间（ms） |
| animation | boolean | true | 否 | 是否开启动画 |
| contentStyle | Record<string, string> | - | 否 | 数值内容的自定义样式 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| title | 自定义标题 |
| prefix | 自定义前缀 |
| suffix | 自定义后缀 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-statistic title="总用户数" :value="12345" />

<!-- 带前后缀 -->
<dedsi-statistic title="账户余额" :value="1234.56" prefix="¥" :precision="2" />
<dedsi-statistic title="完成率" :value="75.5" suffix="%" :precision="1" />

<!-- 自定义样式 -->
<dedsi-statistic
  title="成交量"
  :value="12345"
  :contentStyle="{ color: '#3f8600', fontSize: '24px' }"
/>

<!-- 千分位分隔符 -->
<dedsi-statistic title="销售额" :value="1234567" :showGroup="true" />
<dedsi-statistic title="销售额" :value="1234567" :showGroup="false" />

<!-- 不显示千分位 -->
<dedsi-statistic title="数值" :value="1000000" :showGroup="false" />

<!-- 自定义标题和前缀/后缀 -->
<dedsi-statistic :value="12345">
  <template #title>
    <UserOutlined /> 总用户数
  </template>
  <template #prefix>
    <span style="color: #1890ff">¥</span>
  </template>
  <template #suffix>
    <span style="color: #8c8c8c">元</span>
  </template>
</dedsi-statistic>

<!-- 控制动画 -->
<dedsi-statistic title="动画" :value="12345" :animation="true" />
<dedsi-statistic title="无动画" :value="12345" :animation="false" />
```

### 类型定义

```typescript
interface Props {
  title?: string
  value: number
  precision?: number
  prefix?: string
  suffix?: string
  showGroup?: boolean
  duration?: number
  animation?: boolean
  contentStyle?: Record<string, string>
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiNumberConverter 数字转换](./component-number-converter.md)
