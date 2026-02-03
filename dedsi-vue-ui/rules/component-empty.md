# DedsiEmpty 空状态

## Rule

使用 DedsiEmpty 组件展示空状态时的占位内容。

## Impact

MEDIUM

## Problem

不使用 Empty 组件会导致空数据状态展示不友好。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| image | string | - | 否 | 空状态图片地址 |
| imageSize | number \| string | 120 | 否 | 图片大小 |
| description | string | '暂无数据' | 否 | 描述文本 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 底部内容 |
| image | 自定义图片 |
| description | 自定义描述 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-empty description="暂无数据" />

<!-- 自定义描述 -->
<dedsi-empty description="没有找到相关内容" />

<!-- 自定义图片 -->
<dedsi-empty
  image="https://example.com/empty.png"
  description="暂无数据"
/>

<!-- 自定义大小 -->
<dedsi-empty :imageSize="200" description="暂无数据" />

<!-- 自定义内容 -->
<dedsi-empty description="暂无数据">
  <dedsi-button type="primary">创建</dedsi-button>
</dedsi-empty>

<!-- 完全自定义 -->
<dedsi-empty>
  <template #image>
    <img src="https://example.com/custom.png" style="width: 200px" />
  </template>
  <template #description>
    <span>自定义空状态描述</span>
  </template>
  <dedsi-button>操作</dedsi-button>
</dedsi-empty>
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiResult 结果](./component-result.md)
