# 组件映射

## Rule

使用 Dedsi UI 组件替代标准组件，保持统一性。

## Impact

HIGH

## Problem

混用不同组件库导致样式不一致和代码冗余。

## Solution

### 核心组件对照表

| 标准组件 | Dedsi 组件 | 关键属性/事件 | 用法示例 |
|---------|-------------|---------------|---------|
| Card | `dedsi-card` | `:bordered` | `<dedsi-card :bordered="false">` |
| Button | `dedsi-button` | `type`, `@click`, `size` | `<dedsi-button type="primary" @click="handleClick">` |
| Table | `dedsi-table` | `:columns`, `:data`, `@page-change` | 见下方示例 |
| Form | `dedsi-form` | `:model`, `layout`, `:rules`, `ref` | 见下方示例 |
| Input | `dedsi-input` | `v-model:value`, `type="textarea"` | `<dedsi-input v-model:value="value" />` |
| Select | `dedsi-select` | `v-model:value`, `:options`, `mode` | 见下方示例 |
| Modal | `dedsi-modal` | `v-model:visible`, `title`, `width` | 见下方示例 |
| Message | `DedsiMessage` | `.success()`, `.error()` | `DedsiMessage.success('操作成功')` |
| Space | `dedsi-space` | 用于按钮组间距 | 见下方示例 |
| Popconfirm | `dedsi-popconfirm` | `title`, `@confirm` | 见下方示例 |
| FormItem | `dedsi-form-item` | `label`, `name`, `:rules` | 见下方示例 |
| Typography | `dedsi-typography` | `:copyable`, `:content` | 见下方示例 |
| Tag | `dedsi-tag` | `type`, `v-if/v-else` | `<dedsi-tag type="primary">标签</dedsi-tag>` |
| Switch | `dedsi-switch` | `v-model:checked` | `<dedsi-switch v-model:checked="value" />` |

### Table 组件

```vue
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
    <!-- 自定义操作列 -->
  </template>
</dedsi-table>
```

### Form 组件

```vue
<dedsi-form layout="inline" :model="queryParam" @finish="handleSearch">
  <dedsi-form-item label="关键字">
    <dedsi-input v-model:value="queryParam.keyword" />
  </dedsi-form-item>
  <dedsi-form-item>
    <dedsi-space>
      <dedsi-button type="primary" html-type="submit">查询</dedsi-button>
      <dedsi-button @click="resetQuery">重置</dedsi-button>
    </dedsi-space>
  </dedsi-form-item>
</dedsi-form>
```

### Select 组件

```vue
<dedsi-select
  v-model:value="formState.status"
  :options="statusOptions"
  placeholder="请选择状态"
/>
```

### Modal 组件

```vue
<dedsi-modal v-model:visible="modalVisible" title="编辑" @cancel="handleModalCancel">
  <dedsi-form layout="vertical" :model="formState" @finish="handleSubmit">
    <!-- 表单项 -->
  </dedsi-form>
</dedsi-modal>
```

### Popconfirm 组件

```vue
<dedsi-popconfirm title="确定删除?" @confirm="handleDelete(record)">
  <dedsi-button type="link" danger>删除</dedsi-button>
</dedsi-popconfirm>
```

### Typography 组件（可复制文本）

```vue
<dedsi-typography :copyable="true" :content="row.id || ''">
  {{ row.id || '-' }}
</dedsi-typography>
```

### Tag 组件（状态标签）

```vue
<dedsi-tag type="primary" v-if="row.isSystem">是</dedsi-tag>
<dedsi-tag v-else>否</dedsi-tag>
```

### Switch 组件（开关）

```vue
<dedsi-form-item label="是否系统角色" name="isSystem">
  <dedsi-switch v-model:checked="formData.isSystem" />
</dedsi-form-item>
```

### Textarea 输入

```vue
<dedsi-form-item label="描述" name="description">
  <dedsi-input v-model:value="formData.description" type="textarea" placeholder="请输入描述" />
</dedsi-form-item>
```

## Related

- [View 组件模式](./view-component-patterns.md)
- [表单处理](./form-handling.md)
