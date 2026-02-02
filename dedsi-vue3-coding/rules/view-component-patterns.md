# View 组件模式

## Rule

页面组件使用 Composition API (`<script setup lang="ts">`) 风格开发。

## Impact

HIGH

## Problem

不统一的组件开发风格导致代码维护困难和类型安全问题。

## Solution

### 页面布局结构

标准页面包含三个核心部分：

```vue
<template>
  <!-- 1. 搜索卡片 -->
  <dedsi-card :bordered="false">
    <dedsi-form layout="inline" :model="queryParam">
      <dedsi-form-item label="关键字">
        <dedsi-input v-model:value="queryParam.keyword" />
      </dedsi-form-item>
      <dedsi-form-item>
        <dedsi-space>
          <dedsi-button type="primary" @click="fetchData">查询</dedsi-button>
          <dedsi-button @click="resetQuery">重置</dedsi-button>
        </dedsi-space>
      </dedsi-form-item>
    </dedsi-form>
  </dedsi-card>

  <!-- 2. 表格卡片 -->
  <dedsi-card :bordered="false">
    <dedsi-table
      :columns="columns"
      :data="tableData"
      :total="pagination.total"
      :page-size="pagination.pageSize"
      :loading="tableLoading"
      @page-change="handlePageChange"
      @page-size-change="handlePageSizeChange"
    >
      <template #action="{ record }">
        <dedsi-space>
          <dedsi-button type="link" @click="handleEdit(record)">编辑</dedsi-button>
          <dedsi-popconfirm title="确定删除?" @confirm="handleDelete(record)">
            <dedsi-button type="link" danger>删除</dedsi-button>
          </dedsi-popconfirm>
        </dedsi-space>
      </template>
    </dedsi-table>
  </dedsi-card>

  <!-- 3. 弹窗表单 -->
  <dedsi-modal v-model:visible="modalVisible" title="编辑" @cancel="handleModalCancel">
    <dedsi-form layout="vertical" :model="formState" @finish="handleSubmit">
      <dedsi-form-item label="名称" name="name" :rules="[{ required: true, message: '请输入名称' }]">
        <dedsi-input v-model:value="formState.name" />
      </dedsi-form-item>
    </dedsi-form>
  </dedsi-modal>
</template>
```

### 核心状态定义

```typescript
<script setup lang="ts">
import { reactive, ref } from 'vue'

// 表格数据
const tableLoading = ref(false)
const tableData = ref<UserDto[]>()

// 分页配置
const pagination = reactive({
  current: 1,
  pageSize: 10,
  total: 0
})

// 查询参数
const queryParam = reactive<Partial<UserPagedInputDto>>({
  keyword: undefined
})

// 弹窗表单
const modalVisible = ref(false)
const formState = reactive<UserCreateUpdateDto>({
  name: '',
  email: ''
})
</script>
```

### Composition API 导入

```typescript
<script setup lang="ts">
import { reactive, ref, onMounted, computed, watch } from 'vue'
</script>
```

## Related

- [组件映射](./component-mapping.md)
- [Table 数据处理](./table-data-handling.md)
- [表单处理](./form-handling.md)
