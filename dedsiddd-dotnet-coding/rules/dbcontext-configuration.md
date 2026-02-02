# DbContext 配置

## Rule

配置 DbContext 遵循 DedsiDDD 约定以确保一致性。

## Impact

HIGH

## Problem

不一致的 DbContext 配置会导致可维护性问题和潜在的运行时错误。

## Solution

### 接口定义

```csharp
[ConnectionStringName([Project]DomainConsts.ConnectionStringName)]
public interface I[Project]DbContext : IDedsiEfCoreDbContext
{
    DbSet<[Domain]> [Entities] { get; }
}
```

### 实现

```csharp
[ConnectionStringName([Project]DomainConsts.ConnectionStringName)]
public class [Project]DbContext(DbContextOptions<[Project]DbContext> options)
    : DedsiEfCoreDbContext<[Project]DbContext>(options), I[Project]DbContext
{
    public DbSet<[Domain]> [Entities] { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        ArgumentNullException.ThrowIfNull(modelBuilder);
        modelBuilder.ApplyConfigurationsFromAssembly(typeof([Project]DbContext).Assembly);
        base.OnModelCreating(modelBuilder);
    }
}
```

### DbSet 命名

- 使用聚合根名称的复数形式（如 `Risks`）
- 类型：`DbSet<聚合根>`

### 配置顺序

1. 空值验证（`ArgumentNullException.ThrowIfNull`）
2. 应用程序集配置
3. 调用 `base.OnModelCreating`

## Related

- [EF Core 实体映射](./ef-core-mapping.md)
