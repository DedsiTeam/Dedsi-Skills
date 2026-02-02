# 图标使用

## Rule

使用 `@ant-design/icons-vue` 提供的图标增强用户体验。

## Impact

MEDIUM

## Problem

缺少图标导致界面单调，用户操作不够直观。

## Solution

### 图标导入

```typescript
import { SearchOutlined, ReloadOutlined, PlusOutlined } from '@ant-design/icons-vue'
```

### 按钮图标

使用 `<template #icon>` 插槽：

```vue
<dedsi-button type="primary" @click="handleSearch">
  <template #icon><SearchOutlined /></template>
  查询
</dedsi-button>

<dedsi-button @click="handleReset">
  <template #icon><ReloadOutlined /></template>
  重置
</dedsi-button>

<dedsi-button type="primary" @click="handleCreate">
  <template #icon><PlusOutlined /></template>
  新建角色
</dedsi-button>
```

### 常用图标

| 图标名称 | 用途 |
|---------|------|
| `SearchOutlined` | 搜索按钮 |
| `ReloadOutlined` | 重置/刷新按钮 |
| `PlusOutlined` | 新增按钮 |
| `EditOutlined` | 编辑按钮 |
| `DeleteOutlined` | 删除按钮 |
| `EyeOutlined` | 查看按钮 |
| `CloseOutlined` | 关闭按钮 |
| `CheckOutlined` | 确认/保存按钮 |
| `ExportOutlined` | 导出按钮 |
| `DownloadOutlined` | 下载按钮 |
| `UploadOutlined` | 上传按钮 |

### Popconfirm 图标

在确认对话框中使用自定义图标：

```vue
<dedsi-popconfirm title="确定删除?" @confirm="handleDelete(row)">
  <template #icon>
    <ExclamationCircleOutlined style="color: #ff4d4f" />
  </template>
  <dedsi-button type="link" danger>删除</dedsi-button>
</dedsi-popconfirm>
```

```typescript
import { ExclamationCircleOutlined } from '@ant-design/icons-vue'
```

### 完整示例

```vue
<template>
  <dedsi-card :bordered="false">
    <div class="flex-between">
      <dedsi-form layout="inline" :model="queryParam">
        <dedsi-form-item>
          <dedsi-input v-model:value="queryParam.name" />
        </dedsi-form-item>
        <dedsi-form-item>
          <dedsi-space>
            <dedsi-button type="primary" @click="handleSearch">
              <template #icon><SearchOutlined /></template>
              查询
            </dedsi-button>
            <dedsi-button @click="handleReset">
              <template #icon><ReloadOutlined /></template>
              重置
            </dedsi-button>
          </dedsi-space>
        </dedsi-form-item>
      </dedsi-form>

      <dedsi-button type="primary" @click="handleCreate">
        <template #icon><PlusOutlined /></template>
        新建
      </dedsi-button>
    </div>
  </dedsi-card>
</template>

<script setup lang="ts">
import { SearchOutlined, ReloadOutlined, PlusOutlined } from '@ant-design/icons-vue'
</script>
```

## Related

- [View 组件模式](./view-component-patterns.md)
- [组件映射](./component-mapping.md)
