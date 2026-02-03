# DedsiMenu 导航菜单

## Rule

使用 DedsiMenu 组件实现导航菜单，支持多级嵌套、折叠和主题切换。

## Impact

HIGH

## Problem

不正确使用 Menu 组件会导致菜单项无法正常展开或状态管理混乱。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| items | MenuItem[] | 是 | - | 菜单项数据数组 |
| mode | 'vertical' \| 'horizontal' \| 'inline' | 'inline' | 否 | 菜单模式 |
| theme | 'light' \| 'dark' | 'light' | 否 | 菜单主题 |
| selectedKeys | string[] | [] | 否 | 当前选中的菜单项 key 数组 |
| openKeys | string[] | [] | 否 | 当前展开的子菜单 key 数组 |
| collapsed | boolean | false | 否 | 是否收起菜单（仅 inline） |

### MenuItem 接口

```typescript
interface MenuItem {
  key: string              // 唯一标识
  label: string            // 菜单项标题
  icon?: any              // 图标组件
  disabled?: boolean      // 是否禁用
  children?: MenuItem[]   // 子菜单项
}
```

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:selectedKeys | (keys: string[]) | 选中项变化 |
| update:openKeys | (keys: string[]) | 展开项变化 |
| click | (item: MenuItem) | 点击菜单项 |

### 使用示例

```vue
<template>
  <dedsi-menu
    :items="menuItems"
    v-model:selectedKeys="selectedKeys"
    v-model:openKeys="openKeys"
    mode="inline"
    theme="dark"
    :collapsed="collapsed"
    @click="handleMenuClick"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { HomeOutlined, AppstoreOutlined, SettingOutlined } from '@ant-design/icons-vue'

interface MenuItem {
  key: string
  label: string
  icon?: any
  disabled?: boolean
  children?: MenuItem[]
}

const selectedKeys = ref(['home'])
const openKeys = ref([])
const collapsed = ref(false)

const menuItems: MenuItem[] = [
  {
    key: 'home',
    label: '首页',
    icon: HomeOutlined
  },
  {
    key: 'components',
    label: '组件',
    icon: AppstoreOutlined,
    children: [
      { key: 'basic', label: '基础组件' },
      { key: 'layout', label: '布局组件' },
      { key: 'data', label: '数据展示' },
      { key: 'feedback', label: '反馈组件' }
    ]
  },
  {
    key: 'settings',
    label: '设置',
    icon: SettingOutlined,
    children: [
      { key: 'profile', label: '个人资料' },
      { key: 'security', label: '安全设置' }
    ]
  }
]

const handleMenuClick = (item: MenuItem) => {
  console.log('点击菜单', item)
}
</script>
```

### 菜单模式

- `inline`: 内嵌菜单，支持折叠（默认）
- `vertical`: 垂直菜单
- `horizontal`: 水平菜单

### 双向绑定

使用 `v-model` 实现双向绑定：

```vue
<!-- 选中状态 -->
<dedsi-menu v-model:selectedKeys="selectedKeys" />

<!-- 展开状态 -->
<dedsi-menu v-model:openKeys="openKeys" />
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTabs 标签页](./component-tabs.md)
