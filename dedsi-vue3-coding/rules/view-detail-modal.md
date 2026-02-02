# 查看详情模式

## Rule

实现独立的查看详情弹窗，提供只读的数据展示。

## Impact

MEDIUM

## Problem

缺少查看详情功能影响用户体验，无法在不编辑的情况下查看完整数据。

## Solution

### 查看详情弹窗

```vue
<dedsi-modal
  v-model:visible="viewModalVisible"
  title="查看角色详情"
  width="600px"
>
  <dedsi-form layout="vertical" v-if="viewData">
    <dedsi-form-item label="角色名称">
      <dedsi-typography :copyable="true" :content="viewData.name || ''">
        {{ viewData.name || '-' }}
      </dedsi-typography>
    </dedsi-form-item>
    <dedsi-form-item label="描述">
      {{ viewData.description || '-' }}
    </dedsi-form-item>
    <dedsi-form-item label="是否系统角色">
      <dedsi-tag type="primary" v-if="viewData.isSystem">是</dedsi-tag>
      <dedsi-tag v-else>否</dedsi-tag>
    </dedsi-form-item>
    <dedsi-form-item label="权限">
      <template v-if="viewData.permissions && viewData.permissions.length > 0">
        <dedsi-tag
          v-for="permission in viewData.permissions"
          :key="permission.permissionId"
          style="margin: 2px"
          type="default"
        >
          {{ permission.permissionName }}
        </dedsi-tag>
      </template>
      <span v-else>-</span>
    </dedsi-form-item>
    <dedsi-form-item label="创建人">
      {{ viewData.creatorName || '-' }}
    </dedsi-form-item>
    <dedsi-form-item label="创建时间">
      {{ viewData.creationTime ? dayjs(viewData.creationTime).format('YYYY-MM-DD HH:mm:ss') : '-' }}
    </dedsi-form-item>
    <dedsi-form-item>
      <div style="text-align: right; width: 100%">
        <dedsi-button @click="viewModalVisible = false">关闭</dedsi-button>
      </div>
    </dedsi-form-item>
  </dedsi-form>
</dedsi-modal>
```

### 状态定义

```typescript
// 查看详情弹窗相关
const viewModalVisible = ref(false)
const viewData = ref<IRoleDto | null>(null)
```

### 查看方法

```typescript
const handleView = async (row: IRolePagedRowDto) => {
  try {
    const detail: IRoleDto = await roleApiService.get(row.id || '')
    viewData.value = detail
    viewModalVisible.value = true
  } catch (error) {
    DedsiMessage.error('获取详情失败')
  }
}
```

### 关键点

1. **弹窗配置**:
   - `v-model:visible` 控制显示
   - `title` 设置标题
   - `width` 设置宽度

2. **数据展示**:
   - 使用 `dedsi-form-item` 和 `label` 保持一致性
   - 文本内容使用 `{{ viewData.field || '-' }}` 处理空值
   - 使用 `v-if="viewData"` 确保数据加载后再渲染

3. **可复制内容**:
   - 对关键字段（如 ID、名称）使用 `dedsi-typography:copyable`

4. **列表展示**:
   - 使用 `v-for` 遍历数组
   - 对空数组显示 `-`

5. **格式化**:
   - 时间字段使用 `dayjs` 格式化
   - 布尔值使用 `dedsi-tag` 显示

### 完整示例

```typescript
<script setup lang="ts">
import { ref } from 'vue'
import dayjs from 'dayjs'
import { roleApiService } from '@/apiServices'
import { DedsiMessage } from 'dedsi-vue-ui'
import type { IRoleDto, IRolePagedRowDto } from '@/apiServices'

// 查看详情弹窗相关
const viewModalVisible = ref(false)
const viewData = ref<IRoleDto | null>(null)

const handleView = async (row: IRolePagedRowDto) => {
  try {
    const detail: IRoleDto = await roleApiService.get(row.id || '')
    viewData.value = detail
    viewModalVisible.value = true
  } catch (error) {
    DedsiMessage.error('获取详情失败')
  }
}
</script>

<template>
  <dedsi-modal
    v-model:visible="viewModalVisible"
    title="查看详情"
    width="600px"
  >
    <dedsi-form layout="vertical" v-if="viewData">
      <dedsi-form-item label="ID">
        <dedsi-typography :copyable="true" :content="viewData.id || ''">
          {{ viewData.id || '-' }}
        </dedsi-typography>
      </dedsi-form-item>
      <dedsi-form-item label="名称">
        {{ viewData.name || '-' }}
      </dedsi-form-item>
      <dedsi-form-item>
        <div style="text-align: right; width: 100%">
          <dedsi-button @click="viewModalVisible = false">关闭</dedsi-button>
        </div>
      </dedsi-form-item>
    </dedsi-form>
  </dedsi-modal>
</template>
```

## Related

- [表单处理](./form-handling.md)
- [Table 数据处理](./table-data-handling.md)
