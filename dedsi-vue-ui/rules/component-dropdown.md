# DedsiDropdown 下拉菜单

## Rule

使用 DedsiDropdown 组件显示下拉菜单，支持点击和悬停触发。

## Impact

MEDIUM

## Problem

不正确使用 Dropdown 组件会导致菜单无法正常展开或选项点击失效。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| menu | DropdownMenuItem[] | 是 | - | 菜单项数组 |
| trigger | 'hover' \| 'click' | 'hover' | 否 | 触发方式 |
| placement | 'bottomLeft' \| 'bottomCenter' \| 'bottomRight' \| ... | 'bottomLeft' | 否 | 菜单位置 |
| disabled | boolean | false | 否 | 是否禁用 |

### DropdownMenuItem 接口

```typescript
interface DropdownMenuItem {
  key: string | number     // 唯一标识
  label?: string           // 菜单项文本
  icon?: any              // 图标组件
  disabled?: boolean      // 是否禁用
  danger?: boolean        // 是否危险操作（红色）
  type?: 'item' | 'divider'  // 类型：菜单项或分割线
}
```

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| select | (key: string \| number) | 选择菜单项时触发 |

### 使用示例

```vue
<template>
  <!-- 基础用法 -->
  <dedsi-dropdown :menu="menuItems" @select="handleSelect">
    <dedsi-button>下拉菜单</dedsi-button>
  </dedsi-dropdown>

  <!-- 点击触发 -->
  <dedsi-dropdown :menu="menuItems" trigger="click">
    <dedsi-button>点击触发</dedsi-button>
  </dedsi-dropdown>

  <!-- 不同位置 -->
  <dedsi-space>
    <dedsi-dropdown :menu="menuItems" placement="bottomLeft">
      <dedsi-button>左下</dedsi-button>
    </dedsi-dropdown>
    <dedsi-dropdown :menu="menuItems" placement="bottomRight">
      <dedsi-button>右下</dedsi-button>
    </dedsi-dropdown>
    <dedsi-dropdown :menu="menuItems" placement="topLeft">
      <dedsi-button>左上</dedsi-button>
    </dedsi-dropdown>
    <dedsi-dropdown :menu="menuItems" placement="topRight">
      <dedsi-button>右上</dedsi-button>
    </dedsi-dropdown>
  </dedsi-space>

  <!-- 带图标 -->
  <dedsi-dropdown :menu="iconMenuItems">
    <dedsi-button>
      <template #icon>
        <SettingOutlined />
      </template>
      设置
    </dedsi-button>
  </dedsi-dropdown>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { EditOutlined, DeleteOutlined, UserOutlined, SettingOutlined } from '@ant-design/icons-vue'

interface DropdownMenuItem {
  key: string | number
  label?: string
  icon?: any
  disabled?: boolean
  danger?: boolean
  type?: 'item' | 'divider'
}

const menuItems: DropdownMenuItem[] = [
  { key: '1', label: '编辑', icon: EditOutlined },
  { key: '2', label: '删除', icon: DeleteOutlined, danger: true },
  { type: 'divider' },
  { key: '3', label: '详情' },
  { key: '4', label: '禁用项', disabled: true }
]

const iconMenuItems: DropdownMenuItem[] = [
  { key: 'profile', label: '个人资料', icon: UserOutlined },
  { key: 'settings', label: '系统设置', icon: SettingOutlined }
]

const handleSelect = (key: string | number) => {
  console.log('选择了菜单项', key)
}
</script>
```

### 位置选项

`placement` 支持以下值：
- `bottomLeft`, `bottomCenter`, `bottomRight`
- `topLeft`, `topCenter`, `topRight`

### 类型定义

```typescript
type Placement =
  | 'bottomLeft' | 'bottomCenter' | 'bottomRight'
  | 'topLeft' | 'topCenter' | 'topRight'

interface DropdownMenuItem {
  key: string | number
  label?: string
  icon?: any
  disabled?: boolean
  danger?: boolean
  type?: 'item' | 'divider'
}

interface Props {
  menu: DropdownMenuItem[]
  trigger?: 'hover' | 'click'
  placement?: Placement
  disabled?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiMenu 导航菜单](./component-menu.md)
