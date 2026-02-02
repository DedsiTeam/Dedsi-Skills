# 命名约定

## Rule

遵循统一的命名约定，提高代码可读性和一致性。

## Impact

MEDIUM

## Problem

不一致的命名导致理解困难和维护成本增加。

## Solution

### 实体命名

- 聚合根：使用业务领域单数名词（如 `User`, `Order`, `Risk`）
- 子实体：使用业务领域单数名词（如 `OrderItem`, `RiskAttachment`）

### DbSet 命名

- 使用聚合根的复数形式（如 `Users`, `Orders`, `Risks`）

### DTO 命名

| 类型 | 命名模式 | 示例 |
|------|---------|------|
| 展示 DTO | `[Domain]Dto` | `UserDto` |
| 创建/更新 DTO | `[Domain]CreateUpdateDto` | `UserCreateUpdateDto` |
| 分页输入 DTO | `[Domain]PagedInputDto` | `UserPagedInputDto` |
| 分页行 DTO | `[Domain]PagedRowDto` | `UserPagedRowDto` |
| 分页结果 DTO | `[Domain]PagedResultDto` | `UserPagedResultDto` |

### Command 命名

- Create: `Create[Domain]Command`
- Update: `Update[Domain]Command`
- Delete: `Delete[Domain]Command`

### Handler 命名

- Create: `Create[Domain]CommandHandler`
- Update: `Update[Domain]CommandHandler`
- Delete: `Delete[Domain]CommandHandler`

### Query 命名

- 单项查询：`[Domain]Query`
- 分页查询：`[Domain]PagedQuery`

### Repository 命名

- 接口：`I[Domain]Repository`
- 实现：`[Domain]Repository`

### Controller 命名

- 控制器：`[Domain]Controller`
- Request 对象（推荐）：`Create[Domain]Request`, `Update[Domain]Request`

### DbContext 命名

- 接口：`I[Project]DbContext`
- 实现：`[Project]DbContext`

### EntityConfiguration 命名

- 配置类：`[Domain]Configuration`

### 方法命名

- 改变属性：`Change+PropertyName`（如 `ChangeName`, `ChangeStatus`）
- 添加元素：`Add+ItemName`（如 `AddChild`, `AddAttachment`）
- 移除元素：`Remove+ItemName`（如 `RemoveChild`, `RemoveAttachment`）
- 清空集合：`Clear+CollectionName`（如 `ClearChildren`, `ClearAttachments`）
- 查询方法：`GetAsync`, `PagedQueryAsync`
- 创建方法：`CreateAsync`
- 更新方法：`UpdateAsync`
- 删除方法：`DeleteAsync`

## Related

- [file-organization.md](./file-organization.md)
