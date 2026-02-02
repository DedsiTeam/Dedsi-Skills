# 常见问题和验收清单

## 常见错误

### 1. Update 端点 Body 误用展示 DTO

**错误**：
```csharp
[HttpPost("{id}")]
public Task<bool> UpdateAsync([FromRoute] string id, [FromBody] [Domain]Dto dto) // 错误
```

**正确**：
```csharp
[HttpPost("{id}")]
public Task<bool> UpdateAsync([FromRoute] string id, [FromBody] Update[Domain]Request request)
    => dedsiMediator.SendAsync(new Update[Domain]Command(id, request.[Domain]), HttpContext.RequestAborted);
```

### 2. Query 忘记 AsNoTracking()

**错误**：
```csharp
var query = dbContext.Entities.Where(e => e.Status == status); // 错误
```

**正确**：
```csharp
var query = dbContext.Entities
    .AsNoTracking()
    .Where(e => e.Status == status);
```

### 3. 分页查询顺序错误

**错误**：
```csharp
var query = dbContext.Entities.AsNoTracking();
var items = await query.Skip((input.PageIndex - 1) * input.PageSize)
    .Take(input.PageSize)
    .ToListAsync(cancellationToken);
var totalCount = await query.CountAsync(cancellationToken); // 错误：Count 太晚
```

**正确**：
```csharp
var query = dbContext.Entities.AsNoTracking();
var totalCount = await query.CountAsync(cancellationToken);
query = query.Skip((input.PageIndex - 1) * input.PageSize)
    .Take(input.PageSize);
var items = await query.ToListAsync(cancellationToken);
```

### 4. CancellationToken 断链

**错误**：
```csharp
public override async Task<string> Handle(Create[Domain]Command command, CancellationToken cancellationToken)
{
    await repository.InsertAsync(domain, autoSave: true); // 错误：未传递 cancellationToken
}
```

**正确**：
```csharp
public override async Task<string> Handle(Create[Domain]Command command, CancellationToken cancellationToken)
{
    await repository.InsertAsync(domain, autoSave: true, cancellationToken);
}
```

### 5. enum 未显式赋值

**错误**：
```csharp
public enum Status
{
    Active,      // 错误：未显式赋值
    Inactive,
    Deleted
}
```

**正确**：
```csharp
public enum Status
{
    Active = 1,
    Inactive = 2,
    Deleted = 3
}
```

## 验收清单

### 1. 文件组织

- [ ] Command/Handler 在同一文件
- [ ] Query 接口/实现在同一文件
- [ ] Repository 接口/实现在同一文件
- [ ] Create/Update/Delete Command 分别在不同文件

### 2. 依赖方向

- [ ] Domain 不依赖任何其他层
- [ ] Infrastructure 只依赖 Domain
- [ ] UseCase 依赖 Domain 和 Infrastructure
- [ ] HttpApi 只依赖 UseCase

### 3. CancellationToken

- [ ] Controller 使用 `HttpContext.RequestAborted`
- [ ] Command Handler 接收并透传 `CancellationToken`
- [ ] Query Handler 接收并透传 `CancellationToken`
- [ ] 所有 EF Core 操作传递 `CancellationToken`
- [ ] 所有 Repository 调用传递 `CancellationToken`

### 4. 返回类型

- [ ] Create 返回类型与 Command 一致（通常是 `string`）
- [ ] Update 返回类型与 Command 一致（通常是 `bool`）
- [ ] Delete 返回类型与 Command 一致（通常是 `bool`）
- [ ] Controller 返回类型与 Query/Command 一致

### 5. Query 规范

- [ ] 分页查询使用 `AsNoTracking()`
- [ ] 分页查询顺序：筛选 → Count → 排序 → 分页 → 投影 → ToList
- [ ] `id` 参数用于查询谓词
- [ ] 导出时不分页

### 6. Controller 规范

- [ ] Update Body 使用 `CreateUpdateDto`
- [ ] Update/Delete 路由不冲突
- [ ] 继承项目基础控制器
- [ ] 使用 `HttpContext.RequestAborted`

### 7. 领域模型

- [ ] 聚合根继承 `DedsiAggregateRoot<string>`
- [ ] 包含 `protected` 无参构造函数
- [ ] 属性使用 `private set`
- [ ] 状态变更通过方法
- [ ] 入参校验使用 `Check.NotNullOrWhiteSpace` 或 `Check.NotNull`

### 8. EF Core 映射

- [ ] DbSet 使用聚合根复数形式
- [ ] 配置类实现 `IEntityTypeConfiguration<T>`
- [ ] 必填属性使用 `IsRequired()`
- [ ] 字符串属性使用 `HasMaxLength()`

### 9. DTO

- [ ] 使用 `public get; set;`
- [ ] 每个属性有 XML 注释
- [ ] 不直接暴露领域对象类型
- [ ] 枚举 DTO 有完整的值说明

### 10. 枚举

- [ ] 所有枚举显式赋值
- [ ] 第一个业务值从 `1` 开始
- [ ] 领域模型验证枚举值合法性

## Related

- [聚合根模式](./aggregate-root-patterns.md)
- [Query Handler](./query-handlers.md)
- [Controller 模式](./controller-patterns.md)
