# 异常处理

## Rule

在项目中正确配置 Dedsi Global Exception Handler 以统一错误响应格式。

## Impact

MEDIUM

## Problem

不统一的错误响应格式会影响前端调用和错误处理。

## Solution

### 选择错误响应格式

#### 选项 1：ABP 默认错误响应（推荐）

保持 ABP 的异常处理与错误返回，不启用 `DedsiGlobalExceptionHandler`。

业务层使用 `BusinessException` / `UserFriendlyException` 表达可预期错误。

```csharp
// 业务层抛出异常
throw new BusinessException("ERROR_CODE", "错误消息");
// 或
throw new UserFriendlyException("ERROR_CODE", "用户友好错误消息");
```

#### 选项 2：DedsiErrorData 错误响应

仅当调用方明确要求 `DedsiErrorData` 契约时，在 `Program.cs` 中启用。

**注册中间件**：

```csharp
builder.Services.AddExceptionHandler<DedsiGlobalExceptionHandler>();
app.UseExceptionHandler();
```

**错误码提取规则**：

- 优先从 `UserFriendlyException.Code` 提取
- 其次从 `BusinessException.Code` 提取
- 默认使用 `global-error-500`

**返回格式**：

```json
{
  "Error": {
    "Message": "错误消息",
    "Code": "ERROR_CODE"
  }
}
```

### DedsiGlobalExceptionHandler 行为

- 实现 `IExceptionHandler`
- 自动使用 `logger.LogException()` 记录异常
- 返回状态码：`500`
- 捕获并处理 `UserFriendlyException` 和 `BusinessException`

## Related

- [模块接入](./module-dependencies.md)
