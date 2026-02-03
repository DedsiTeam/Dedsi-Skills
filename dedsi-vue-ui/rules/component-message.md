# DedsiMessage 全局提示

## Rule

使用 DedsiMessage 组件显示全局提示消息，支持命令式和声明式调用。

## Impact

HIGH

## Problem

不使用 Message 组件会导致用户无法获得及时的操作反馈。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| content | string | 是 | - | 提示内容 |
| type | 'info' \| 'success' \| 'warning' \| 'error' \| 'loading' | 'info' | 否 | 提示类型 |
| duration | number | 3000 | 否 | 持续时间（ms），0 为不自动关闭 |
| onClose | () => void | - | 否 | 关闭回调 |

### 暴露方法

| 方法名 | 说明 |
|--------|------|
| close | 主动关闭提示 |

### 声明式使用

```vue
<!-- 基础用法 -->
<dedsi-message content="这是一条消息" type="info" />
<dedsi-message content="操作成功" type="success" :duration="2000" />
<dedsi-message content="请注意" type="warning" />
<dedsi-message content="操作失败" type="error" />
<dedsi-message content="加载中..." type="loading" />

<!-- 不自动关闭 -->
<dedsi-message content="这条消息不会自动关闭" type="info" :duration="0" />
```

### 命令式使用（推荐）

```vue
<script setup lang="ts">
import { getCurrentInstance } from 'vue'

const instance = getCurrentInstance()

const showMessages = () => {
  // 显示不同类型的消息
  instance?.proxy?.$message.info('普通消息')
  instance?.proxy?.$message.success('成功消息')
  instance?.proxy?.$message.warning('警告消息')
  instance?.proxy?.$message.error('错误消息')
  instance?.proxy?.$message.loading('加载中...')

  // 手动关闭
  const msg = instance?.proxy?.$message.info('不会自动关闭', 0)
  setTimeout(() => {
    msg?.close()
  }, 5000)
}
</script>
```

### 在 API 请求中使用

```typescript
import { getCurrentInstance } from 'vue'

const instance = getCurrentInstance()

const fetchData = async () => {
  const msg = instance?.proxy?.$message.loading('加载中...', 0)
  try {
    const response = await apiService.getData()
    msg?.close()
    instance?.proxy?.$message.success('加载成功')
    return response
  } catch (error) {
    msg?.close()
    instance?.proxy?.$message.error('加载失败')
    throw error
  }
}
```

### 类型定义

```typescript
interface Props {
  content: string
  type?: 'info' | 'success' | 'warning' | 'error' | 'loading'
  duration?: number
  onClose?: () => void
}

interface MessageInstance {
  close(): void
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiAlert 警告提示](./component-alert.md)
