# DTO 约定

## Rule

遵循一致的 DTO 约定以提高清晰度和可维护性。

## Impact

MEDIUM

## Problem

不一致的 DTO 命名和结构会导致混淆和维护问题。

## Solution

### 命名约定

- 展示 DTO：`[Domain]Dto`
- 创建/更新 DTO：`[Domain]CreateUpdateDto`
- 分页输入：`[Domain]PagedInputDto`（继承 `DedsiPagedRequestDto`）
- 分页行：`[Domain]PagedRowDto`
- 分页结果：`[Domain]PagedResultDto`（继承 `DedsiPagedResultDto<[Domain]PagedRowDto>`）

### 属性类型

- 所有属性使用 `public get; set;`
- 可空引用类型：`string?`、`T?`
- 集合：`IEnumerable<T>` 使用 `[]` 默认值

### XML 注释

每个字段/属性必须有 XML 文档：

```csharp
/// <summary>
/// 标识
/// </summary>
public string Id { get; set; }

/// <summary>
/// 创建时间
/// </summary>
public DateTime CreationTime { get; set; }

/// <summary>
/// 必填字段
/// </summary>
public string RequiredField { get; set; }
```

### DTO 分离

不要直接暴露领域对象类型：

```csharp
// 好
public class [Domain]Dto
{
    public string Id { get; set; }
    public IEnumerable<[Child]Dto> Children { get; set; } = [];
}

// 不好
public class [Domain]Dto
{
    public string Id { get; set; }
    public IEnumerable<[Child]> Children { get; set; } = []; // 不要暴露领域类型
}
```

## Related

- [DTO XML 注释](./dto-xml-comments.md)
