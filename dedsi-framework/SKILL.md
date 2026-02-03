---
name: dedsi-framework
description: 基于 DedsiFramework 源代码，提供 Dedsi Framework（ABP 扩展框架）的模块速查、接入步骤、常见用法与问题定位建议。在读取源代码时忽略 bin 和 obj 目录。
---

# Dedsi Framework

Dedsi Framework 是基于 ABP 框架的扩展，提供 CQRS、Repository、审计、异常处理、ORM（EF Core/SqlSugar）、CAP、FastEndpoints 等增强功能。

## 何时使用

- 在 ABP 项目中接入 Dedsi Framework 模块
- 配置和使用 Dedsi CQRS 组件（命令、事件、命令处理器）
- 实现 Repository（CQRS 仓储和通用仓储）
- 配置审计功能（CreatorName 自动填充）
- 使用 DedsiControllerBase 导出 Excel/PDF
- 配置 SqlSugar ORM
- 配置异常处理中间件
- 遵循 Dedsi Framework 约定进行开发

---

## 能力规则

没有这些规则，AI 无法解决问题或生成的代码无法正确运行。

### 模块配置

| 规则 | 影响 | 描述 |
|------|------|------|
| [module-dependencies.md](rules/module-dependencies.md) | HIGH | 在 ABP 项目中正确接入 Dedsi Framework 模块 |

### CQRS

| 规则 | 影响 | 描述 |
|------|------|------|
| [cqrs-pattern.md](rules/cqrs-pattern.md) | HIGH | 使用 Dedsi Framework 的 CQRS 组件实现命令处理和事件发布 |

### Repository

| 规则 | 影响 | 描述 |
|------|------|------|
| [repository-pattern.md](rules/repository-pattern.md) | HIGH | 使用 Dedsi Framework 的 Repository 接口实现数据访问 |

### 审计

| 规则 | 影响 | 描述 |
|------|------|------|
| [auditing-configuration.md](rules/auditing-configuration.md) | MEDIUM | 使用 Dedsi Framework 的审计接口和属性设置器实现自动审计 |

---

## 效率规则

这些规则帮助 AI 更有效、更一致地解决问题。

### Web API

| 规则 | 影响 | 描述 |
|------|------|------|
| [exception-handling.md](rules/exception-handling.md) | MEDIUM | 在项目中正确配置 Dedsi Global Exception Handler 以统一错误响应格式 |
| [file-export.md](rules/file-export.md) | LOW | 使用 DedsiControllerBase 快速实现 Excel/PDF 导出功能 |

### 数据库

| 规则 | 影响 | 描述 |
|------|------|------|
| [sqlsugar-configuration.md](rules/sqlsugar-configuration.md) | MEDIUM | 在项目中正确配置和使用 SqlSugar ORM |

### DTO

| 规则 | 影响 | 描述 |
|------|------|------|
| [paged-request-dto.md](rules/paged-request-dto.md) | MEDIUM | 使用 DedsiPagedRequestDto 实现统一的分页请求 |

---

## 模块速查

### Dedsi.AspNetCore
- `DedsiControllerBase`：控制器基类，提供 `FileByExcel`、`FileByPdf` 方法
- `DedsiGlobalExceptionHandler`：全局异常处理中间件
- `DedsiErrorData`、`DedsiErrorMessage`：错误响应结构

### Dedsi.Ddd.Domain
- `IDedsiCreationAuditedObject`：创建审计接口
- `IDedsiCqrsRepository<TDomain, TKey>`：CQRS 仓储接口
- `IDedsiRepository<TEntity, TKey>`：通用仓储接口
- `IDedsiQuery`：查询标记接口

### Dedsi.Ddd.CQRS
- `DedsiMediator`：中介者（自动记录命令）
- `DedsiCommand`、`DedsiCommand<TResponse>`：命令基类
- `DedsiCommandHandler<TCommand>`：命令处理器基类
- `DedsiLocalEventBus`：本地事件总线（自动记录事件）
- `DedsiEvent`：事件基类
- `ICommandEventRecorderRepository`：命令/事件记录仓储

### Dedsi.EntityFrameworkCore
- `DedsiDddEfCoreRepository<TDbContext, TDomain, TKey>`：CQRS 仓储实现
- `DedsiEfCoreRepository<TDbContext, TEntity, TKey>`：通用仓储实现
- `DedsiAuditPropertySetter`：审计属性设置器

### Dedsi.SqlSugar
- `DedsiSqlSugarExtensions`：SqlSugar 配置扩展
- `IDedsiSqlSugarQuery`：SqlSugar 查询接口

### Dedsi.Ddd.Application.Contracts
- `DedsiPagedRequestDto`：分页请求基类
- `DedsiPagedResultDto<TRowDto>`：分页结果基类

### Dedsi.Core
- 扩展工具类：`EnumExtensions`、`DateTimeExtensions`、`StringExtensions` 等

---

## 技术栈

- **框架**：ABP Framework
- **语言**：C# / .NET
- **ORM**：Entity Framework Core、SqlSugar
- **CQRS**：MediatR
- **分布式事务**：DotNetCore.CAP
- **轻量级 API**：FastEndpoints

---

## 源代码信息

- **源代码目录**：`/Users/cohen/GitHub/DedsiFramework/src`
- **文件数量**：64 个 .cs 文件（忽略 bin/obj 目录）
- **模块数量**：12 个模块

---

## 排错指南

### "不知道该引用哪个库"
1. 查看模块速查，定位类型所在模块
2. 检查项目是否引用了对应 NuGet 包/项目引用
3. 检查 ABP `DependsOn` 是否添加

### "异常没有变成 DedsiErrorData"
1. 确认期望的错误响应格式（ABP 默认 vs DedsiErrorData）
2. 若使用 DedsiErrorData，检查 `DedsiGlobalExceptionHandler` 是否注册
3. 检查业务层是否抛出 `BusinessException` / `UserFriendlyException`

### "CreatorName 没有自动填充"
1. 确认使用 EF Core 集成
2. 检查实体是否实现了 `IDedsiHasCreationName`、`IDedsiHasCreationId` 或 `IDedsiHasCreationTime`
3. 检查 `CurrentUser.Name` 和 `CurrentUser.Id` 是否有值

### "CQRS 记录没有落库/没有输出"
1. 确认发命令是否通过 `DedsiMediator`，发事件是否通过 `DedsiLocalEventBus`
2. 确认 `ICommandEventRecorderRepository` 是否有实现并注册
3. 开发期可使用 `LocalCommandEventRecorderRepository` 验证

### "分页查询结果不正确"
1. 检查 `PageIndex` 和 `PageSize` 的值（PageSize 限制在 1-1000）
2. 检查 `GetPagedListAsync` 的 `isReverse` 参数
3. 检查排序字段是否正确

### "SqlSugar 日志不输出"
1. 检查当前环境是否为 `Development`（只有在 `Development` 环境下才会输出 SQL 日志）
2. 检查 `connectionConfig` 配置是否正确

---

## 参考资源

- [DedsiFramework 源代码](/Users/cohen/GitHub/DedsiFramework/src) - 完整源代码
- [ABP Framework](https://abp.io/) - ABP 官方文档
- [dedsiddd-dotnet-coding](../dedsiddd-dotnet-coding/) - DedsiDDD 代码生成规范
