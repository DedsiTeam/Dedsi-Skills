# DTO XML 注释

## Rule

所有 DTO 必须有适当的 XML 文档以提高 API 清晰度。

## Impact

MEDIUM

## Problem

缺少 XML 注释会使 API 文档不清楚且难以使用。

## Solution

### 类文档

```csharp
/// <summary>
/// [Domain] 数据传输对象
/// </summary>
public class [Domain]Dto
{
    // ...
}
```

### 属性文档

```csharp
/// <summary>
/// 主键标识
/// </summary>
public string Id { get; set; }

/// <summary>
/// 创建时间
/// </summary>
public DateTime CreationTime { get; set; }

/// <summary>
/// 业务状态（1-启用，2-停用）
/// </summary>
public int Status { get; set; }

/// <summary>
/// 关键字搜索
/// </summary>
public string? Keyword { get; set; }
```

### 方法文档（在包含方法的 DTO 中）

```csharp
/// <summary>
/// 验证DTO数据有效性
/// </summary>
/// <returns>是否有效</returns>
public bool Validate()
{
    return !string.IsNullOrWhiteSpace(RequiredField);
}
```

### 实现类

对于实现类，使用 `<inheritdoc />`：

```csharp
/// <summary>
/// <inheritdoc />
/// </summary>
public class [Domain]ServiceImpl : I[Domain]Service
{
    // ...
}
```

## Related

- [DTO 约定](./dto-conventions.md)
