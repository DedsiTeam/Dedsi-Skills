# 代码模板

## Rule

使用标准代码模板提高开发效率，确保代码一致性。

## Impact

MEDIUM

## Problem

重复编写相似代码导致效率低下和不一致。

## Solution

### API Service 模板

```typescript
// src/apiServices/roleService.ts
import { AxiosRequest } from '../../utils/axiosRequest'

// DTO 类型定义
export interface IRolePagedInputDto {
  name?: string
  isSystem?: boolean
  isDefault?: boolean
  pageIndex: number
  pageSize: number
}

export interface IRoleDto {
  id: string
  name: string
  description?: string
  isSystem: boolean
  isDefault: boolean
  permissions?: IPermissionDto[]
  creatorName?: string
  creationTime: string
}

export interface IRoleCreateUpdateDto {
  name: string
  description?: string
  isSystem: boolean
  isDefault: boolean
  permissions?: IPermissionDto[]
}

export interface IRolePagedRowDto {
  id: string
  name: string
  description?: string
  isSystem: boolean
  isDefault: boolean
  creatorName?: string
  creationTime: string
}

export interface IRolePagedResultDto {
  totalCount: number
  items: IRolePagedRowDto[]
}

// Service 类定义
export class RoleApiService {
  private request = new AxiosRequest(IdentityApiServiceUrl)

  public pagedQuery(data: IRolePagedInputDto) {
    return this.request.post<IRolePagedResultDto, IRolePagedInputDto>(
      '/api/Identity/Role/PagedQuery',
      data
    )
  }

  public get(id: string) {
    return this.request.get<IRoleDto>(`/api/Identity/Role/${id}`)
  }

  public create(data: IRoleCreateUpdateDto) {
    return this.request.post<string, { role: IRoleCreateUpdateDto }>(
      '/api/Identity/Role',
      { role: data }
    )
  }

  public update(id: string, data: IRoleCreateUpdateDto) {
    return this.request.post<boolean, { id: string, role: IRoleCreateUpdateDto }>(
      `/api/Identity/Role/${id}`,
      { id, role: data }
    )
  }

  public delete(id: string) {
    return this.request.delete<boolean>(`/api/Identity/Role/${id}`)
  }
}

// 导出单例
export const roleApiService = new RoleApiService()
```

### View 组件完整模板

