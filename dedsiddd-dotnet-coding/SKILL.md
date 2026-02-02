---
name: dedsiddd-dotnet-coding
description: 用于在 DedsiDDD + .NET 项目中，按统一约定生成 DDD/CQRS 常见代码骨架（领域模型、DTO、EF Core 映射、仓储、Commands、Queries、Controller）。
---

# DedsiDDD .NET Coding

DedsiDDD + .NET 项目代码生成规范，遵循 DDD/CQRS 架构模式和统一约定。

## 何时使用

- 在 DedsiDDD + .NET 项目中生成新实体/聚合根
- 创建 CQRS Command/Query 处理器
- 配置 EF Core 映射和 DbContext
- 实现 Repository
- 创建 Controller 和 API 端点
- 定义 DTO 数据传输对象
- 遵循 DedsiDDD 约定进行代码审查

---

## 能力规则

没有这些规则，AI 无法解决问题或生成的代码无法正确运行。

### 领域模型

| 规则 | 影响 | 描述 |
|------|------|------|
| [aggregate-root-patterns.md](rules/aggregate-root-patterns.md) | HIGH | 定义聚合根遵循 DedsiDDD 约定 |
| [entity-creation-validation.md](rules/entity-creation-validation.md) | HIGH | 实体创建和验证模式 |
| [collection-management.md](rules/collection-management.md) | MEDIUM | 集合管理和维护 |

### DTO

| 规则 | 影响 | 描述 |
|------|------|------|
| [dto-conventions.md](rules/dto-conventions.md) | MEDIUM | DTO 命名和结构约定 |
| [dto-xml-comments.md](rules/dto-xml-comments.md) | MEDIUM | DTO XML 文档注释 |

### 数据库

| 规则 | 影响 | 描述 |
|------|------|------|
| [dbcontext-configuration.md](rules/dbcontext-configuration.md) | HIGH | DbContext 配置约定 |
| [ef-core-mapping.md](rules/ef-core-mapping.md) | HIGH | EF Core 实体映射 |

### 仓储

| 规则 | 影响 | 描述 |
|------|------|------|
| [repository-patterns.md](rules/repository-patterns.md) | HIGH | Repository 实现模式 |

### CQRS

| 规则 | 影响 | 描述 |
|------|------|------|
| [command-handlers.md](rules/command-handlers.md) | HIGH | Command Handler 实现模式 |
| [query-handlers.md](rules/query-handlers.md) | HIGH | Query Handler 实现模式 |
| [pagination-queries.md](rules/pagination-queries.md) | HIGH | 分页查询实现 |

### Controller

| 规则 | 影响 | 描述 |
|------|------|------|
| [controller-patterns.md](rules/controller-patterns.md) | HIGH | Controller 实现模式 |

### 通用

| 规则 | 影响 | 描述 |
|------|------|------|
| [cancellation-token.md](rules/cancellation-token.md) | HIGH | CancellationToken 全链路传递 |
| [enum-conventions.md](rules/enum-conventions.md) | MEDIUM | 枚举显式赋值约定 |
| [file-organization.md](rules/file-organization.md) | MEDIUM | 文件组织规范 |
| [naming-conventions.md](rules/naming-conventions.md) | MEDIUM | 命名约定 |
| [project-structure.md](rules/project-structure.md) | HIGH | DDD 分层架构 |

---

## 效率规则

这些规则帮助 AI 更有效、更一致地解决问题。

### 质量保证

| 规则 | 影响 | 描述 |
|------|------|------|
| [common-pitfalls.md](rules/common-pitfalls.md) | HIGH | 常见错误和验收清单 |

---

## 交互协议

### 冲突优先级

本 SKILL 约定 > 现有仓库约定 > 一般 DDD/CQRS 习惯

### 澄清问题清单

在生成代码前，必须先澄清以下问题：

1. **模块边界**：实体属于哪个模块？命名空间与目录？
2. **路由风格**：Controller 使用 action route 还是资源路由？
3. **聚合信息**：实体单/复数名、主键类型（默认 string/ULID）、是否聚合根
4. **字段清单**：字段名/类型/必填/长度/默认值/enum/值对象
5. **关系与集合**：一对多/多对多、集合唯一性约束、更新策略（增量 vs Clear+Add）
6. **数据库映射**：表名、Schema、索引/唯一约束、是否软删
7. **API 契约**：需要哪些端点（Get/Paged/Create/Update/Delete/Export）
8. **返回结构**：是否有统一的 ApiResponse/Result 包装？

### 缺省策略

- **主键**：默认 `string`，生成方式 `Ulid.NewUlid().ToString()`
- **分页**：使用 `DedsiPagedRequestDto`
- **更新集合**：默认 Clear + Add
- **导出组件**：仅当仓库已存在 MiniExcel 时提供落地代码

### 交付标准

- 明确的文件清单与落点（Domain/Infrastructure/UseCase/HttpApi）
- 每个文件的代码模板或最小变更补丁
- 所有公开入口包含 `CancellationToken` 并透传到 EF Core/仓储
- 分页查询顺序：筛选 → Count → 排序 →（非导出则分页）→ 投影 → ToList
- Controller 能编译并能调用 Query/Mediator；Command/Query 返回类型与 Controller 对齐

### 占位符规则

必须替换的占位符：
- `[Project]` - 项目名称
- `[Domain]` - 领域实体名称
- `[Entities]` - 实体复数形式
- `[DbContext]` - DbContext 名称
- `[Child]` - 子实体名称

---

## 推荐工作流

1. 澄清边界与不变式
2. 写领域模型（Domain）
3. 定义 DTO（Contract）
4. 配置 EF Core（DbContext + EntityConfiguration）
5. 生成仓储（Repository）
6. 实现 Commands（写侧）
7. 实现 Queries（读侧）
8. 实现 Controller（API 层）
9. 一致性检查

---

## 参考

- [DedsiFramework.md](../../DedsiFramework.md) - Dedsi Framework 完整文档
- [dedsi-framework](../dedsi-framework/) - Dedsi Framework 接入指南
