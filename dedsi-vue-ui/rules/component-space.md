# DedsiSpace 间距

## Rule

使用 DedsiSpace 组件设置组件之间的间距，简化布局代码。

## Impact

MEDIUM

## Problem

不使用 Space 组件会导致间距设置不一致，需要手动处理 margin。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| direction | 'horizontal' \| 'vertical' | 'horizontal' | 否 | 间距方向 |
| size | 'small' \| 'middle' \| 'large' \| number \| [number, number] | 'small' | 否 | 间距大小，支持数组 [水平, 垂直] |
| wrap | boolean | false | 否 | 是否自动换行 |
| align | 'start' \| 'end' \| 'center' \| 'baseline' | - | 否 | 对齐方式 |

### 使用示例

```vue
<!-- 水平间距 -->
<dedsi-space :size="'middle'">
  <dedsi-button>按钮1</dedsi-button>
  <dedsi-button>按钮2</dedsi-button>
  <dedsi-button>按钮3</dedsi-button>
</dedsi-space>

<!-- 垂直间距 -->
<dedsi-space direction="vertical" :size="20">
  <div>内容1</div>
  <div>内容2</div>
  <div>内容3</div>
</dedsi-space>

<!-- 不同尺寸 -->
<dedsi-space :size="'small'">
  <span>小间距</span>
  <span>小间距</span>
</dedsi-space>

<dedsi-space :size="'large'">
  <span>大间距</span>
  <span>大间距</span>
</dedsi-space>

<!-- 自定义间距 -->
<dedsi-space :size="30">
  <span>30px 间距</span>
  <span>30px 间距</span>
</dedsi-space>

<!-- 水平和垂直间距 -->
<dedsi-space :size="[16, 8]" wrap>
  <dedsi-button>按钮1</dedsi-button>
  <dedsi-button>按钮2</dedsi-button>
  <dedsi-button>按钮3</dedsi-button>
  <dedsi-button>按钮4</dedsi-button>
</dedsi-space>

<!-- 对齐方式 -->
<dedsi-space align="center">
  <div style="height: 40px">元素1</div>
  <div style="height: 60px">元素2</div>
  <div style="height: 40px">元素3</div>
</dedsi-space>

<!-- 表格操作列 -->
<dedsi-table :data="data" :columns="columns">
  <template #actions="{ row }">
    <dedsi-space>
      <dedsi-button type="link" @click="view(row)">查看</dedsi-button>
      <dedsi-button type="link" @click="edit(row)">编辑</dedsi-button>
      <dedsi-popconfirm title="确定删除?" @confirm="del(row)">
        <dedsi-button type="link" danger>删除</dedsi-button>
      </dedsi-popconfirm>
    </dedsi-space>
  </template>
</dedsi-table>
```

### 间距尺寸预设

| 值 | 水平间距 | 垂直间距 |
|----|----------|----------|
| small | 8px | 8px |
| middle | 16px | 16px |
| large | 24px | 24px |

### 类型定义

```typescript
type SpaceSize = 'small' | 'middle' | 'large' | number | [number, number]

interface Props {
  direction?: 'horizontal' | 'vertical'
  size?: SpaceSize
  wrap?: boolean
  align?: 'start' | 'end' | 'center' | 'baseline'
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTable 表格](./component-table.md)
