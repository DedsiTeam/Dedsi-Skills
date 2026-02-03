# Repository 模式

## Rule

使用 Dedsi Framework 的 Repository 接口实现数据访问。

## Impact

HIGH

## Problem

不正确的 Repository 使用会导致代码不一致或功能缺失。

## Solution

### 选择合适的 Repository 类型

#### 写侧（命令）：CQRS Repository

用于命令操作（增删改）。

**接口定义**：

```csharp
using Dedsi.Ddd.Domain.Repositories;

public interface IMyDomainRepository : IDedsiCqrsRepository<MyDomain, Guid>;
```

**实现**：

```csharp
using Dedsi.EntityFrameworkCore.Repositories;
using Volo.Abp.EntityFrameworkCore;

public class MyDomainRepository(IDbContextProvider<MyDbContext> dbContextProvider)
    : DedsiDddEfCoreRepository<MyDbContext, MyDomain, Guid>(dbContextProvider), IMyDomainRepository;
```

**可用方法**：

- `InsertAsync(TDomain domain, bool autoSave = false)`
- `UpdateAsync(TDomain domain, bool autoSave = false)`
- `DeleteAsync(TDomain domain, bool autoSave = false)`
- `DeleteAsync(Expression<Func<TDomain, bool>> wherePredicate, bool autoSave = false)`
- `GetAsync(Expression<Func<TDomain, bool>> predicate, bool includeDetails = true)`
- `GetAsync(TKey id, bool includeDetails = true)`

#### 读侧（查询）：通用 Repository

用于查询操作和批量操作。

**接口定义**：

```csharp
using Dedsi.Ddd.Domain.Repositories;

public interface IMyEntityRepository : IDedsiRepository<MyEntity, Guid>;
```

**实现**：

```csharp
using Dedsi.EntityFrameworkCore.Repositories;
using Volo.Abp.EntityFrameworkCore;

public class MyEntityRepository(IDbContextProvider<MyDbContext> dbContextProvider)
    : DedsiEfCoreRepository<MyDbContext, MyEntity, Guid>(dbContextProvider), IMyEntityRepository;
```

**可用方法**：

- `GetQueryableNoTrackingAsync()`：无追踪查询
- `GetPagedListAsync<TOrderKey>(wherePredicate, orderPredicate, isReverse = true, pageIndex = 1, pageSize = 10)`：分页查询
- `DeleteManyAsync(wherePredicate, autoSave = false)`：批量删除
- `ExecuteSqlAsync(FormattableString sql)`：执行原生 SQL

### 分页查询示例

```csharp
var (total, items) = await repository.GetPagedListAsync(
    x => x.IsActive,
    x => x.CreatedTime,
    isReverse: true,
    pageIndex: 1,
    pageSize: 10
);
```

### 批量删除示例

```csharp
var count = await repository.DeleteManyAsync(x => x.IsDeleted, autoSave: true);
```

### 执行原生 SQL 示例

```csharp
var affectedRows = await repository.ExecuteSqlAsync($"UPDATE Users SET Status = 1 WHERE IsActive = 1");
```

### 基类要求

- CQRS 仓储：`DedsiDddEfCoreRepository<TDbContext, TDomain, TKey>`
- 基础接口：`IDedsiCqrsRepository<TDomain, TKey>`
- 通用仓储：`DedsiEfCoreRepository<TDbContext, TEntity, TKey>`
- 基础接口：`IDedsiRepository<TEntity, TKey>`

## Related

- [CQRS 模式](./cqrs-pattern.md)
- [审计配置](./auditing-configuration.md)
