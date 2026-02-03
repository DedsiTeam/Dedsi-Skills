# 审计配置

## Rule

使用 Dedsi Framework 的审计接口和属性设置器实现自动审计。

## Impact

MEDIUM

## Problem

不正确的审计配置会导致审计字段无法自动填充。

## Solution

### 审计接口

实现审计接口以启用自动填充：

```csharp
// 创建审计：姓名/Id/创建时间
public class MyEntity : Entity<Guid>, IDedsiCreationAuditedObject
{
    public string CreatorName { get; private set; }
    public Guid CreatorId { get; private set; }
    public DateTime CreationTime { get; private set; }
    
    // 其他字段...
}
```

**可用审计接口**：

- `IDedsiCreationAuditedObject`：组合接口（包含 `IDedsiHasCreationId`、`IDedsiHasCreationName`、`IDedsiHasCreationTime`）
- `IDedsiHasCreationId`：创建人 ID
- `IDedsiHasCreationName`：创建人姓名
- `IDedsiHasCreationTime`：创建时间

### DedsiAuditPropertySetter 行为

`DedsiAuditPropertySetter` 继承 `AuditPropertySetter`，在 `SetCreationProperties` 中：

1. 调用 ABP 的基础审计设置
2. 检查 `IDedsiHasCreationName` 并填充 `CurrentUser.Name`
3. 检查 `IDedsiHasCreationId` 并填充 `CurrentUser.Id`
4. 检查 `IDedsiHasCreationTime` 并填充 `Clock.Now`

### 注意事项

- `DedsiAuditPropertySetter` 仅在 EF Core 集成下生效
- 若 `CurrentUser.Name` 为空，会抛出异常
- 若 `CurrentUser.Id` 无值，会抛出异常
- 确保已正确配置 ABP 的身份认证和当前用户服务

## Related

- [Repository 模式](./repository-pattern.md)
- [模块接入](./module-dependencies.md)
