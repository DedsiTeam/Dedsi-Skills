# API Service 模式

## Rule

定义 API 服务类遵循统一约定，使用类封装 HTTP 请求。

## Impact

HIGH

## Problem

不一致的 API 服务实现导致代码重复、维护困难和类型安全问题。

## Solution

### 服务类定义

使用 `class` 定义服务，内部实例化 `AxiosRequest`：

```typescript
export class UserApiService {
    private request = new AxiosRequest(IdentityApiServiceUrl)

    public pagedQuery(data: UserPagedInputDto) {
        return this.request.post<UserPagedResultDto, UserPagedInputDto>('/api/Identity/User/PagedQuery', data)
    }
}
```

### 单例导出

导出服务实例而不是类：

```typescript
export const userApiService = new UserApiService()
```

### 标准方法

所有 API 服务应实现以下标准方法：

| 方法名 | 用途 | 类型 |
|--------|------|------|
| `pagedQuery` | 分页查询 | `(data: InputDto) => Promise<PagedResultDto>` |
| `get` | 获取详情 | `(id: string) => Promise<DetailDto>` |
| `create` | 创建 | `(data: CreateDto) => Promise<string>` |
| `update` | 更新 | `(id: string, data: UpdateDto) => Promise<boolean>` |
| `delete` | 删除 | `(id: string) => Promise<boolean>` |

### 完整示例

```typescript
import { AxiosRequest } from '../../utils/axiosRequest'

export interface UserPagedInputDto {
    keyword?: string
    pageIndex: number
    pageSize: number
}

export interface UserPagedResultDto {
    totalCount: number
    items: UserDto[]
}

export interface UserCreateUpdateDto {
    name: string
    email: string
}

export class UserApiService {
    private request = new AxiosRequest(IdentityApiServiceUrl)

    public pagedQuery(data: UserPagedInputDto) {
        return this.request.post<UserPagedResultDto, UserPagedInputDto>('/api/Identity/User/PagedQuery', data)
    }

    public get(id: string) {
        return this.request.get<UserDto>(`/api/Identity/User/${id}`)
    }

    public create(data: UserCreateUpdateDto) {
        return this.request.post<string, UserCreateUpdateDto>('/api/Identity/User', data)
    }

    public update(id: string, data: UserCreateUpdateDto) {
        return this.request.post<boolean, { id: string, data: UserCreateUpdateDto }>(
            `/api/Identity/User/${id}`,
            { id, data }
        )
    }

    public delete(id: string) {
        return this.request.delete<boolean>(`/api/Identity/User/${id}`)
    }
}

export const userApiService = new UserApiService()
```

### 目录结构

```
src/apiServices/
├── identitys/
│   ├── index.ts          # 导出所有服务
│   ├── types.ts         # DTO 类型定义
│   └── userService.ts   # 用户服务实现
└── index.ts             # 统一导出
```

## Related

- [状态管理](./state-management.md)
- [View 组件模式](./view-component-patterns.md)
