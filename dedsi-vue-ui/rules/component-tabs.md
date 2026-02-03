# DedsiTabs 标签页

## Rule

使用 DedsiTabs 和 DedsiTabPane 组件实现选项卡切换功能。

## Impact

MEDIUM

## Problem

不正确使用 Tabs 组件会导致标签页无法正常切换或编辑功能失效。

## Solution

### DedsiTabs Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| modelValue / activeKey | string \| number | - | 否 | 当前激活的标签页 key |
| type | 'line' \| 'card' \| 'editable-card' | 'line' | 否 | 标签页类型 |
| hideAdd | boolean | false | 否 | 是否隐藏添加按钮（editable-card） |
| size | 'default' \| 'small' | 'default' | 否 | 标签页尺寸 |
| tabBarGutter | number | - | 否 | 标签之间的间距 |

### DedsiTabPane Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| label | string | 是 | - | 标签文本 |
| name | string \| number | 是 | - | 标签的唯一标识 |
| disabled | boolean | false | 否 | 是否禁用 |
| closable | boolean | true | 否 | 是否可关闭（editable-card） |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| update:modelValue | (name: string \| number) | 激活标签变化 |
| update:activeKey | (name: string \| number) | 激活标签变化 |
| tab-click | (name: string \| number) | 点击标签 |
| change | (name: string \| number) | 切换标签 |
| edit | (targetKey: string \| number, action: 'add' \| 'remove') | 添加/删除标签 |

### Slots

| 插槽名 | 组件 | 说明 |
|--------|------|------|
| default | DedsiTabs | 标签页内容（DedsiTabPane） |
| extra | DedsiTabs | 标签栏右侧额外操作区 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-tabs v-model:activeKey="activeKey">
  <dedsi-tab-pane name="1" label="标签1">内容1</dedsi-tab-pane>
  <dedsi-tab-pane name="2" label="标签2">内容2</dedsi-tab-pane>
  <dedsi-tab-pane name="3" label="标签3">内容3</dedsi-tab-pane>
</dedsi-tabs>

<!-- 卡片类型 -->
<dedsi-tabs type="card">
  <dedsi-tab-pane name="1" label="标签1">内容1</dedsi-tab-pane>
  <dedsi-tab-pane name="2" label="标签2">内容2</dedsi-tab-pane>
</dedsi-tabs>

<!-- 可编辑标签页 -->
<dedsi-tabs
  v-model:activeKey="activeKey"
  type="editable-card"
  @edit="handleEdit"
  @change="handleChange"
>
  <dedsi-tab-pane
    v-for="tab in tabs"
    :key="tab.key"
    :name="tab.key"
    :label="tab.label"
    :closable="tab.closable"
  >
    {{ tab.content }}
  </dedsi-tab-pane>
</dedsi-tabs>

<!-- 带额外操作 -->
<dedsi-tabs v-model:activeKey="activeKey">
  <template #extra>
    <dedsi-button size="small">操作</dedsi-button>
  </template>
  <dedsi-tab-pane name="1" label="标签1">内容1</dedsi-tab-pane>
</dedsi-tabs>
```

### 可编辑标签页实现

```vue
<script setup lang="ts">
import { ref } from 'vue'

interface Tab {
  key: string
  label: string
  content: string
  closable: boolean
}

const activeKey = ref('1')
const tabs = ref<Tab[]>([
  { key: '1', label: '标签1', content: '内容1', closable: false },
  { key: '2', label: '标签2', content: '内容2', closable: true },
  { key: '3', label: '标签3', content: '内容3', closable: true }
])

const handleEdit = (targetKey: string | number, action: 'add' | 'remove') => {
  if (action === 'add') {
    const newKey = String(tabs.value.length + 1)
    tabs.value.push({
      key: newKey,
      label: `新标签${newKey}`,
      content: `新内容${newKey}`,
      closable: true
    })
    activeKey.value = newKey
  } else if (action === 'remove') {
    const index = tabs.value.findIndex(tab => tab.key === targetKey)
    tabs.value.splice(index, 1)
    if (activeKey.value === targetKey) {
      activeKey.value = tabs.value[Math.max(0, index - 1)].key
    }
  }
}

const handleChange = (key: string | number) => {
  console.log('切换到标签', key)
}
</script>
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiMenu 导航菜单](./component-menu.md)
