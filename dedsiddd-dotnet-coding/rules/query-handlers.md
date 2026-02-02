# Query Handler

## Rule

实现 Query Handler 遵循 CQRS 模式和 DedsiDDD 约定。

## Impact

HIGH

## Problem

不一致的 Query 实现会导致性能问题、内存泄漏和错误结果。

## Solution

### 文件组织

Query 接口和实现必须在同一文件中。

### 分页查询

```csharp
using Dedsi.Ddd.Application.Contracts.Dtos;
using Dedsi.Ddd.Domain.Queries;
using Microsoft.EntityFrameworkCore;
using Volo.Abp.Linq;

/// <summary>
/// [Domain] 分页查询
/// </summary>
/// <param name="[Project]DbContext"></param>
public class [Domain]PagedQuery(I[Project]DbContext [Project]DbContext) : IDedsiQuery
{
    /// <summary>
    /// [Domain] 分页条件查询
    /// </summary>
    /// <param name="input"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task<[Domain]PagedResultDto> PagedQueryAsync([Domain]PagedInputDto input, CancellationToken cancellationToken)
    {
        var query = [Project]DbContext
            .[Entities]
            .AsNoTracking()
            .WhereIf(!string.IsNullOrWhiteSpace(input.Keyword), e => e.RequiredField.Contains(input.Keyword!));

        var totalCount = await query.CountAsync(cancellationToken);

        query = query.OrderByDescending(e => e.CreationTime);
        if (!input.IsExport)
        {
            query = query.PageBy(input.GetSkipCount(), input.PageSize);
        }

        var items = await query
            .Select(e => new [Domain]PagedRowDto { Id = e.Id, Example = e.RequiredField })
            .ToListAsync(cancellationToken);

        return new [Domain]PagedResultDto { TotalCount = totalCount, Items = items };
    }
}
```

### 单项查询

```csharp
/// <summary>
/// [Domain] 查询
/// </summary>
/// <param name="dbContext"></param>
/// <param name="repository"></param>
public class [Domain]Query(
    I[Project]DbContext [Project]DbContext,
    I[Domain]Repository [Domain]Repository) : IDedsiQuery
{
    /// <summary>
    /// 获取详情
    /// </summary>
    /// <param name="id"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task<[Domain]Dto> GetAsync(string id, CancellationToken cancellationToken)
    {
        var domain = await [Domain]Repository.GetAsync(e => e.Id == id, true, cancellationToken);
        return new [Domain]Dto
        {
            Id = domain.Id,
            CreationTime = domain.CreationTime,
            RequiredField = domain.RequiredField,
            OptionalField = domain.OptionalField,
            Children = domain.Children.Select(c => new [Child]Dto { /* 字段映射 */ })
        };
    }
}
```

### 关键要求

1. 实现 `IDedsiQuery` 接口
2. 分页查询使用 `AsNoTracking()`
3. 遵循查询顺序：筛选 → Count → 排序 →（非导出则分页）→ 投影 → ToList
4. 注入 `I[Project]DbContext` 用于 EF Core 查询
5. 为所有公共方法包含 XML 注释
6. 将 `CancellationToken` 传递给所有异步 EF Core 操作

## Related

- [分页查询](./pagination-queries.md)
- [CancellationToken 传递](./cancellation-token.md)
