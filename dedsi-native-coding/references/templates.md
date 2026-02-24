# DedsiNative 模板与骨架

## 目录
1. 领域实体
2. 仓储接口
3. 仓储实现
4. EF Core 实体配置
5. DbContext 追加
6. Operation 模板
7. Endpoint 模板
8. 注册端点
9. 新增实体检查清单

## 领域实体
位置
`content/src/DedsiNative.Domain/{EntityName}s/{EntityName}.cs`

模板
```csharp
namespace DedsiNative.{EntityName}s;

public class {EntityName}
{
    protected {EntityName}() { }

    public {EntityName}(string id, string property1, string property2)
    {
        Id = id;
        ChangeProperty1(property1);
        ChangeProperty2(property2);
    }

    public string Id { get; private set; } = null!;
    public string Property1 { get; private set; } = null!;
    public string Property2 { get; private set; } = null!;

    public {EntityName} ChangeProperty1(string property1)
    {
        if (string.IsNullOrWhiteSpace(property1))
        {
            throw new ArgumentException("Property1 不能为空。", nameof(property1));
        }

        Property1 = property1.Trim();
        return this;
    }

    public {EntityName} ChangeProperty2(string property2)
    {
        if (string.IsNullOrWhiteSpace(property2))
        {
            throw new ArgumentException("Property2 不能为空。", nameof(property2));
        }

        Property2 = property2.Trim();
        return this;
    }
}
```

## 仓储接口
位置
`content/src/DedsiNative.Domain/{EntityName}s/I{EntityName}Repository.cs`

模板
```csharp
using Dedsi.Repositories;

namespace DedsiNative.{EntityName}s;

public interface I{EntityName}Repository : IDedsiNativeRepository<{EntityName}, string>;
```
主键为 Guid 时，将 string 替换为 Guid，并同步调整相关 DTO 与操作类的主键类型。

## 仓储实现
位置
`content/src/DedsiNative.Infrastructure/Repositories/{EntityName}Repository.cs`

模板
```csharp
using DedsiNative.{EntityName}s;
using DedsiNative.EntityFrameworkCores;

namespace DedsiNative.Repositories;

public class {EntityName}Repository(DedsiNativeDbContext dedsiNativeDbContext)
    : DedsiNativeEfCoreRepository<{EntityName}, string>(dedsiNativeDbContext), I{EntityName}Repository;
```

## EF Core 实体配置
位置
`content/src/DedsiNative.Infrastructure/EntityFrameworkCores/EntityConfigurations/{EntityName}Configuration.cs`

模板
```csharp
using DedsiNative.{EntityName}s;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace DedsiNative.EntityFrameworkCores.EntityConfigurations;

internal class {EntityName}Configuration : IEntityTypeConfiguration<{EntityName}>
{
    public void Configure(EntityTypeBuilder<{EntityName}> builder)
    {
        builder.ToTable("{TableName}");
        builder.HasKey(e => e.Id);
    }
}
```

## DbContext 追加
位置
`content/src/DedsiNative.Infrastructure/EntityFrameworkCores/DedsiNativeDbContext.cs`

模板
```csharp
using DedsiNative.{EntityName}s;

public DbSet<{EntityName}> {EntityName}s { get; set; }
```

## Operation 模板
位置
`content/src/DedsiNative.Operation/{EntityName}s/Operations/`

创建操作
```csharp
using Dedsi;

namespace DedsiNative.{EntityName}s.Operations;

public record Create{EntityName}InputDto
(
    string Property1,
    string Property2
);

public class Create{EntityName}Operation(I{EntityName}Repository {entityName}Repository)
    : DedsiNativeOperation<Create{EntityName}InputDto, bool>
{
    public override Task<bool> ExecuteAsync(Create{EntityName}InputDto input, CancellationToken cancellationToken)
    {
        var {entityName} = new {EntityName}(
            GetStringPrimaryKey(),
            input.Property1,
            input.Property2
        );

        return {entityName}Repository.InsertAsync({entityName}, cancellationToken);
    }
}
```