```vue
<template>
  <section class="role-page">
    <!-- 搜索栏 -->
    <dedsi-card :bordered="false" class="search-card">
      <div class="flex-between">
        <dedsi-form layout="inline" :model="queryParam">
          <dedsi-form-item label="角色名称">
            <dedsi-input v-model:value="queryParam.name" placeholder="请输入角色名称" />
          </dedsi-form-item>
          <dedsi-form-item label="是否系统角色">
            <dedsi-select
              v-model:value="queryParam.isSystem"
              placeholder="请选择"
              :options="yesNoOptions"
              allow-clear
            />
          </dedsi-form-item>
          <dedsi-form-item label="是否默认角色">
            <dedsi-select
              v-model:value="queryParam.isDefault"
              placeholder="请选择"
              :options="yesNoOptions"
              allow-clear
            />
          </dedsi-form-item>
          <dedsi-form-item>
            <dedsi-space>
              <dedsi-button type="primary" @click="handleSearch">
                <template #icon><SearchOutlined /></template>
                查询
              </dedsi-button>
              <dedsi-button @click="handleReset">
                <template #icon><ReloadOutlined /></template>
                重置
              </dedsi-button>
            </dedsi-space>
          </dedsi-form-item>
        </dedsi-form>

        <dedsi-button type="primary" @click="handleCreate">
          <template #icon><PlusOutlined /></template>
          新建角色
        </dedsi-button>
      </div>
    </dedsi-card>

    <!-- 数据表格 -->
    <dedsi-card :bordered="false" class="table-card">
      <dedsi-table
        :columns="columns"
        :data="tableData"
        :total="pagination.total"
        :loading="tableLoading"
        @page-change="handlePageChange"
      >
        <template #id="{ row }">
          <dedsi-typography :copyable="true" :content="row.id || ''">
            {{ row.id || '-' }}
          </dedsi-typography>
        </template>

        <template #name="{ row }">
          <dedsi-typography :copyable="true" :content="row.name || ''">
            {{ row.name || '-' }}
          </dedsi-typography>
        </template>

        <template #isSystem="{ row }">
          <dedsi-tag type="primary" v-if="row.isSystem">是</dedsi-tag>
          <dedsi-tag v-else>否</dedsi-tag>
        </template>

        <template #isDefault="{ row }">
          <dedsi-tag type="success" v-if="row.isDefault">是</dedsi-tag>
          <dedsi-tag v-else>否</dedsi-tag>
        </template>

        <template #creationTime="{ row }">
          {{ row.creationTime ? dayjs(row.creationTime).format('YYYY-MM-DD HH:mm:ss') : '-' }}
        </template>

        <template #actions="{ row }">
          <dedsi-space>
            <dedsi-button type="link" size="small" @click="handleView(row)">查看</dedsi-button>
            <dedsi-button type="link" size="small" @click="handleEdit(row)">编辑</dedsi-button>
            <dedsi-popconfirm title="确定要删除该角色吗?" @confirm="handleDelete(row)">
              <dedsi-button type="link" size="small" danger>删除</dedsi-button>
            </dedsi-popconfirm>
          </dedsi-space>
        </template>
      </dedsi-table>
    </dedsi-card>

    <!-- 新增/编辑弹窗 -->
    <dedsi-modal
      v-model:visible="modalVisible"
      :title="modalTitle"
      width="600px"
    >
      <dedsi-form ref="formRef" :model="formData" :rules="formRules" layout="vertical">
        <dedsi-form-item label="角色名称" name="name">
          <dedsi-input v-model:value="formData.name" placeholder="请输入角色名称" />
        </dedsi-form-item>
        <dedsi-form-item label="描述" name="description">
          <dedsi-input v-model:value="formData.description" type="textarea" placeholder="请输入描述" />
        </dedsi-form-item>
        <dedsi-form-item label="是否系统角色" name="isSystem">
          <dedsi-switch v-model:checked="formData.isSystem" />
        </dedsi-form-item>
        <dedsi-form-item label="是否默认角色" name="isDefault">
          <dedsi-switch v-model:checked="formData.isDefault" />
        </dedsi-form-item>
        <dedsi-form-item>
          <dedsi-space>
            <dedsi-button type="primary" @click="handleModalConfirm">保存</dedsi-button>
            <dedsi-button @click="handleModalCancel">取消</dedsi-button>
          </dedsi-space>
        </dedsi-form-item>
      </dedsi-form>
    </dedsi-modal>

    <!-- 查看详情弹窗 -->
    <dedsi-modal
      v-model:visible="viewModalVisible"
      title="查看角色详情"
      width="600px"
    >
      <dedsi-form layout="vertical" v-if="viewData">
        <dedsi-form-item label="角色名称">
          <dedsi-typography :copyable="true" :content="viewData.name || ''">
            {{ viewData.name || '-' }}
          </dedsi-typography>
        </dedsi-form-item>
        <dedsi-form-item label="描述">
          {{ viewData.description || '-' }}
        </dedsi-form-item>
        <dedsi-form-item label="是否系统角色">
          <dedsi-tag type="primary" v-if="viewData.isSystem">是</dedsi-tag>
          <dedsi-tag v-else>否</dedsi-tag>
        </dedsi-form-item>
        <dedsi-form-item label="是否默认角色">
          <dedsi-tag type="success" v-if="viewData.isDefault">是</dedsi-tag>
          <dedsi-tag v-else>否</dedsi-tag>
        </dedsi-form-item>
        <dedsi-form-item label="创建人">
          {{ viewData.creatorName || '-' }}
        </dedsi-form-item>
        <dedsi-form-item label="创建时间">
          {{ viewData.creationTime ? dayjs(viewData.creationTime).format('YYYY-MM-DD HH:mm:ss') : '-' }}
        </dedsi-form-item>
        <dedsi-form-item>
          <div style="text-align: right; width: 100%">
            <dedsi-button @click="viewModalVisible = false">关闭</dedsi-button>
          </div>
        </dedsi-form-item>
      </dedsi-form>
    </dedsi-modal>
  </section>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
import { SearchOutlined, ReloadOutlined, PlusOutlined } from '@ant-design/icons-vue'
import dayjs from 'dayjs'
import { roleApiService } from '@/apiServices'
import type { IRolePagedRowDto, IRoleCreateUpdateDto, IRoleDto } from '@/apiServices'
import { DedsiMessage } from 'dedsi-vue-ui'

// 表格列定义
const columns = [
  { title: 'id', slot: 'id', key: 'id' },
  { title: '角色名称', slot: 'name', key: 'name' },
  { title: '描述', dataIndex: 'description', key: 'description' },
  { title: '是否系统角色', slot: 'isSystem', key: 'isSystem', width: 120 },
  { title: '是否默认角色', slot: 'isDefault', key: 'isDefault', width: 120 },
  { title: '创建人', dataIndex: 'creatorName', key: 'creatorName' },
  { title: '创建时间', slot: 'creationTime', key: 'creationTime', width: 180 },
  { title: '操作', slot: 'actions', key: 'actions', width: 200 }
]

// 表格数据
const tableData = ref<IRolePagedRowDto[]>([])
const tableLoading = ref(false)

// 分页配置
const pagination = reactive({
  current: 1,
  pageSize: 10,
  total: 0,
  showSizeChanger: true,
  showTotal: (total: number) => `共 ${total} 条`
})

// 查询参数
const queryParam = reactive({
  name: '',
  isSystem: undefined as boolean | undefined,
  isDefault: undefined as boolean | undefined
})

// 是/否选项
const yesNoOptions = [
  { label: '是', value: true },
  { label: '否', value: false }
]

// 弹窗相关
const modalVisible = ref(false)
const modalTitle = ref('')
const isEdit = ref(false)
const currentId = ref('')

// 查看详情弹窗相关
const viewModalVisible = ref(false)
const viewData = ref<IRoleDto | null>(null)

// 表单数据
const formData = reactive<IRoleCreateUpdateDto>({
  name: '',
  description: '',
  isSystem: false,
  isDefault: false,
  permissions: []
})

const formRef = ref()

// 表单验证规则
const formRules = {
  name: [{ required: true, message: '请输入角色名称', trigger: 'blur' }],
  description: [{ required: false, message: '请输入描述', trigger: 'blur' }]
}

// 加载数据
const fetchData = async () => {
  tableLoading.value = true
  try {
    const result = await roleApiService.pagedQuery({
      ...queryParam,
      pageIndex: pagination.current,
      pageSize: pagination.pageSize
    })
    tableData.value = result.items
    pagination.total = result.totalCount
  } catch (error) {
    DedsiMessage.error('加载数据失败')
    console.error(error)
  } finally {
    tableLoading.value = false
  }
}

// 分页改变
const handlePageChange = (page: number, pageSize: number) => {
  pagination.current = page
  pagination.pageSize = pageSize
  fetchData()
}

// 查询
const handleSearch = () => {
  pagination.current = 1
  fetchData()
}

// 重置
const handleReset = () => {
  Object.assign(queryParam, {
    name: '',
    isSystem: undefined,
    isDefault: undefined
  })
  pagination.current = 1
  fetchData()
}

// 重置表单数据
const resetFormData = () => {
  Object.assign(formData, {
    name: '',
    description: '',
    isSystem: false,
    isDefault: false,
    permissions: []
  })
}

// 新增
const handleCreate = () => {
  isEdit.value = false
  modalTitle.value = '新建角色'
  resetFormData()
  modalVisible.value = true
}

// 查看
const handleView = async (row: IRolePagedRowDto) => {
  try {
    const detail: IRoleDto = await roleApiService.get(row.id || '')
    viewData.value = detail
    viewModalVisible.value = true
  } catch (error) {
    DedsiMessage.error('获取详情失败')
  }
}

// 编辑
const handleEdit = async (row: IRolePagedRowDto) => {
  try {
    const detail: IRoleDto = await roleApiService.get(row.id || '')
    isEdit.value = true
    modalTitle.value = '编辑角色'
    currentId.value = row.id || ''
    Object.assign(formData, {
      name: detail.name,
      description: detail.description,
      isSystem: detail.isSystem,
      isDefault: detail.isDefault,
      permissions: detail.permissions || []
    })
    modalVisible.value = true
  } catch (error) {
    DedsiMessage.error('获取详情失败')
  }
}

// 删除
const handleDelete = async (row: IRolePagedRowDto) => {
  try {
    await roleApiService.delete(row.id || '')
    DedsiMessage.success('删除成功')
    fetchData()
  } catch (error) {
    DedsiMessage.error('删除失败')
    console.error(error)
  }
}

// 弹窗确认
const handleModalConfirm = async () => {
  try {
    await formRef.value?.validate()
    const requestData: IRoleCreateUpdateDto = {
      name: formData.name,
      description: formData.description,
      isSystem: formData.isSystem,
      isDefault: formData.isDefault,
      permissions: formData.permissions
    }

    if (isEdit.value) {
      await roleApiService.update(currentId.value, { role: requestData })
      DedsiMessage.success('更新成功')
    } else {
      await roleApiService.create({ role: requestData })
      DedsiMessage.success('创建成功')
    }

    modalVisible.value = false
    resetFormData()
    fetchData()
  } catch (error) {
    if (error && typeof error === 'object') {
      DedsiMessage.error(isEdit.value ? '更新失败' : '创建失败')
    }
  }
}

// 弹窗取消
const handleModalCancel = () => {
  modalVisible.value = false
  formRef.value?.clearValidate()
  resetFormData()
}

// 初始化
onMounted(() => {
  fetchData()
})
</script>

<style scoped>
.role-page {
  padding: 0;
}
.search-card {
  margin-bottom: 16px;
}
</style>
```

## Related

- [API Service 模式](./api-service-patterns.md)
- [View 组件模式](./view-component-patterns.md)
