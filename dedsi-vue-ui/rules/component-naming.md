# 组件命名规范

## Rule

Dedsi UI 组件的命名和使用必须遵循前缀规范和模板中的 kebab-case 格式。

## Impact

HIGH

## Problem

不正确的组件命名会导致组件无法正确渲染和类型错误。

## Solution

### 组件命名前缀

所有组件使用 `Dedsi` 前缀命名：

```typescript
// 导入时使用 PascalCase
import { DedsiAlert, DedsiButton, DedsiTable } from 'dedsi-vue-ui'
```

### 模板中使用 kebab-case

**重要**：在模板中使用组件时，必须使用 kebab-case 格式的 name 值。

| 导入名称 | 模板中使用 |
|---------|-----------|
| `DedsiTable` | `dedsi-table` |
| `DedsiButton` | `dedsi-button` |
| `DedsiTag` | `dedsi-tag` |
| `DedsiAlert` | `dedsi-alert` |
| `DedsiTabs` | `dedsi-tabs` |
| `DedsiTabPane` | `dedsi-tab-pane` |
| `DedsiMenu` | `dedsi-menu` |
| `DedsiMenuItem` | `dedsi-menu-item` |
| `DedsiSubMenu` | `dedsi-sub-menu` |
| `DedsiRow` | `dedsi-row` |
| `DedsiCol` | `dedsi-col` |

### 正确使用示例

```vue
<template>
  <!-- ✅ 正确：使用 kebab-case -->
  <dedsi-button>点击</dedsi-button>
  <dedsi-table :data="data" :columns="columns" />
  <dedsi-alert type="success" title="成功" />

  <!-- ❌ 错误：不要使用 PascalCase -->
  <DedsiButton>点击</DedsiButton>
  <DedsiTable :data="data" :columns="columns" />
</template>

<script setup lang="ts">
// ✅ 导入时使用 PascalCase
import { DedsiButton, DedsiTable, DedsiAlert } from 'dedsi-vue-ui'
</script>
```

### 注册组件

```typescript
import { createApp } from 'vue'
import { DedsiAlert, DedsiButton } from 'dedsi-vue-ui'

const app = createApp(App)
app.use(DedsiAlert)
app.use(DedsiButton)
```

## Related

- [基础组件](./components-basic.md)
- [布局组件](./components-layout.md)
- [数据展示组件](./components-data-display.md)
- [反馈组件](./components-feedback.md)
