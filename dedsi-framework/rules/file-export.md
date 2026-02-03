# Controller 导出文件

## Rule

使用 DedsiControllerBase 快速实现 Excel/PDF 导出功能。

## Impact

LOW

## Problem

手动实现文件导出会导致代码重复和不一致。

## Solution

### 继承 DedsiControllerBase

```csharp
using Dedsi.AspNetCore;

public class MyController : DedsiControllerBase
{
    // 控制器代码
}
```

### 导出 Excel

#### 使用 byte[]

```csharp
public IActionResult ExportExcel()
{
    var bytes = GenerateExcelBytes();
    return FileByExcel(bytes, "report.xlsx");
}
```

#### 使用 Stream

```csharp
public IActionResult ExportExcel()
{
    var stream = GenerateExcelStream();
    return FileByExcel(stream, "report.xlsx");
}
```

### 导出 PDF

```csharp
public IActionResult ExportPdf()
{
    var bytes = GeneratePdfBytes();
    return FileByPdf(bytes, "report.pdf");
}
```

### MIME 类型

- Excel：`application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`
- PDF：`application/pdf`

## Related

- [异常处理](./exception-handling.md)
- [模块接入](./module-dependencies.md)
