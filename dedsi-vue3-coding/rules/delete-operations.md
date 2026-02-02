# 删除操作

## Rule

使用确认组件防止误操作，确保删除操作的安全性。

## Impact

MEDIUM

## Problem

直接删除导致用户误操作，造成数据丢失。

## Solution

### 基本删除操作

使用 `dedsi-popconfirm` 包裹删除按钮：

```vue
<template #action="{ record }">
  <dedsi-space>
    <dedsi-button type="link" @click="handleEdit(record)">编辑</dedsi-button>
    <dedsi-popconfirm
      title="确定删除该数据吗?"
      @confirm="handleDelete(record)"
    >
      <dedsi-button type="link" danger>删除</dedsi-button>
    </dedsi-popconfirm>
  </dedsi-space>
</template>
```

### 删除处理函数

```typescript
const handleDelete = async (record: UserDto) => {
  try {
    await userApiService.delete(record.id)
    DedsiMessage.success('删除成功')
    // 如果是最后一页且删除后没有数据，回到上一页
    if (tableData.value && tableData.value.length === 1 && pagination.current > 1) {
      pagination.current -= 1
    }
    fetchData()
  } catch (error) {
    DedsiMessage.error('删除失败')
    console.error(error)
  }
}
```

### 批量删除

```vue
<dedsi-table
  :row-selection="rowSelection"
  ...
>
</dedsi-table>

<dedsi-space>
  <dedsi-button type="primary" @click="fetchData">查询</dedsi-button>
  <dedsi-button
    danger
    :disabled="!hasSelected"
    @click="handleBatchDelete"
  >
    批量删除 ({{ selectedRowKeys.length }})
  </dedsi-button>
</dedsi-space>
```

```typescript
const selectedRowKeys = ref<string[]>([])
const hasSelected = computed(() => selectedRowKeys.value.length > 0)

const rowSelection = {
  selectedRowKeys,
  onChange: (keys: string[]) => {
    selectedRowKeys.value = keys
  }
}

const handleBatchDelete = async () => {
  try {
    await userApiService.batchDelete(selectedRowKeys.value)
    DedsiMessage.success('批量删除成功')
    selectedRowKeys.value = []
    fetchData()
  } catch (error) {
    DedsiMessage.error('批量删除失败')
  }
}
```

### 高级确认

自定义确认内容：

```vue
<dedsi-popconfirm @confirm="handleDelete(record)">
  <template #title>
    确定删除用户 <b>{{ record.name }}</b> 吗？此操作不可恢复。
  </template>
  <template #icon>
    <ExclamationCircleOutlined style="color: #ff4d4f" />
  </template>
  <dedsi-button type="link" danger>删除</dedsi-button>
</dedsi-popconfirm>
```

## Related

- [Table 数据处理](./table-data-handling.md)
- [表单处理](./form-handling.md)
