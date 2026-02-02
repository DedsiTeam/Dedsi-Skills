# Repository 模式

## Rule

实现 Repository 遵循 DedsiDDD 约定以确保一致性。

## Impact

HIGH

## Problem

不一致的 Repository 模式会导致可维护性问题和耦合问题。

## Solution

### 文件组织

Repository 接口和实现必须在同一文件中。

### 接口定义

```csharp
using Dedsi.Ddd.Domain.Repositories;

/// <summary>
/// [Domain] 仓储
/// </summary>
public interface I[Domain]Repository : IDedsiCqrsRepository<[Domain], string>;
```

### 实现

```csharp
using Dedsi.EntityFrameworkCore.Repositories;
using Volo.Abp.EntityFrameworkCore;
using [Project].Infrastructure.EntityFrameworkCore;

/// <summary>
/// [Domain] 仓储
/// </summary>
public class [Domain]Repository(IDbContextProvider<[Project]DbContext> dbContextProvider)
    : DedsiDddEfCoreRepository<[Project]DbContext, [Domain], string>(dbContextProvider), I[Domain]Repository;
```

### 构造函数

使用主构造函数注入 `IDbContextProvider<DbContext>`：

```csharp
public class [Domain]Repository(IDbContextProvider<[Project]DbContext> dbContextProvider)
    : DedsiDddEfCoreRepository<[Project]DbContext, [Domain], string>(dbContextProvider), I[Domain]Repository;
```

### 基类

- CQRS 仓储：`DedsiDddEfCoreRepository<TDbContext, TEntity, TKey>`
- 基础接口：`IDedsiCqrsRepository<TEntity, TKey>`

## Related

- [Command Handler](./command-handlers.md)
- [Query Handler](./query-handlers.md)
