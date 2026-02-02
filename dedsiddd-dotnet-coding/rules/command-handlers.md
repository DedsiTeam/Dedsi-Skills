# Command Handler

## Rule

实现 Command Handler 遵循 CQRS 模式和 DedsiDDD 约定。

## Impact

HIGH

## Problem

不一致的 Command Handler 实现会导致可维护性问题和 CQS 违反。

## Solution

### 文件组织

Command 和 Handler 必须在同一文件中。Create/Update/Delete 应该在独立的文件中。

### Create Command

```csharp
using Dedsi.Ddd.CQRS.CommandHandlers;
using Dedsi.Ddd.CQRS.Commands;

/// <summary>
/// 创建 [Domain] 命令
/// </summary>
public record Create[Domain]Command([Domain]CreateUpdateDto Dto) : DedsiCommand<string>;

/// <summary>
/// 创建 [Domain] 命令处理器
/// </summary>
public class Create[Domain]CommandHandler(I[Domain]Repository repository)
    : DedsiCommandHandler<Create[Domain]Command, string>
{
    public override async Task<string> Handle(Create[Domain]Command command, CancellationToken cancellationToken)
    {
        var domainId = Ulid.NewUlid().ToString();
        var domain = new [Domain](domainId, command.Dto.RequiredField);
        await repository.InsertAsync(domain, autoSave: true, cancellationToken);
        return domainId;
    }
}
```

### Update Command

```csharp
/// <summary>
/// 更新 [Domain] 命令
/// </summary>
public record Update[Domain]Command(string Id, [Domain]CreateUpdateDto Dto) : DedsiCommand<bool>;

/// <summary>
/// 更新 [Domain] 命令处理器
/// </summary>
public class Update[Domain]CommandHandler(I[Domain]Repository repository)
    : DedsiCommandHandler<Update[Domain]Command, bool>
{
    public override async Task<bool> Handle(Update[Domain]Command command, CancellationToken cancellationToken)
    {
        var domain = await repository.GetAsync(e => e.Id == command.Id, true, cancellationToken);
        domain.ChangeRequiredField(command.Dto.RequiredField);
        await repository.UpdateAsync(domain, autoSave: true, cancellationToken);
        return true;
    }
}
```

### Delete Command

```csharp
/// <summary>
/// 删除 [Domain] 命令
/// </summary>
public record Delete[Domain]Command(string Id) : DedsiCommand<bool>;

/// <summary>
/// 删除 [Domain] 命令处理器
/// </summary>
public class Delete[Domain]CommandHandler(I[Domain]Repository repository)
    : DedsiCommandHandler<Delete[Domain]Command, bool>
{
    public override async Task<bool> Handle(Delete[Domain]Command command, CancellationToken cancellationToken)
    {
        var domain = await repository.GetAsync(command.Id, true, cancellationToken);
        await repository.DeleteAsync(domain, true, cancellationToken);
        return true;
    }
}
```

### Handler 基类

继承 `DedsiCommandHandler<TCommand, TResult>` 以自动注入常用服务（Logger、CurrentUser 等）。

### 返回类型

- Create: 返回新实体 ID（通常是 `string`）
- Update: 返回 `bool`
- Delete: 返回 `bool`

## Related

- [Repository 模式](./repository-patterns.md)
- [CancellationToken 传递](./cancellation-token.md)
