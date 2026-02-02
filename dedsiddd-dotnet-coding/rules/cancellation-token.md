# CancellationToken 传递

## Rule

所有公共 API 必须接受并在整个调用链中传递 CancellationToken。

## Impact

HIGH

## Problem

缺少 CancellationToken 传递会导致取消支持不佳和资源浪费。

## Solution

### Controller 层

使用 `HttpContext.RequestAborted`：

```csharp
public Task<[Domain]PagedResultDto> PagedQueryAsync([FromBody] [Domain]PagedInputDto input)
    => [Domain]PagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);
```

### Command Handlers

```csharp
public override async Task<string> Handle(Create[Domain]Command command, CancellationToken cancellationToken)
{
    var domainId = Ulid.NewUlid().ToString();
    var domain = new [Domain](domainId, command.Dto.RequiredField);
    await repository.InsertAsync(domain, autoSave: true, cancellationToken);
    return domainId;
}
```

### Query Handlers

```csharp
public async Task<[Domain]PagedResultDto> PagedQueryAsync([Domain]PagedInputDto input, CancellationToken cancellationToken)
{
    var query = [Project]DbContext
        .[Entities]
        .AsNoTracking()
        .WhereIf(!string.IsNullOrWhiteSpace(input.Keyword), e => e.RequiredField.Contains(input.Keyword!));

    var totalCount = await query.CountAsync(cancellationToken);

    // ... 其余实现
}
```

### Repository 层

所有 EF Core 操作必须包含 `cancellationToken`：

```csharp
await repository.InsertAsync(domain, autoSave: true, cancellationToken);
await repository.UpdateAsync(domain, autoSave: true, cancellationToken);
await repository.DeleteAsync(domain, true, cancellationToken);
var domain = await repository.GetAsync(e => e.Id == id, true, cancellationToken);
```

### Mediator

```csharp
await dedsiMediator.SendAsync(new Create[Domain]Command(request.[Domain]), HttpContext.RequestAborted);
```

### 要求

1. Controller 方法接受 `HttpContext.RequestAborted`
2. Command/Query handler 签名包含 `CancellationToken`
3. 所有 EF Core 操作传递 `cancellationToken`
4. Repository 调用传播 `cancellationToken`
5. Mediator 调用传播 `cancellationToken`

## Related

- [Command Handler](./command-handlers.md)
- [Query Handler](./query-handlers.md)
- [Controller 模式](./controller-patterns.md)
