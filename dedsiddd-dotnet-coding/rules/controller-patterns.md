# Controller 模式

## Rule

实现 Controller 遵循 DedsiDDD 约定和项目特定的路由。

## Impact

HIGH

## Problem

不一致的 Controller 模式会导致路由冲突、错误的 API 契约和可维护性问题。

## Solution

### 基类

```csharp
public class [Domain]Controller(
    [Domain]Query [Domain]Query,
    [Domain]PagedQuery [Domain]PagedQuery,
    IDedsiMediator dedsiMediator) : [Project]Controller
{
    // ...
}
```

### 路由

- 继承 `[Project]Controller`，它定义了路由
- 使用 `[HttpPost]` 属性而不使用显式路由前缀
- 遵循现有项目的路由模式
- 不要修改路由以避免冲突

### Request 模型（推荐）

```csharp
/// <summary>
/// 创建请求对象
/// </summary>
public record Create[Domain]Request([Domain]CreateUpdateDto [Domain]);

/// <summary>
/// 修改请求对象
/// </summary>
public record Update[Domain]Request([Domain]CreateUpdateDto [Domain]);
```

### CancellationToken

使用 `HttpContext.RequestAborted`：

```csharp
public Task<[Domain]PagedResultDto> PagedQueryAsync([FromBody] [Domain]PagedInputDto input)
{
    input.IsExport = false;
    return [Domain]PagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);
}
```

### 端点模式

```csharp
/// <summary>
/// 分页查询
/// </summary>
[HttpPost]
public Task<[Domain]PagedResultDto> PagedQueryAsync([FromBody] [Domain]PagedInputDto input)
{
    input.IsExport = false;
    return [Domain]PagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);
}

/// <summary>
/// 查询详情
/// </summary>
[HttpGet("{id}")]
public Task<[Domain]Dto> GetAsync([FromRoute] string id)
    => [Domain]Query.GetAsync(id, HttpContext.RequestAborted);

/// <summary>
/// 创建
/// </summary>
[HttpPost]
public Task<string> CreateAsync([FromBody] Create[Domain]Request request)
    => dedsiMediator.SendAsync(new Create[Domain]Command(request.[Domain]), HttpContext.RequestAborted);

/// <summary>
/// 修改
/// </summary>
[HttpPost("{id}")]
public Task<bool> UpdateAsync([FromRoute] string id, [FromBody] Update[Domain]Request request)
    => dedsiMediator.SendAsync(new Update[Domain]Command(id, request.[Domain]), HttpContext.RequestAborted);

/// <summary>
/// 删除
/// </summary>
[HttpPost("{id}")]
public Task<bool> DeleteAsync([FromRoute] string id)
    => dedsiMediator.SendAsync(new Delete[Domain]Command(id), HttpContext.RequestAborted);
```

### 导出端点（可选）

仅当仓库中可使用 MiniExcel 时才包含：

```csharp
/// <summary>
/// 导出 Excel
/// </summary>
[HttpPost("export")]
public async Task<IActionResult> ExportExcelAsync([FromBody] [Domain]PagedInputDto input)
{
    input.IsExport = true;
    var result = await [Domain]PagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);

    var stream = new MemoryStream();
    await stream.SaveAsAsync(result.Items, cancellationToken: HttpContext.RequestAborted);
    stream.Seek(0, SeekOrigin.Begin);

    return File(
        stream,
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
        $"[Domain]-{DateTime.Now:yyyyMMddHHmmss}.xlsx"
    );
}
```

### 重要规则

1. 不要使用展示 `Dto` 作为 Update/Delete Body - 使用 `CreateUpdateDto`
2. Controller 返回类型必须与 Command/Query 返回类型匹配
3. Update/Delete 路由不得冲突
4. 导出端点是可选的 - 仅当 MiniExcel 可用时
5. 遵循现有项目的路由风格（action route vs resource route）
6. 使用 `HttpContext.RequestAborted` 进行取消

## Related

- [Command Handler](./command-handlers.md)
- [Query Handler](./query-handlers.md)
- [CancellationToken 传递](./cancellation-token.md)
