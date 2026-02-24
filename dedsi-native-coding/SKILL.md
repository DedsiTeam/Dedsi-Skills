---
name: dedsi-native-coding
description: DedsiNative (.NET 10) DDD/Clean Architecture Web API 开发规范、模板与排错指引。用于在 content/src 下新增或维护 Domain/Infrastructure/Operation/HttpApi 代码、补全 EF Core 配置/仓储/Minimal API 端点，或排查 DI/数据库/异常处理中间件问题。
---

# DedsiNative 开发

## Quick start
1. 确认实体名、主键类型、路由资源名。
2. 按层次顺序修改：Domain -> Infrastructure -> Operation -> HttpApi。
3. 需要模板或路径细节时，打开 `references/templates.md` 与 `references/architecture.md`。

## Project structure
- 仅在 `content/src` 中增删改代码。
- 不要修改 `content/frameworks`。
- 需要项目结构或运行管线细节时，打开 `references/architecture.md`。

## Workflow
1. 创建领域实体与仓储接口。
2. 添加 EF Core 实体配置与 DbContext 的 DbSet。
3. 实现仓储。
4. 编写 Operation（创建、更新、删除、查询、条件分页）。
5. 添加 Minimal API 端点并在 `DedsiNativeEndpoints` 中注册。
6. 必要时补充数据库迁移或调整配置。

## Conventions
- 保持实体属性 `private set`，通过 `Change*` 方法做校验与修改。
- 输入 DTO 使用 `record`，输出 DTO 使用 `class`。
- 查询使用 `WhereIf` 与 `PagedBy` 扩展方法。
- 让 Operation 实现 `IDedsiNativeOperation`，仓储类名以 `Repository` 结尾，依赖注入由 Scrutor 扫描完成。
- 路由使用 kebab-case，组内 `.WithTags("{EntityName}")`。
- 不要吞异常，让 `GlobalExceptionHandlingMiddleware` 统一处理。

## References
- `references/architecture.md`
- `references/templates.md`
