# 集合管理

## Rule

正确管理集合以维护聚合完整性和不变式。

## Impact

MEDIUM

## Problem

不当的集合管理可能导致孤立实体、违反不变式和不一致的状态。

## Solution

### 集合声明

使用 `ICollection<T>` 和 `private set`：

```csharp
public ICollection<[Child]> Children { get; private set; } = [];
```

### 添加模式

添加前验证：

```csharp
public void AddChild([Child] child)
{
    Children.Add(Check.NotNull(child, nameof([Child])));
}
```

### 移除模式

```csharp
public void RemoveChild(string childId)
{
    var child = Children.FirstOrDefault(c => c.Id == childId);
    if (child != null)
    {
        Children.Remove(child);
    }
}
```

### 清空模式

```csharp
public void ClearChildren()
{
    Children.Clear();
}
```

### 更新策略

默认策略：Clear + Add 更新集合：

```csharp
public void UpdateChildren(IEnumerable<[Child]> newChildren)
{
    Children.Clear();
    foreach (var child in newChildren)
    {
        Children.Add(Check.NotNull(child, nameof([Child])));
    }
}
```

## Related

- [聚合根模式](./aggregate-root-patterns.md)
