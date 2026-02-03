# DedsiCard 卡片

## Rule

使用 DedsiCard 组件作为通用卡片容器，支持标题、额外操作、内容和底部。

## Impact

MEDIUM

## Problem

不使用 Card 组件会导致容器样式不一致，缺少统一的边框和阴影效果。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| title | string | - | 否 | 卡片标题 |
| bordered | boolean | true | 否 | 是否有边框 |
| hoverable | boolean | false | 否 | 鼠标悬停时是否可浮起 |
| noShadow | boolean | true | 否 | 是否无阴影 |
| shadow | 'always' \| 'hover' \| 'never' | 'hover' | 否 | 阴影显示时机 |
| bodyStyle | Record<string, any> | - | 否 | 内容区域自定义样式对象 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 卡片内容 |
| title | 自定义标题 |
| extra | 标题旁的额外操作区 |
| footer | 底部内容 |

### 使用示例

```vue
<!-- 基础卡片 -->
<dedsi-card title="卡片标题">
  <p>卡片内容</p>
</dedsi-card>

<!-- 无边框 -->
<dedsi-card :bordered="false" title="无边框卡片">
  <p>无边框的卡片</p>
</dedsi-card>

<!-- 可悬浮 -->
<dedsi-card hoverable title="可悬浮卡片">
  <p>鼠标悬停时会有阴影效果</p>
</dedsi-card>

<!-- 自定义内容样式 -->
<dedsi-card title="自定义样式" :bodyStyle="{ padding: '20px' }">
  <p>自定义内边距的内容</p>
</dedsi-card>

<!-- 带额外操作 -->
<dedsi-card title="用户列表">
  <template #extra>
    <dedsi-space>
      <dedsi-button type="primary" @click="handleAdd">新增</dedsi-button>
      <dedsi-button @click="handleRefresh">刷新</dedsi-button>
    </dedsi-space>
  </template>
  <dedsi-table :data="data" :columns="columns" />
</dedsi-card>

<!-- 自定义标题 -->
<dedsi-card>
  <template #title>
    <dedsi-space>
      <UserOutlined />
      <span>自定义标题</span>
    </dedsi-space>
  </template>
  <p>卡片内容</p>
</dedsi-card>

<!-- 带底部 -->
<dedsi-card title="统计信息">
  <dedsi-statistic title="总用户数" :value="12345" />
  <template #footer>
    <dedsi-space style="width: 100%; justify-content: flex-end">
      <dedsi-button>查看详情</dedsi-button>
    </dedsi-space>
  </template>
</dedsi-card>

<!-- 搜索卡片 -->
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
```

### 类型定义

```typescript
interface Props {
  title?: string
  bordered?: boolean
  hoverable?: boolean
  noShadow?: boolean
  shadow?: 'always' | 'hover' | 'never'
  bodyStyle?: Record<string, any>
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiTable 表格](./component-table.md)
- [DedsiStatistic 统计数值](./component-statistic.md)
