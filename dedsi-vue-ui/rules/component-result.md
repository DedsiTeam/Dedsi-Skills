# DedsiResult 结果

## Rule

使用 DedsiResult 组件反馈各类操作结果。

## Impact

LOW

## Problem

不使用 Result 组件会导致结果反馈展示不统一。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| status | 'success' \| 'error' \| 'info' \| 'warning' \| '404' \| '403' \| '500' | - | 否 | 结果状态 |
| title | string | - | 否 | 标题 |
| subTitle | string | - | 否 | 副标题 |
| icon | string | - | 否 | 自定义图标组件 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| default | 额外内容 |
| icon | 自定义图标 |
| title | 自定义标题 |
| subTitle | 自定义副标题 |
| extra | 底部操作区 |

### 使用示例

```vue
<!-- 成功结果 -->
<dedsi-result status="success" title="操作成功" subTitle="预计2小时内到达">
  <template #extra>
    <dedsi-button type="primary">返回首页</dedsi-button>
  </template>
</dedsi-result>

<!-- 错误结果 -->
<dedsi-result status="error" title="提交失败" subTitle="请检查后重试">
  <template #extra>
    <dedsi-button @click="handleRetry">重试</dedsi-button>
  </template>
</dedsi-result>

<!-- 404 页面 -->
<dedsi-result status="404" title="404" subTitle="抱歉，您访问的页面不存在">
  <template #extra>
    <dedsi-button type="primary">返回首页</dedsi-button>
  </template>
</dedsi-result>

<!-- 403 页面 -->
<dedsi-result status="403" title="403" subTitle="抱歉，您无权访问该页面">
  <template #extra>
    <dedsi-button type="primary">返回首页</dedsi-button>
  </template>
</dedsi-result>

<!-- 500 页面 -->
<dedsi-result status="500" title="500" subTitle="抱歉，服务器出错了">
  <template #extra>
    <dedsi-button type="primary">返回首页</dedsi-button>
  </template>
</dedsi-result>

<!-- 自定义内容 -->
<dedsi-result status="info" title="提示" subTitle="这是一条提示信息">
  <p>额外的说明内容</p>
  <template #extra>
    <dedsi-button>知道了</dedsi-button>
  </template>
</dedsi-result>
```

### 状态说明

| 状态 | 图标 | 适用场景 |
|------|------|----------|
| success | 成功图标 | 操作成功 |
| error | 错误图标 | 操作失败 |
| info | 信息图标 | 一般提示 |
| warning | 警告图标 | 警告提示 |
| 404 | 404图标 | 页面不存在 |
| 403 | 403图标 | 无权限访问 |
| 500 | 500图标 | 服务器错误 |

### 类型定义

```typescript
type ResultStatus =
  | 'success'
  | 'error'
  | 'info'
  | 'warning'
  | '404'
  | '403'
  | '500'

interface Props {
  status?: ResultStatus
  title?: string
  subTitle?: string
  icon?: string
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiEmpty 空状态](./component-empty.md)
