# 表单处理

## Rule

统一表单数据处理流程，确保新增、编辑和提交的一致性。

## Impact

HIGH

## Problem

不统一的表单处理逻辑导致数据回填错误、状态管理混乱。

## Solution

### 弹窗管理

```typescript
const modalVisible = ref(false)
const isEditMode = ref(false)
const editId = ref<string>()
```

### 表单状态

```typescript
const formState = reactive<UserCreateUpdateDto>({
  name: '',
  description: '',
  isSystem: false,
  isDefault: false,
  permissionIds: []
})
```

### 打开新增弹窗

```typescript
const handleCreate = () => {
  isEditMode.value = false
  editId.value = undefined
  Object.assign(formState, {
    name: '',
    description: '',
    isSystem: false,
    isDefault: false,
    permissionIds: []
  })
  modalVisible.value = true
}
```

### 打开编辑弹窗

```typescript
const handleEdit = async (record: UserDto) => {
  try {
    isEditMode.value = true
    editId.value = record.id
    // 回填数据
    const detail = await userApiService.get(record.id)
    Object.assign(formState, {
      name: detail.name,
      email: detail.email
    })
    modalVisible.value = true
  } catch (error) {
    DedsiMessage.error('获取详情失败')
  }
}
```

### 提交表单

```typescript
const handleSubmit = async () => {
  try {
    if (isEditMode.value && editId.value) {
      // 更新
      await userApiService.update(editId.value, formState)
      DedsiMessage.success('更新成功')
    } else {
      // 新增
      await userApiService.create(formState)
      DedsiMessage.success('创建成功')
    }
    modalVisible.value = false
    fetchData()
  } catch (error) {
    DedsiMessage.error('操作失败')
  }
}
```

### 取消弹窗

```typescript
const handleModalCancel = () => {
  modalVisible.value = false
  // 可选：重置表单
}
```

### 表单组件

#### 文本输入

```vue
<dedsi-form-item label="角色名称" name="name">
  <dedsi-input v-model:value="formState.name" placeholder="请输入角色名称" />
</dedsi-form-item>
```

#### 文本域

```vue
<dedsi-form-item label="描述" name="description">
  <dedsi-input
    v-model:value="formState.description"
    type="textarea"
    placeholder="请输入描述"
  />
</dedsi-form-item>
```

#### 开关（Switch）

```vue
<dedsi-form-item label="是否系统角色" name="isSystem">
  <dedsi-switch v-model:checked="formState.isSystem" />
</dedsi-form-item>

<dedsi-form-item label="是否默认角色" name="isDefault">
  <dedsi-switch v-model:checked="formState.isDefault" />
</dedsi-form-item>
```

#### 单选下拉框

```vue
<dedsi-form-item label="状态" name="status">
  <dedsi-select
    v-model:value="formState.status"
    placeholder="请选择状态"
    :options="statusOptions"
    allow-clear
  />
</dedsi-form-item>
```

#### 多选下拉框

```vue
<dedsi-form-item label="权限" name="permissionIds">
  <dedsi-select
    v-model:value="formState.permissionIds"
    mode="multiple"
    placeholder="请选择权限"
    :options="permissionOptions"
  />
</dedsi-form-item>
```

### 完整示例

```typescript
<script setup lang="ts">
import { reactive, ref } from 'vue'
import { userApiService } from '@/apiServices'
import { DedsiMessage } from 'dedsi-vue-ui'

const modalVisible = ref(false)
const isEditMode = ref(false)
const editId = ref<string>()

const formState = reactive<UserCreateUpdateDto>({
  name: '',
  email: ''
})

const handleAdd = () => {
  isEditMode.value = false
  editId.value = undefined
  Object.assign(formState, { name: '', email: '' })
  modalVisible.value = true
}

const handleEdit = async (record: UserDto) => {
  try {
    isEditMode.value = true
    editId.value = record.id
    const detail = await userApiService.get(record.id)
    Object.assign(formState, {
      name: detail.name,
      email: detail.email
    })
    modalVisible.value = true
  } catch (error) {
    DedsiMessage.error('获取详情失败')
  }
}

const handleSubmit = async () => {
  try {
    if (isEditMode.value && editId.value) {
      await userApiService.update(editId.value, formState)
      DedsiMessage.success('更新成功')
    } else {
      await userApiService.create(formState)
      DedsiMessage.success('创建成功')
    }
    modalVisible.value = false
    fetchData()
  } catch (error) {
    DedsiMessage.error('操作失败')
  }
}

const handleModalCancel = () => {
  modalVisible.value = false
}
</script>
```

## Related

- [View 组件模式](./view-component-patterns.md)
- [组件映射](./component-mapping.md)
