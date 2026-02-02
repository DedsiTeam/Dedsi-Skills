# 分页查询

## Rule

分页查询必须遵循固定的查询顺序，确保性能和导出功能的一致性。

## Impact

HIGH

## Problem

错误的查询顺序会导致性能问题、导出数据不完整或结果不一致。

## Solution

### 查询顺序

必须严格遵循以下顺序：

1. **筛选** (Filter) - 应用 Where 条件
2. **计数** (Count) - 获取总数
3. **排序** (Sort) - 应用排序
4. **分页** (Page) - 非导出时应用分页
5. **投影** (Project) - 选择需要的字段
6. **执行** (ToList) - 执行查询

### 标准模板

```csharp
public async Task<[Domain]PagedResultDto> PagedQueryAsync([Domain]PagedInputDto input, CancellationToken cancellationToken)
{
    // 1. 筛选
    var query = [Project]DbContext
        .[Entities]
        .AsNoTracking()
        .WhereIf(!string.IsNullOrWhiteSpace(input.Keyword), e => e.RequiredField.Contains(input.Keyword!))
        .WhereIf(input.Status.HasValue, e => e.Status == input.Status.Value);

    // 2. 计数
    var totalCount = await query.CountAsync(cancellationToken);

    // 3. 排序
    query = query.OrderByDescending(e => e.CreationTime);

    // 4. 分页（仅非导出时）
    if (!input.IsExport)
    {
        query = query.PageBy(input.GetSkipCount(), input.PageSize);
    }

    // 5. 投影
    var items = await query
        .Select(e => new [Domain]PagedRowDto
        {
            Id = e.Id,
            Example = e.RequiredField,
            Status = e.Status
        })
        // 6. 执行
        .ToListAsync(cancellationToken);

    return new [Domain]PagedResultDto { TotalCount = totalCount, Items = items };
}
```

### 关键点

- **AsNoTracking()**: 分页查询必须使用，避免不必要的变更追踪
- **WhereIf**: 使用条件筛选，避免空值过滤
- **IsExport**: 导出时不分页，返回所有数据
- **PageBy**: 使用 ABP 的分页扩展方法

### 常见错误

```csharp
// 错误：在 Count 之后才应用筛选
var query = dbContext.Entities.AsNoTracking();
var count = await query.CountAsync(cancellationToken);
query = query.Where(e => e.Status == status); // 错误：筛选太晚

// 错误：忘记 AsNoTracking
var query = dbContext.Entities.Where(e => e.Status == status); // 错误：缺少 AsNoTracking

// 错误：先投影再排序
var items = query.Select(e => new { e.Id, e.Name })
    .OrderByDescending(e => e.CreationTime) // 错误：投影后可能丢失 CreationTime
    .ToListAsync(cancellationToken);
```

## Related

- [query-handlers.md](./query-handlers.md)