更新操作
```csharp
using Dedsi;

namespace DedsiNative.{EntityName}s.Operations;

public record Update{EntityName}InputDto
(
    string Id,
    string Property1,
    string Property2
);

public class Update{EntityName}Operation(I{EntityName}Repository {entityName}Repository)
    : DedsiNativeOperation<Update{EntityName}InputDto, bool>
{
    public override async Task<bool> ExecuteAsync(Update{EntityName}InputDto input, CancellationToken cancellationToken)
    {
        var {entityName} = await {entityName}Repository.GetAsync(input.Id, cancellationToken);

        {entityName}.ChangeProperty1(input.Property1);
        {entityName}.ChangeProperty2(input.Property2);

        return await {entityName}Repository.UpdateAsync({entityName}, cancellationToken);
    }
}
```

删除操作
```csharp
using Dedsi;

namespace DedsiNative.{EntityName}s.Operations;

public record Delete{EntityName}InputDto(string Id);

public class Delete{EntityName}Operation(I{EntityName}Repository {entityName}Repository)
    : DedsiNativeOperation<Delete{EntityName}InputDto, bool>
{
    public override Task<bool> ExecuteAsync(Delete{EntityName}InputDto input, CancellationToken cancellationToken)
    {
        return {entityName}Repository.DeleteAsync(input.Id, cancellationToken);
    }
}
```

单条查询操作
```csharp
using Dedsi;

namespace DedsiNative.{EntityName}s.Operations;

public class {EntityName}Dto
{
    public string Id { get; set; }
    public string Property1 { get; set; }
    public string Property2 { get; set; }
}

public class Get{EntityName}Operation(I{EntityName}Repository {entityName}Repository)
    : DedsiNativeOperation<string, {EntityName}Dto>
{
    public override async Task<{EntityName}Dto> ExecuteAsync(string id, CancellationToken cancellationToken)
    {
        var {entityName} = await {entityName}Repository.GetAsync(id, cancellationToken);

        return new {EntityName}Dto
        {
            Id = {entityName}.Id,
            Property1 = {entityName}.Property1,
            Property2 = {entityName}.Property2
        };
    }
}
```

条件分页查询操作
```csharp
using Dedsi;
using DedsiNative.EntityFrameworkCores;
using Microsoft.EntityFrameworkCore;

namespace DedsiNative.{EntityName}s.Operations;

public class {EntityName}PagedQueryInputDto
{
    public int PageIndex { get; set; } = 1;
    public int PageSize { get; set; } = 10;
    public bool? IsPaged { get; set; } = true;
    public string? Property1 { get; set; }
    public string? Property2 { get; set; }
}

public class {EntityName}PagedQueryResultDto
{
    public int TotalCount { get; set; }
    public IEnumerable<{EntityName}PagedQueryRowDto> Items { get; set; } = [];
}

public class {EntityName}PagedQueryRowDto
{
    public string Id { get; set; }
    public string Property1 { get; set; }
    public string Property2 { get; set; }
}

public class ConditionalQueryOperation(DedsiNativeDbContext dedsiNativeDbContext)
    : DedsiNativeOperation<{EntityName}PagedQueryInputDto, {EntityName}PagedQueryResultDto>
{
    public override async Task<{EntityName}PagedQueryResultDto> ExecuteAsync(
        {EntityName}PagedQueryInputDto input,
        CancellationToken cancellationToken)
    {
        var query = dedsiNativeDbContext
            .{EntityName}s
            .AsQueryable()
            .WhereIf(input.Property1, e => e.Property1.Contains(input.Property1!))
            .WhereIf(input.Property2, e => e.Property2.Contains(input.Property2!));

        var totalCount = await query.CountAsync(cancellationToken: cancellationToken);

        var items = await query
            .OrderByDescending(e => e.Property1)
            .PagedBy(input.IsPaged, input.PageIndex, input.PageSize)
            .Select(e => new {EntityName}PagedQueryRowDto
            {
                Id = e.Id,
                Property1 = e.Property1,
                Property2 = e.Property2
            })
            .ToArrayAsync(cancellationToken);

        return new {EntityName}PagedQueryResultDto
        {
            TotalCount = totalCount,
            Items = items
        };
    }
}
```

