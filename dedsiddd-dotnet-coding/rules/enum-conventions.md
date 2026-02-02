# 枚举约定

## Rule

所有枚举必须显式赋值，第一个业务值从 1 开始。

## Impact

MEDIUM

## Problem

未显式赋值的枚举会导致数据库映射问题和业务逻辑错误。

## Solution

### 枚举定义

```csharp
public enum Status
{
    Active = 1,
    Inactive = 2,
    Deleted = 3
}
```

### 枚举验证

在领域模型中验证枚举值：

```csharp
public void ChangeStatus(Status status)
{
    if (!Enum.IsDefined(typeof(Status), status))
    {
        throw new BusinessException("无效的状态值");
    }
    Status = status;
}
```

### EF Core 映射

```csharp
builder.Property(e => e.Status)
    .IsRequired()
    .HasConversion<int>();
```

### DTO 文档注释

```csharp
/// <summary>
/// 状态（1-启用，2-停用，3-删除）
/// </summary>
public int Status { get; set; }
```

### 为什么从 1 开始

- 数据库默认值语义更清晰（0 通常表示"未设置"）
- 避免 EF Core 的默认行为问题
- 符合业务语义（激活状态从 1 开始）

## Related

- [entity-creation-validation.md](./entity-creation-validation.md)
