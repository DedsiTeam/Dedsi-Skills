# CQRS 模式

## Rule

使用 Dedsi Framework 的 CQRS 组件实现命令处理和事件发布。

## Impact

HIGH

## Problem

不正确的 CQRS 实现会导致命令/事件记录丢失或功能不可用。

## Solution

### 命令发送

使用 `DedsiMediator` 而非直接使用 MediatR，以便自动记录命令日志。

```csharp
public class MyService
{
    private readonly IDedsiMediator _mediator;
    
    public MyService(IDedsiMediator mediator)
    {
        _mediator = mediator;
    }
    
    // 无返回值的命令
    public async Task ExecuteAsync()
    {
        var command = new MyCommand();
        await _mediator.SendAsync(command); // 自动记录
    }
    
    // 有返回值的命令
    public async Task<string> ExecuteWithResultAsync()
    {
        var command = new MyCommand<string>();
        return await _mediator.SendAsync<string>(command); // 自动记录
    }
}
```

### 命令定义

```csharp
// 无返回值的命令
public record MyCommand : DedsiCommand
{
    public string Data { get; init; }
}

// 有返回值的命令
public record MyCommand<TResponse> : DedsiCommand<TResponse>
{
    public string Data { get; init; }
}
```

### 命令处理器

```csharp
// 无返回值的命令处理器
public class MyCommandHandler : DedsiCommandHandler<MyCommand>
{
    public override async Task Handle(MyCommand command, CancellationToken cancellationToken)
    {
        // 使用注入的服务
        Logger.LogInformation($"Handling command: {command.CommandId}");
        
        // 业务逻辑
    }
}

// 有返回值的命令处理器
public class MyCommandHandlerWithResponse : DedsiCommandHandler<MyCommand<string>, string>
{
    public override async Task<string> Handle(MyCommand<string> command, CancellationToken cancellationToken)
    {
        Logger.LogInformation($"Handling command: {command.CommandId}");
        
        // 业务逻辑
        return "result";
    }
}
```

### 事件发布

使用 `DedsiLocalEventBus` 发布事件，以便自动记录事件日志。

```csharp
public class MyService
{
    private readonly ILocalEventBus _eventBus;
    
    public MyService(ILocalEventBus eventBus)
    {
        _eventBus = eventBus;
    }
    
    public async Task PublishAsync()
    {
        var @event = new MyEvent();
        await _eventBus.PublishAsync(@event); // 自动记录
    }
}
```

### 事件定义

```csharp
public record MyEvent : DedsiEvent
{
    public string Data { get; init; }
    
    public MyEvent(string data)
    {
        Data = data;
    }
}
```

### 自动记录机制

- **命令记录**：通过 `DedsiMediator` 在发送前后自动记录
- **事件记录**：通过 `DedsiLocalEventBus` 的 `PublishAsync` 自动记录
- **实体事件**：ABP 的 `EntityCreatedEventData`、`EntityDeletedEventData`、`EntityUpdatedEventData` 对聚合根自动记录

### 命令/事件记录配置

实现并注册 `ICommandEventRecorderRepository`：

```csharp
public class MyCommandEventRecorderRepository : ICommandEventRecorderRepository
{
    public Task InsertAsync(Guid dataId, string name, string fullName, RecorderDataSource dataSource, CancellationToken cancellationToken)
    {
        // 实现落库逻辑
    }
    
    public Task InsertAsync(CommandEventRecorder entity, CancellationToken cancellationToken)
    {
        // 实现落库逻辑
    }
    
    public Task<(long, CommandEventRecorder[])> GetPagedListAsync(int skipCount, int maxResultCount, string name, string creatorName)
    {
        // 实现分页查询逻辑
    }
    
    public Task<CommandEventRecorder[]> GetByDataIdAsync(Guid dataId)
    {
        // 实现查询逻辑
    }
}
```

开发期可使用 `LocalCommandEventRecorderRepository`（仅输出日志，不落库）验证记录链路。

## Related

- [模块接入](./module-dependencies.md)
- [Repository 模式](./repository-pattern.md)
