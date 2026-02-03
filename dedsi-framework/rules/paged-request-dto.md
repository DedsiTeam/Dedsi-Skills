# 分页请求 DTO

## Rule

使用 DedsiPagedRequestDto 实现统一的分页请求。

## Impact

MEDIUM

## Problem

不统一的分页实现会导致前端调用困难和维护成本增加。

## Solution

### 使用 DedsiPagedRequestDto

```csharp
using Dedsi.Ddd.Application.Contracts.Dtos;

public class MyPagedRequestDto : DedsiPagedRequestDto
{
    public string? Keyword { get; set; }
    
    public bool IsActive { get; set; }
}
```

### 属性说明

- `PageIndex`：当前第几页（从 1 开始）
- `PageSize`：每页多少条（限制：1-1000，默认 10）
- `IsExport`：是否导出（JSON Ignore，Controller 中设置为 true）

### 自动验证

```csharp
public int GetSkipCount()
{
    if (PageIndex < 1) { PageIndex = 1; }
    if (PageSize > 1000 || PageSize < 1) { PageSize = 10; }
    
    return (PageIndex - 1) * PageSize;
}
```

### 在 Controller 中使用

```csharp
[HttpGet]
public async Task<DedsiPagedResultDto<MyDto>> GetPagedAsync([FromQuery] MyPagedRequestDto input)
{
    var skipCount = input.GetSkipCount();
    var (total, items) = await _repository.GetPagedListAsync(
        x => x.IsActive,
        x => x.CreatedTime,
        isReverse: true,
        pageIndex: input.PageIndex,
        pageSize: input.PageSize
    );
    
    return new DedsiPagedResultDto<MyDto>(total, items);
}
```

### 导出功能

在 Controller 中设置 `IsExport`：

```csharp
public async Task<IActionResult> ExportAsync([FromQuery] MyPagedRequestDto input)
{
    input.IsExport = true;
    
    // 获取所有数据（不分页）
    var (total, items) = await _repository.GetPagedListAsync(
        x => x.IsActive,
        x => x.CreatedTime,
        isReverse: true,
        pageIndex: 1,
        pageSize: int.MaxValue
    );
    
    var bytes = GenerateExcel(items);
    return FileByExcel(bytes, "export.xlsx");
}
```

## Related

- [Controller 导出文件](./file-export.md)
- [Repository 模式](./repository-pattern.md)
