# DedsiTable 表格完整指南

## Rule

使用 DedsiTable 组件展示数据，支持分页、自定义列渲染、序号显示和空数据状态。

## Impact

HIGH

## Problem

不正确使用 Table 组件会导致数据展示错误、分页功能失效或类型安全问题。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| data | Record<string, any>[] | 是 | - | 表格数据数组 |
| columns | Column[] | 是 | - | 列配置数组 |
| total | number | 是 | - | 数据总条数 |
| pageSize | number | 10 | 否 | 每页显示条数 |
| bordered | boolean | false | 否 | 是否显示边框 |
| showIndex | boolean | false | 否 | 是否显示序号列 |
| indexColumnTitle | string | '序号' | 否 | 序号列的标题 |
| pagination | boolean | true | 否 | 是否显示分页器 |

### Column 接口

```typescript
interface Column {
  key: string                        // 列的唯一标识，用于插槽匹配
  title: string                      // 列标题
  width?: string                     // 列宽度（CSS 值，如 '200px', '20%'）
  align?: 'left' | 'center' | 'right'  // 对齐方式
}
```

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| page-change | (page: number) | 页码变化 |
| page-size-change | (pageSize: number) | 每页条数变化 |

### Slots

| 插槽名 | 参数 | 说明 |
|--------|------|------|
| [column.key] | { row: any, value: any } | 自定义列内容，插槽名为列的 key |

### 完整使用示例

```vue
<template>
  <dedsi-card title="用户列表">
    <dedsi-table
      :data="tableData"
      :columns="columns"
      :total="total"
      :pageSize="pageSize"
      :showIndex="true"
      :bordered="true"
      @page-change="handlePageChange"
      @page-size-change="handlePageSizeChange"
    >
      <!-- 自定义状态列 -->
      <template #status="{ row, value }">
        <dedsi-tag :type="value === 1 ? 'success' : 'danger'">
          {{ value === 1 ? '启用' : '禁用' }}
        </dedsi-tag>
      </template>

      <!-- 自定义时间列 -->
      <template #createdAt="{ value }">
        {{ dayjs(value).format('YYYY-MM-DD HH:mm:ss') }}
      </template>

      <!-- 自定义操作列 -->
      <template #actions="{ row }">
        <dedsi-space>
          <dedsi-button type="link" @click="handleView(row)">查看</dedsi-button>
          <dedsi-button type="link" @click="handleEdit(row)">编辑</dedsi-button>
          <dedsi-popconfirm title="确定删除该用户?" @confirm="handleDelete(row)">
            <dedsi-button type="link" danger>删除</dedsi-button>
          </dedsi-popconfirm>
        </dedsi-space>
      </template>
    </dedsi-table>
  </dedsi-card>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import dayjs from 'dayjs'

interface User {
  id: string
  name: string
  email: string
  status: number
  createdAt: string
}

interface Column {
  key: string
  title: string
  width?: string
  align?: 'left' | 'center' | 'right'
}

const tableData = ref<User[]>([])
const total = ref(0)
const pageSize = ref(10)

const columns: Column[] = [
  { key: 'name', title: '姓名', width: '150px' },
  { key: 'email', title: '邮箱', width: '200px' },
  { key: 'status', title: '状态', width: '100px', align: 'center' },
  { key: 'createdAt', title: '创建时间', width: '180px', align: 'center' },
  { key: 'actions', title: '操作', width: '200px', align: 'center' }
]

const fetchData = async (page: number) => {
  // API 调用
  const response = await userApiService.pagedQuery({
    pageIndex: page,
    pageSize: pageSize.value
  })
  tableData.value = response.items
  total.value = response.totalCount
}

const handlePageChange = (page: number) => {
  fetchData(page)
}

const handlePageSizeChange = (newPageSize: number) => {
  pageSize.value = newPageSize
  fetchData(1)
}

const handleView = (row: User) => {
  console.log('查看', row)
}

const handleEdit = (row: User) => {
  console.log('编辑', row)
}

const handleDelete = async (row: User) => {
  await userApiService.delete(row.id)
  fetchData(1)
}

onMounted(() => {
  fetchData(1)
})
</script>
```

### 分页功能说明

Table 组件内置分页器，自动根据 `total` 和 `pageSize` 计算页码：

- 显示当前页/总页数
- 显示每页条数和总条数
- 支持上一页/下一页
- 支持页码跳转
- 支持切换每页条数（10/20/50/100）

分页器仅在 `total > pageSize` 时显示。

### 序号列

设置 `showIndex="true"` 后，表格会自动显示序号列：

- 序号根据当前页码和每页条数自动计算
- 可通过 `indexColumnTitle` 自定义列标题

### 空数据状态

当 `data.length === 0` 时，自动显示"暂无数据"提示，跨所有列。

### 类型安全

```typescript
// 使用泛型定义表格数据类型
const tableData = ref<User[]>([])

// 定义列类型
const columns: Column[] = [
  { key: 'name', title: '姓名' }
]

// 插槽参数也有类型提示
<template #name="{ row, value }">
  <!-- row: User, value: string -->
</template>
```

### 性能优化

- 使用 `key` 属性确保列表正确渲染
- 对于大型数据集，考虑虚拟滚动（需自行实现）
- 避免在插槽中执行复杂计算

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTag 标签](./component-tag.md)
- [DedsiSpace 间距](./component-space.md)
- [DedsiPopconfirm 气泡确认框](./component-popconfirm.md)
