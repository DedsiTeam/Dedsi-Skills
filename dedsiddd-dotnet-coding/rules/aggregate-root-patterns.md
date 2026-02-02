# 聚合根模式

## Rule

定义聚合根遵循 DedsiDDD 约定以确保一致性和正确的领域建模。

## Impact

HIGH

## Problem

不遵循聚合根模式会导致领域模型缺乏一致性、验证和适当的封装。

## Solution

### 聚合根定义

- 聚合根必须继承 `DedsiAggregateRoot<string>`
- 必须包含 `protected` 无参构造函数供 ORM 使用
- 属性使用 `private set` 实现不可变性
- 状态变更通过方法：`Change+属性名`、`Add+元素名`、`Remove+元素名`、`Clear+集合名`

### 输入验证

- 字符串：`Check.NotNullOrWhiteSpace(value, nameof(PropertyName))`
- 引用类型：`Check.NotNull(object, nameof(ObjectName))`
- 枚举值：验证业务值合法性

### 幂等性和不变式

- 新旧值相同时直接返回（幂等性）
- 违反不变式时抛出 `BusinessException`

### Example

```csharp
using Volo.Abp;
using Dedsi.Ddd.Domain.Entities;

/// <summary>
/// [Domain]
/// </summary>
public class [Domain] : DedsiAggregateRoot<string>
{
    protected [Domain]() { }

    public [Domain](string id, string requiredField)
        : base(id)
    {
        ChangeRequiredField(requiredField);
        CreationTime = DateTime.Now;
    }

    public DateTime CreationTime { get; private set; }

    /// <summary>
    /// 中文注释
    /// </summary>
    public string RequiredField { get; private set; }

    public void ChangeRequiredField(string value)
    {
        RequiredField = Check.NotNullOrWhiteSpace(value, nameof(RequiredField));
    }

    public ICollection<[Child]> Children { get; private set; } = [];

    public void AddChild([Child] child)
    {
        Children.Add(Check.NotNull(child, nameof([Child])));
    }

    public void ClearChildren()
    {
        Children.Clear();
    }
}
```

## Related

- [实体创建和验证](./entity-creation-validation.md)
- [集合管理](./collection-management.md)
