# DedsiPopconfirm 气泡确认框

## Rule

使用 DedsiPopconfirm 组件在点击元素时显示确认气泡，防止误操作。

## Impact

HIGH

## Problem

不使用 Popconfirm 会导致删除等危险操作缺少确认环节，容易误操作。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| title | string | 是 | - | 确认框标题 |
| confirmText | string | '确定' | 否 | 确认按钮文本 |
| cancelText | string | '取消' | 否 | 取消按钮文本 |
| placement | 'top' \| 'bottom' \| 'left' \| 'right' \| ... | 'top' | 否 | 位置 |
| disabled | boolean | false | 否 | 是否禁用 |

### Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| confirm | () | 点击确认 |
| cancel | () | 点击取消 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-popconfirm
  title="确定要删除吗？"
  @confirm="handleDelete"
  @cancel="handleCancel"
>
  <dedsi-button danger>删除</dedsi-button>
</dedsi-popconfirm>

<!-- 自定义按钮文本 -->
<dedsi-popconfirm
  title="确定提交吗？"
  confirmText="是的"
  cancelText="再想想"
  @confirm="handleSubmit"
>
  <dedsi-button type="primary">提交</dedsi-button>
</dedsi-popconfirm>

<!-- 不同位置 -->
<dedsi-space>
  <dedsi-popconfirm placement="top" title="顶部提示">
    <dedsi-button>顶部</dedsi-button>
  </dedsi-popconfirm>

  <dedsi-popconfirm placement="bottom" title="底部提示">
    <dedsi-button>底部</dedsi-button>
  </dedsi-popconfirm>

  <dedsi-popconfirm placement="left" title="左侧提示">
    <dedsi-button>左侧</dedsi-button>
  </dedsi-popconfirm>

  <dedsi-popconfirm placement="right" title="右侧提示">
    <dedsi-button>右侧</dedsi-button>
  </dedsi-popconfirm>
</dedsi-space>

<!-- 在表格中使用 -->
<dedsi-table :data="data" :columns="columns">
  <template #actions="{ row }">
    <dedsi-space>
      <dedsi-button type="link" @click="handleEdit(row)">编辑</dedsi-button>
      <dedsi-popconfirm title="确定删除该用户?" @confirm="handleDelete(row)">
        <dedsi-button type="link" danger>删除</dedsi-button>
      </dedsi-popconfirm>
    </dedsi-space>
  </template>
</dedsi-table>

<!-- 禁用状态 -->
<dedsi-popconfirm title="确定删除吗?" :disabled="true">
  <dedsi-button disabled>禁用删除</dedsi-button>
</dedsi-popconfirm>
```

### 位置选项

`placement` 支持以下值：
- `top`, `topLeft`, `topRight`
- `bottom`, `bottomLeft`, `bottomRight`
- `left`, `leftTop`, `leftBottom`
- `right`, `rightTop`, `rightBottom`

### 类型定义

```typescript
type Placement =
  | 'top' | 'topLeft' | 'topRight'
  | 'bottom' | 'bottomLeft' | 'bottomRight'
  | 'left' | 'leftTop' | 'leftBottom'
  | 'right' | 'rightTop' | 'rightBottom'

interface Props {
  title: string
  confirmText?: string
  cancelText?: string
  placement?: Placement
  disabled?: boolean
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTable 表格](./component-table.md)
