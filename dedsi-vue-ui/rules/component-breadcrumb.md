# DedsiBreadcrumb 面包屑

## Rule

使用 DedsiBreadcrumb 组件显示当前页面在系统层级结构中的位置。

## Impact

LOW

## Problem

不使用 Breadcrumb 组件会导致用户无法理解页面层级关系。

## Solution

### DedsiBreadcrumb Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| separator | string | '/' | 否 | 分隔符自定义 |

### DedsiBreadcrumbItem Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| to | string \| object | - | 否 | 路由跳转目标，同 Vue Router |
| replace | boolean | false | 否 | 是否使用 replace 模式跳转 |

### Slots

| 插槽名 | 组件 | 说明 |
|--------|------|------|
| separator | DedsiBreadcrumbItem | 自定义分隔符内容 |
| default | DedsiBreadcrumb | 面包屑项列表 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-breadcrumb>
  <dedsi-breadcrumb-item to="/">首页</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item to="/components">组件</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item>面包屑</dedsi-breadcrumb-item>
</dedsi-breadcrumb>

<!-- 自定义分隔符 -->
<dedsi-breadcrumb separator=">">
  <dedsi-breadcrumb-item to="/">首页</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item to="/components">组件</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item>面包屑</dedsi-breadcrumb-item>
</dedsi-breadcrumb>

<!-- 自定义分隔符图标 -->
<dedsi-breadcrumb>
  <template #separator><RightOutlined /></template>
  <dedsi-breadcrumb-item to="/">首页</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item>当前页面</dedsi-breadcrumb-item>
</dedsi-breadcrumb>

<!-- 路由跳转 -->
<dedsi-breadcrumb>
  <dedsi-breadcrumb-item to="/">首页</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item to="/products">产品</dedsi-breadcrumb-item>
  <dedsi-breadcrumb-item :to="{ name: 'product-detail', params: { id: 1 } }">
    产品详情
  </dedsi-breadcrumb-item>
</dedsi-breadcrumb>
```

## Related

- [组件命名规范](./component-naming.md)
