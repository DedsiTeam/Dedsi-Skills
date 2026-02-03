# DedsiSegmented 分段器

## Rule

使用 DedsiSegmented 组件实现分段控制器，用于切换显示不同内容。

## Impact

MEDIUM

## Problem

不使用 Segmented 组件会导致分段控制器实现复杂。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| modelValue | string \| number | 是 | - | 当前值 |
| options | SegmentedOption[] | 是 | - | 选项数组 |
| block | boolean | false | 否 | 是否撑满容器宽度 |
| size | 'large' \| 'default' \| 'small' | 'default' | 否 | 尺寸 |
| disabled | boolean | false | 否 | 是否禁用 |

### SegmentedOption 接口

```typescript
interface SegmentedOption {
  label: string           // 选项文本
  value: string | number  // 选项值
  icon?: any             // 图标
  disabled?: boolean     // 是否禁用
}
```

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:modelValue | (value: string \| number) | 值变化 |
| change | (value: string \| number) | 选项变化 |

### 使用示例

```vue
<template>
  <!-- 基础用法 -->
  <dedsi-segmented
    v-model="value"
    :options="options"
    @change="handleChange"
  />

  <!-- 撑满容器 -->
  <dedsi-segmented
    v-model="value"
    :options="options"
    :block="true"
  />

  <!-- 不同尺寸 -->
  <dedsi-space direction="vertical">
    <dedsi-segmented v-model="value" :options="options" size="small" />
    <dedsi-segmented v-model="value" :options="options" size="default" />
    <dedsi-segmented v-model="value" :options="options" size="large" />
  </dedsi-space>

  <!-- 带图标 -->
  <dedsi-segmented
    v-model="value"
    :options="iconOptions"
  />

  <!-- 禁用 -->
  <dedsi-segmented
    v-model="value"
    :options="options"
    :disabled="true"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  AppstoreOutlined,
  UnorderedListOutlined,
  SettingOutlined
} from '@ant-design/icons-vue'

const value = ref('day')

const options = [
  { label: '日', value: 'day' },
  { label: '周', value: 'week' },
  { label: '月', value: 'month' },
  { label: '年', value: 'year' }
]

const iconOptions = [
  { label: '网格', value: 'grid', icon: AppstoreOutlined },
  { label: '列表', value: 'list', icon: UnorderedListOutlined },
  { label: '设置', value: 'settings', icon: SettingOutlined }
]

const handleChange = (val: string | number) => {
  console.log('切换到', val)
}
</script>
```

### 类型定义

```typescript
interface SegmentedOption {
  label: string
  value: string | number
  icon?: any
  disabled?: boolean
}

interface Props {
  modelValue: string | number
  options: SegmentedOption[]
  block?: boolean
  size?: 'large' | 'default' | 'small'
  disabled?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTabs 标签页](./component-tabs.md)
