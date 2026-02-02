# Table 数据处理

## Rule

统一封装数据加载逻辑，确保错误处理和加载状态的一致性。

## Impact

HIGH

## Problem

不统一的数据加载逻辑导致错误处理不一致、加载状态管理混乱。

## Solution

### 列定义

#### 方式一：使用 dataIndex（简单字段）

```typescript
const columns = [
  {
    title: 'ID',
    dataIndex: 'id',
    key: 'id'
  },
  {
    title: '描述',
    dataIndex: 'description',
    key: 'description'
  }
]
```

#### 方式二：使用 slot（推荐，复杂字段）

```typescript
const columns = [
  { title: 'id', slot: 'id', key: 'id' },
  { title: '角色名称', slot: 'name', key: 'name' },
  { title: '描述', dataIndex: 'description', key: 'description' },
  { title: '是否系统角色', slot: 'isSystem', key: 'isSystem', width: 120 },
  { title: '创建时间', slot: 'creationTime', key: 'creationTime', width: 180 },
  { title: '操作', slot: 'actions', key: 'actions', width: 200 }
]
```

#### Template 插槽使用

```vue
<dedsi-table
  :columns="columns"
  :data="tableData"
  ...
>
  <!-- ID 可复制 -->
  <template #id="{ row }">
    <dedsi-typography :copyable="true" :content="row.id || ''">
      {{ row.id || '-' }}
    </dedsi-typography>
  </template>

  <!-- 名称可复制 -->
  <template #name="{ row }">
    <dedsi-typography :copyable="true" :content="row.name || ''">
      {{ row.name || '-' }}
    </dedsi-typography>
  </template>

  <!-- 时间格式化 -->
  <template #creationTime="{ row }">
    {{ row.creationTime ? dayjs(row.creationTime).format('YYYY-MM-DD HH:mm:ss') : '-' }}
  </template>

  <!-- Tag 状态 -->
  <template #isSystem="{ row }">
    <dedsi-tag type="primary" v-if="row.isSystem">是</dedsi-tag>
    <dedsi-tag v-else>否</dedsi-tag>
  </template>

  <!-- 操作列 -->
  <template #actions="{ row }">
    <dedsi-space>
      <dedsi-button type="link" size="small" @click="handleView(row)">查看</dedsi-button>
      <dedsi-button type="link" size="small" @click="handleEdit(row)">编辑</dedsi-button>
      <dedsi-popconfirm title="确定删除?" @confirm="handleDelete(row)">
        <dedsi-button type="link" size="small" danger>删除</dedsi-button>
      </dedsi-popconfirm>
    </dedsi-space>
  </template>
</dedsi-table>
```

### 数据加载

统一封装 `fetchData` 方法：

```typescript
const fetchData = async () => {
  tableLoading.value = true
  try {
    const res = await userApiService.pagedQuery({
      ...queryParam,
      pageIndex: pagination.current,
      pageSize: pagination.pageSize
    })
    tableData.value = res.items
    pagination.total = res.totalCount
  } catch (error) {
    DedsiMessage.error('加载数据失败')
    console.error(error)
  } finally {
    tableLoading.value = false
  }
}
```

### 分页处理

```typescript
const handlePageChange = (page: number, pageSize: number) => {
  pagination.current = page
  pagination.pageSize = pageSize
  fetchData()
}
```

### 生命周期

```typescript
onMounted(() => {
  fetchData()
})
```

### 完整示例

```typescript
<script setup lang="ts">
import { reactive, ref, onMounted } from 'vue'
import dayjs from 'dayjs'
import { userApiService } from '@/apiServices'
import { DedsiMessage } from 'dedsi-vue-ui'

const tableLoading = ref(false)
const tableData = ref<UserDto[]>()

const pagination = reactive({
  current: 1,
  pageSize: 10,
  total: 0,
  showSizeChanger: true,
  showTotal: (total: number) => `共 ${total} 条`
})

const queryParam = reactive<Partial<UserPagedInputDto>>({
  keyword: undefined
})

const columns = [
  { title: 'id', slot: 'id', key: 'id' },
  { title: '名称', slot: 'name', key: 'name' },
  { title: '描述', dataIndex: 'description', key: 'description' },
  { title: '创建时间', slot: 'creationTime', key: 'creationTime', width: 180 },
  { title: '操作', slot: 'actions', key: 'actions', width: 200 }
]

const fetchData = async () => {
  tableLoading.value = true
  try {
    const res = await userApiService.pagedQuery({
      ...queryParam,
      pageIndex: pagination.current,
      pageSize: pagination.pageSize
    })
    tableData.value = res.items
    pagination.total = res.totalCount
  } catch (error) {
    DedsiMessage.error('加载数据失败')
    console.error(error)
  } finally {
    tableLoading.value = false
  }
}

const handlePageChange = (page: number, pageSize: number) => {
  pagination.current = page
  pagination.pageSize = pageSize
  fetchData()
}

onMounted(() => {
  fetchData()
})
</script>

<template>
  <dedsi-table
    :columns="columns"
    :data="tableData"
    :total="pagination.total"
    :loading="tableLoading"
    @page-change="handlePageChange"
  >
    <template #id="{ row }">
      <dedsi-typography :copyable="true" :content="row.id || ''">
        {{ row.id || '-' }}
      </dedsi-typography>
    </template>

    <template #name="{ row }">
      {{ row.name || '-' }}
    </template>

    <template #creationTime="{ row }">
      {{ row.creationTime ? dayjs(row.creationTime).format('YYYY-MM-DD HH:mm:ss') : '-' }}
    </template>

    <template #actions="{ row }">
      <dedsi-space>
        <dedsi-button type="link" size="small" @click="handleEdit(row)">编辑</dedsi-button>
        <dedsi-popconfirm title="确定删除?" @confirm="handleDelete(row)">
          <dedsi-button type="link" size="small" danger>删除</dedsi-button>
        </dedsi-popconfirm>
      </dedsi-space>
    </template>
  </dedsi-table>
</template>
```

## Related

- [View 组件模式](./view-component-patterns.md)
- [组件映射](./component-mapping.md)