## Endpoint 模板
位置
`content/src/DedsiNative.HttpApi/Apis/{ResourcePlural}/{EntityName}Endpoints.cs`

ResourcePlural 使用资源名英文复数，例如 Users。路由段使用实体名的 kebab-case，例如 DedsiUser -> dedsi-users。

模板
```csharp
using DedsiNative.{EntityName}s.Operations;
using Microsoft.AspNetCore.Mvc;

namespace DedsiNative.Apis.{ResourcePlural};

public static class {EntityName}Endpoints
{
    public static void Map{EntityName}Endpoints(this IEndpointRouteBuilder endpoints)
    {
        var group = endpoints
            .MapGroup("/api/{kebab-entity-name}s")
            .WithTags("{EntityName}");

        group
            .MapPost("/", (
                [FromBody] Create{EntityName}InputDto input,
                [FromServices] Create{EntityName}Operation operation,
                CancellationToken cancellationToken) => operation.ExecuteAsync(input, cancellationToken))
            .WithName("Create{EntityName}")
            .WithSummary("创建 {EntityName}")
            .WithDescription("创建一个新的 {EntityName}");

        group
            .MapDelete("/{id}", (
                [FromRoute] string id,
                [FromServices] Delete{EntityName}Operation operation,
                CancellationToken cancellationToken) =>
            {
                var input = new Delete{EntityName}InputDto(id);
                return operation.ExecuteAsync(input, cancellationToken);
            })
            .WithName("Delete{EntityName}")
            .WithSummary("删除 {EntityName}")
            .WithDescription("根据ID删除指定的 {EntityName}");

        group
            .MapPut("/{id}", (
                [FromRoute] string id,
                [FromBody] Update{EntityName}InputDto input,
                [FromServices] Update{EntityName}Operation operation,
                CancellationToken cancellationToken) =>
            {
                var dto = input with { Id = id };
                return operation.ExecuteAsync(dto, cancellationToken);
            })
            .WithName("Update{EntityName}")
            .WithSummary("更新 {EntityName}")
            .WithDescription("更新指定的 {EntityName} 信息");

        group
            .MapGet("/{id}", (
                [FromRoute] string id,
                [FromServices] Get{EntityName}Operation operation,
                CancellationToken cancellationToken) => operation.ExecuteAsync(id, cancellationToken))
            .WithName("Get{EntityName}ById")
            .WithSummary("根据 ID 获取")
            .WithDescription("通过 ID 获取单个 {EntityName} 信息");

        group
            .MapPost("/paged", (
                [FromBody] {EntityName}PagedQueryInputDto input,
                [FromServices] ConditionalQueryOperation operation,
                CancellationToken cancellationToken) =>
            {
                input.IsPaged = true;
                return operation.ExecuteAsync(input, cancellationToken);
            })
            .WithName("Get{EntityName}sPagedQuery")
            .WithSummary("分页查询")
            .WithDescription("根据条件分页查询 {EntityName} 列表");
    }
}
```

## 注册端点
位置
`content/src/DedsiNative.HttpApi/Apis/DedsiNativeEndpoints.cs`

模板
```csharp
using DedsiNative.Apis.{ResourcePlural};

endpoints.Map{EntityName}Endpoints();
```

## 新增实体检查清单
1. 创建领域实体与 Change 方法校验。
2. 添加仓储接口与实现。
3. 添加 EF Core 配置与 DbSet。
4. 完成 CRUD 与条件分页 Operation。
5. 添加 Endpoint 并注册。
6. 运行应用并验证 OpenAPI 与数据库访问。
