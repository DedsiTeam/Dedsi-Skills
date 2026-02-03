# DedsiNumberConverter 数字转换

## Rule

使用 DedsiNumberConverter 组件将数字转换为万/千万/亿单位显示。

## Impact

LOW

## Problem

不使用 NumberConverter 组件会导致大数字显示不直观。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| value | number \| string | 是 | - | 要转换的数值 |
| targetUnit | 'none' \| 'wan' \| 'qianwan' \| 'yi' \| 'auto' | 'auto' | 否 | 目标单位，auto 自动选择 |
| precision | number | 2 | 否 | 保留小数位数 |
| suffix | string | '' | 否 | 单位后缀（如"元"、"人"） |

### 使用示例

```vue
<!-- 自动转换 -->
<dedsi-number-converter :value="12345" />
<!-- 显示：1.23万 -->

<dedsi-number-converter :value="12345678" />
<!-- 显示：1234.57千万 -->

<dedsi-number-converter :value="123456789" />
<!-- 显示：1.23亿 -->

<!-- 指定单位 -->
<dedsi-number-converter :value="12345" targetUnit="wan" suffix="元" />
<!-- 显示：1.23万元 -->

<dedsi-number-converter :value="12345678" targetUnit="qianwan" />
<!-- 显示：1234.57千万 -->

<dedsi-number-converter :value="123456789" targetUnit="yi" />
<!-- 显示：1.23亿 -->

<!-- 不转换 -->
<dedsi-number-converter :value="12345" targetUnit="none" />
<!-- 显示：12345 -->

<!-- 自定义精度 -->
<dedsi-number-converter :value="12345" :precision="1" />
<!-- 显示：1.2万 -->

<!-- 自定义后缀 -->
<dedsi-number-converter :value="12345" suffix="人" />
<!-- 显示：1.23万人 -->
```

### 单位说明

| 单位 | 数值范围 | 示例 |
|------|----------|------|
| none | 不转换 | 12345 |
| wan | ≥ 10000 | 1.23万 |
| qianwan | ≥ 10000000 | 1234.57千万 |
| yi | ≥ 100000000 | 1.23亿 |
| auto | 自动选择 | 根据数值自动选择最合适的单位 |

### 类型定义

```typescript
interface Props {
  value: number | string
  targetUnit?: 'none' | 'wan' | 'qianwan' | 'yi' | 'auto'
  precision?: number
  suffix?: string
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiStatistic 统计数值](./component-statistic.md)
