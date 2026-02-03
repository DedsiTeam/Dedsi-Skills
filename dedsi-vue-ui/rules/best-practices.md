# DedsiBestPractices 组件使用最佳实践

## Rule

遵循 Dedsi Vue UI 组件库的使用规范和最佳实践，确保代码质量和一致性。

## Impact

HIGH

## Problem

不遵循最佳实践会导致代码不一致、类型安全问题、性能问题和维护困难。

## Solution

### 1. 组件导入

推荐使用按需导入以减小打包体积：

```typescript
// ✅ 推荐：按需导入
import { DedsiAlert, DedsiButton, DedsiTable } from 'dedsi-vue-ui'

app.use(DedsiAlert)
app.use(DedsiButton)
app.use(DedsiTable)
```

**避免全量导入**（除非确实需要全部组件）：

```typescript
// ❌ 不推荐：会导入所有组件
import DedsiVueUI from 'dedsi-vue-ui'
app.use(DedsiVueUI)
```

### 2. 组件命名

在模板中必须使用 kebab-case 格式：

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

### 3. 事件处理

所有组件的事件都使用 Vue 3 标准事件命名：

```vue
<!-- ✅ 推荐：使用 kebab-case 事件名 -->
<dedsi-dialog @update:visible="handleVisibleChange" />
<dedsi-table @page-change="handlePageChange" />
<dedsi-tabs @update:activeKey="handleTabChange" />

<!-- ❌ 避免：使用 camelCase -->
<dedsi-dialog @visibleChange="..." />
<dedsi-dialog @onVisibleChange="..." />
```

**事件回调类型定义**：

```typescript
const handleVisibleChange = (visible: boolean) => {
  console.log('对话框显示状态:', visible)
}

const handlePageChange = (page: number) => {
  console.log('当前页码:', page)
}
```

### 4. 双向绑定

使用 `v-model` 进行双向绑定，遵循 Vue 3 规范：

```vue
<!-- 带参数的 v-model（推荐） -->
<dedsi-tabs v-model:activeKey="activeKey" />
<dedsi-menu v-model:selectedKeys="selectedKeys" />
<dedsi-menu v-model:openKeys="openKeys" />

<!-- 不带参数的 v-model（默认 modelValue） -->
<dedsi-segmented v-model="value" />
<dedsi-input v-model="inputValue" />

<!-- 完整等价形式 -->
<dedsi-tabs
  :activeKey="activeKey"
  @update:activeKey="val => activeKey = val"
/>
```

### 5. 类型安全

推荐使用 TypeScript 以获得完整的类型提示和检查：

```typescript
// 导入类型定义
import type {
  MenuItem,
  Column,
  DropdownMenuItem,
  SegmentedOption
} from 'dedsi-vue-ui'

// 使用类型定义
const menuItems: MenuItem[] = [
  { key: 'home', label: '首页', icon: HomeIcon }
]

const columns: Column[] = [
  { key: 'name', title: '姓名', width: '200px' }
]
```

### 6. 插槽使用

**具名插槽**：

```vue
<dedsi-card>
  <template #title>
    <span>自定义标题</span>
  </template>
  <template #extra>
    <dedsi-button>更多</dedsi-button>
  </template>
  <p>卡片内容</p>
</dedsi-card>
```

**作用域插槽**：

```vue
<dedsi-table :data="data" :columns="columns">
  <template #name="{ row, value }">
    <a @click="handleClick(row)">{{ value }}</a>
  </template>
</dedsi-table>
```

### 7. 样式定制

**通过 props 修改样式**：

```vue
<!-- 使用组件提供的样式 props -->
<dedsi-card :bodyStyle="{ padding: '20px' }" />
<dedsi-statistic :contentStyle="{ color: '#3f8600' }" />
```

**通过 CSS 覆盖**（谨慎使用）：

```vue
<style scoped>
/* 使用深度选择器覆盖组件内部样式 */
.custom-card :deep(.dedsi-card-body) {
  padding: 30px;
}
</style>
```

### 8. 表格使用

```vue
<dedsi-table
  :data="tableData"
  :columns="columns"
  :total="total"
  :showIndex="true"
  @page-change="handlePageChange"
>
  <!-- 自定义操作列 -->
  <template #actions="{ row }">
    <dedsi-space>
      <dedsi-button type="link" @click="handleEdit(row)">编辑</dedsi-button>
      <dedsi-popconfirm title="确定删除?" @confirm="handleDelete(row)">
        <dedsi-button type="link" danger>删除</dedsi-button>
      </dedsi-popconfirm>
    </dedsi-space>
  </template>
</dedsi-table>
```

### 9. 对话框使用

```vue
<dedsi-dialog
  v-model:visible="visible"
  title="对话框"
  :confirmLoading="loading"
  @ok="handleOk"
  @cancel="handleCancel"
>
  <p>对话框内容</p>
</dedsi-dialog>
```

### 10. 消息提示

```typescript
// 命令式调用
instance?.proxy?.$message.success('操作成功')
instance?.proxy?.$message.error('操作失败')

// 在 API 请求中
const msg = instance?.proxy?.$message.loading('加载中...', 0)
try {
  await apiService.getData()
  msg?.close()
  instance?.proxy?.$message.success('加载成功')
} catch (error) {
  msg?.close()
  instance?.proxy?.$message.error('加载失败')
}
```

### 11. 常见问题

**Q: 组件样式不生效？**
A: 检查是否正确导入了组件样式文件，确保 CSS 已被加载。

**Q: TypeScript 报错找不到模块？**
A: 确保 `tsconfig.json` 中正确配置了模块解析，已安装依赖。

**Q: 组件无法渲染？**
A: 检查模板中是否使用了 kebab-case 格式，已正确注册组件。

**Q: 分页不工作？**
A: 确保正确设置了 `total` 和 `pageSize`，监听 `page-change` 事件。

### 12. 性能优化

**合理使用 v-once**：

```vue
<!-- 静态内容使用 v-once 避免不必要的更新 -->
<dedsi-card v-once>
  <h1>静态标题</h1>
</dedsi-card>
```

**避免不必要的响应式**：

```typescript
// ✅ 使用 shallowRef 对于大型对象
const largeData = shallowRef<DataType[]>(...)

// ✅ 使用 markRaw 跳过组件的响应式转换
import { markRaw } from 'vue'
const iconComponent = markRaw(CustomIcon)
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTable 表格](./component-table.md)
- [DedsiDialog 对话框](./component-dialog.md)
