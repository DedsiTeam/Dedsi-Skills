# 模块接入

## Rule

在 ABP 项目中正确接入 Dedsi Framework 模块。

## Impact

HIGH

## Problem

不正确的模块依赖配置会导致运行时错误或功能不可用。

## Solution

### 选择需要的模块

根据项目需求选择以下模块：

- Web API（异常处理/导出）→ `Dedsi.AspNetCore`
- DDD 基础（审计接口/仓储接口/查询标记）→ `Dedsi.Ddd.Domain` + `Dedsi.Ddd.Domain.Shared`
- 应用服务 → `Dedsi.Ddd.Application.Contracts` + `Dedsi.Ddd.Application`
- CQRS（MediatR + 记录）→ `Dedsi.Ddd.CQRS`
- EF Core 仓储实现 → `Dedsi.EntityFrameworkCore`
- SqlSugar Query → `Dedsi.SqlSugar`
- 分布式事务/消息 → `Dedsi.CAP`
- FastEndpoints → `Dedsi.FastEndpoints`

### DependsOn 配置

在项目的 Host 或启动模块上声明依赖：

```csharp
using Volo.Abp.Modularity;
using Dedsi.AspNetCore;
using Dedsi.Ddd.Domain;
using Dedsi.Ddd.Domain.Shared;
using Dedsi.Ddd.Application.Contracts;
using Dedsi.Ddd.Application;
using Dedsi.Ddd.CQRS;
using Dedsi.EntityFrameworkCore;
using Dedsi.SqlSugar;
using Dedsi.CAP;
using Dedsi.FastEndpoints;

[DependsOn(
    typeof(DedsiAspNetCoreModule),
    typeof(DedsiDddDomainModule),
    typeof(DedsiDddDomainSharedModule),
    typeof(DedsiDddApplicationContractsModule),
    typeof(DedsiDddApplicationModule),
    typeof(DedsiDddCqrsModule),
    typeof(DedsiEntityFrameworkCoreModule),
    typeof(DedsiSqlSugarModule)
)]
public class YourProjectModule : AbpModule
{
    public override void ConfigureServices(ServiceConfigurationContext context)
    {
        // 配置 SqlSugar（可选）
        context.Services.ConfigureSqlSugarBySqlServer("Default");
        
        // 或配置 MySql
        // context.Services.ConfigureSqlSugarByMySql("Default");
    }
}
```

### 模块依赖关系

- `Dedsi.CAP` 依赖 `Dedsi.AspNetCore`
- `Dedsi.FastEndpoints` 依赖 `Dedsi.AspNetCore`
- `Dedsi.EntityFrameworkCore` 实现 `IDedsiEfCoreDbContext`
- `Dedsi.SqlSugar` 提供 `ISqlSugarClient`

## Related

- [异常处理](./exception-handling.md)
- [CQRS 模式](./cqrs-pattern.md)
- [审计配置](./auditing-configuration.md)
- [SqlSugar 配置](./sqlsugar-configuration.md)
