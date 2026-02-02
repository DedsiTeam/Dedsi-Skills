# 表单验证

## Rule

使用表单验证规则确保数据完整性。

## Impact

HIGH

## Problem

缺少表单验证导致用户提交无效数据。

## Solution

### 表单引用

```typescript
const formRef = ref()
```

```vue
<dedsi-form ref="formRef" :model="formData" :rules="formRules" layout="vertical">
  <!-- 表单项 -->
</dedsi-form>
```

### 验证规则定义

```typescript
const formRules = {
  name: [
    { required: true, message: '请输入角色名称', trigger: 'blur' }
  ],
  description: [
    { required: false, message: '请输入描述', trigger: 'blur' }
  ],
  email: [
    { required: true, message: '请输入邮箱', trigger: 'blur' },
    { type: 'email', message: '邮箱格式不正确', trigger: 'blur' }
  ]
}
```

### 表单项绑定规则

```vue
<dedsi-form-item label="角色名称" name="name">
  <dedsi-input v-model:value="formData.name" placeholder="请输入角色名称" />
</dedsi-form-item>

<dedsi-form-item label="邮箱" name="email">
  <dedsi-input v-model:value="formData.email" placeholder="请输入邮箱" />
</dedsi-form-item>
```

### 常用验证规则

| 规则类型 | 示例 | 说明 |
|---------|------|------|
| 必填 | `{ required: true, message: '必填' }` | 字段必须填写 |
| 邮箱 | `{ type: 'email', message: '邮箱格式错误' }` | 验证邮箱格式 |
| URL | `{ type: 'url', message: 'URL格式错误' }` | 验证 URL 格式 |
| 数字 | `{ type: 'number', message: '必须是数字' }` | 验证数字 |
| 长度 | `{ min: 6, max: 20, message: '长度在 6-20 之间' }` | 字符串长度限制 |
| 正则 | `{ pattern: /^[A-Za-z]+$/, message: '只能包含字母' }` | 自定义正则验证 |
| 自定义 | `{ validator: validateFn, trigger: 'blur' }` | 自定义验证函数 |

### 自定义验证函数

```typescript
const validatePhone = (rule: any, value: string) => {
  if (!value) {
    return Promise.resolve()
  }
  if (!/^1[3-9]\d{9}$/.test(value)) {
    return Promise.reject('手机号格式不正确')
  }
  return Promise.resolve()
}

const formRules = {
  phone: [
    { required: true, message: '请输入手机号', trigger: 'blur' },
    { validator: validatePhone, trigger: 'blur' }
  ]
}
```

### 手动验证

```typescript
const handleModalConfirm = async () => {
  try {
    // 验证表单
    await formRef.value?.validate()
    // 验证通过，继续处理
    handleSubmit()
  } catch (error) {
    // 验证失败，组件会自动显示错误提示
    console.log('表单验证失败:', error)
  }
}
```

### 清除验证

```typescript
const handleModalCancel = () => {
  modalVisible.value = false
  formRef.value?.clearValidate()
  resetFormData()
}
```

### 完整示例

```vue
<template>
  <dedsi-modal v-model:visible="modalVisible" title="编辑" width="600px">
    <dedsi-form ref="formRef" :model="formData" :rules="formRules" layout="vertical">
      <dedsi-form-item label="角色名称" name="name">
        <dedsi-input v-model:value="formData.name" placeholder="请输入角色名称" />
      </dedsi-form-item>

      <dedsi-form-item label="邮箱" name="email">
        <dedsi-input v-model:value="formData.email" placeholder="请输入邮箱" />
      </dedsi-form-item>

      <dedsi-form-item>
        <dedsi-space>
          <dedsi-button type="primary" @click="handleModalConfirm">保存</dedsi-button>
          <dedsi-button @click="handleModalCancel">取消</dedsi-button>
        </dedsi-space>
      </dedsi-form-item>
    </dedsi-form>
  </dedsi-modal>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import { DedsiMessage } from 'dedsi-vue-ui'

const formRef = ref()

const formData = reactive({
  name: '',
  email: ''
})

const formRules = {
  name: [
    { required: true, message: '请输入角色名称', trigger: 'blur' }
  ],
  email: [
    { required: true, message: '请输入邮箱', trigger: 'blur' },
    { type: 'email', message: '邮箱格式不正确', trigger: 'blur' }
  ]
}

const handleModalConfirm = async () => {
  try {
    await formRef.value?.validate()
    // 提交逻辑
    DedsiMessage.success('保存成功')
    modalVisible.value = false
  } catch (error) {
    console.log('表单验证失败:', error)
  }
}

const handleModalCancel = () => {
  modalVisible.value = false
  formRef.value?.clearValidate()
}
</script>
```

## Related

- [表单处理](./form-handling.md)
- [View 组件模式](./view-component-patterns.md)
