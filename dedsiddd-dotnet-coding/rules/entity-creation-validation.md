# 实体创建和验证

## Rule

实现正确的实体创建和验证模式以确保领域完整性。

## Impact

HIGH

## Problem

没有适当验证的实体可能导致无效的领域状态和数据损坏。

## Solution

### 构造函数验证

所有必填字段必须在构造函数中验证：

```csharp
public [Domain](string id, string requiredField)
    : base(id)
{
    ChangeRequiredField(requiredField);
    CreationTime = DateTime.Now;
}
```

### 属性变更方法

每个可变属性应该有一个 `Change*` 方法：

```csharp
public void ChangeRequiredField(string value)
{
    RequiredField = Check.NotNullOrWhiteSpace(value, nameof(RequiredField));
}
```

### 值对象

对于复杂值，使用值对象：

```csharp
public void ChangeAddress(Address address)
{
    Address = Check.NotNull(address, nameof(Address));
}
```

### 枚举验证

所有枚举必须显式赋值，从 `1` 开始：

```csharp
public enum Status
{
    Active = 1,
    Inactive = 2,
    Deleted = 3
}

public void ChangeStatus(Status status)
{
    if (!Enum.IsDefined(typeof(Status), status))
    {
        throw new BusinessException("Invalid status value");
    }
    Status = status;
}
```

## Related

- [聚合根模式](./aggregate-root-patterns.md)
