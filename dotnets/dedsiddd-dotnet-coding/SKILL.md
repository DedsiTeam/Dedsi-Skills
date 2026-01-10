---
name: dedsiddd-dotnet-coding
description: 用于在 DedsiDDD + .NET 项目中，按统一约定生成 DDD/CQRS 常见代码骨架（领域模型、DTO、EF Core 映射、仓储、Commands、Queries、Controller）。
---

## 项目结构（Clean Architecture CQRS）

> 本节用于快速对齐“代码应该放在哪一层/哪个项目”。具体版本与文件名以实际仓库为准（例如 `global.json`、`Directory.Build.props`、`Directory.Build.targets`）。

### 概览
- .NET SDK：10.0.100
- 目标框架：net10.0（来自 `Directory.Build.props`）
- 语言版本：latest（来自 `Directory.Build.props`）
- 集中管理的包/版本（来自 `Directory.Build.targets`）：
    - `MicrosoftVersion`：10.0.0
    - `DedsiFrameworkVersion`：10.0.0
    - `AbpFrameworkVersion`：10.0.0

### 架构分层
- 领域（`src/[Project].Domain`）
    - 核心领域对象、常量、领域模块。
    - 外部包：`Dedsi.CleanArchitecture.Domain`（`$(DedsiFrameworkVersion)`）。
- 基础设施（`src/[Project].Infrastructure`）
    - EF Core 持久化与集成；包含 `DbContext` 与模型构建扩展。
    - 依赖：Domain。
    - 外部包：`Dedsi.CleanArchitecture.Infrastructure`（`$(DedsiFrameworkVersion)`）。
- 用例（UseCase，`src/[Project].UseCase`）
    - 应用层（CQRS 命令/查询与编排）。
    - 依赖：Domain、Infrastructure。
    - 外部包：`Dedsi.Ddd.CQRS`（`$(DedsiFrameworkVersion)`）。
- HttpApi（`src/[Project].HttpApi`）
    - 暴露控制器的 API 层，启用 XML 文档。
    - 依赖：UseCase。
    - 外部包：`Dedsi.CleanArchitecture.HttpApi`（`$(DedsiFrameworkVersion)`）。
- 宿主（`host/[Project].Host`）
    - ASP.NET Core Web 宿主；配置、日志、认证、Swagger、模块装配。
    - 依赖：HttpApi。
    - 常见关键包：`Swashbuckle.AspNetCore`、`Microsoft.AspNetCore.Authentication.JwtBearer`、`Volo.Abp.*`、`Serilog.AspNetCore`、`Serilog.Sinks.Async`。

### 项目依赖关系图
- `[Project].Host` → `[Project].HttpApi`
- `[Project].HttpApi` → `[Project].UseCase`
- `[Project].UseCase` → `[Project].Domain`、`[Project].Infrastructure`
- `[Project].Infrastructure` → `[Project].Domain`
- `[Project].Domain` →（无本地项目引用）

### 目录树（关键文件）
```text
.
├─ [Project].sln
├─ global.json
├─ Directory.Build.props
├─ Directory.Build.targets
├─ host/
│  └─ [Project].Host/
│     ├─ [Project].Host.csproj
│     ├─ Program.cs
│     ├─ [Project]HostModule.cs
│     ├─ index.html (EmbeddedResource)
│     ├─ Dockerfile
│     ├─ appsettings.json
│     ├─ appsettings.Development.json
│     ├─ appsettings.Production.json
│     └─ Properties/
│        └─ launchSettings.json
└─ src/
     ├─ [Project].Domain/
     │  ├─ [Project].Domain.csproj
     │  ├─ [Project]DomainConsts.cs
     │  ├─ [Project]DomainModule.cs
     │  └─ [Entities]/
     │     └─ [Entity].cs
     ├─ [Project].Infrastructure/
     │  ├─ [Project].Infrastructure.csproj
     │  ├─ [Project]InfrastructureModule.cs
     │  ├─ Repositories/
     │  │  └─ [Entity]Repository.cs
     │  └─ EntityFrameworkCore/
     │     ├─ [Project]DbContext.cs
     │     └─ [Project]DbContextModelCreatingExtensions.cs
     ├─ [Project].UseCase/
     │  ├─ [Project].UseCase.csproj
     │  ├─ [Project]UseCaseModule.cs
     │  └─ [Entities]/
     │     ├─ CommandHandlers/
     │     ├─ Dtos/
     │     └─ Queries/
     └─ [Project].HttpApi/
            ├─ [Project].HttpApi.csproj
            ├─ [Entity]Controller.cs
            └─ [Project]HttpApiModule.cs
```

### 重要的构建/配置设置
- `global.json`
    - SDK `10.0.100`，`rollForward: latestFeature`。
- `Directory.Build.props`
    - `TargetFramework: net10.0`，`LangVersion: latest`，`ImplicitUsings: enable`，抑制 `CS1591`。
- `Directory.Build.targets`
    - 集中管理版本：`MicrosoftVersion`、`DedsiFrameworkVersion`、`AbpFrameworkVersion`。
- 宿主项目（`[Project].Host.csproj`）
    - 将 `index.html` 作为 `EmbeddedResource`。
    - 从多个 ItemGroup 中排除 `Logs/**`。
    - 引用 `src/[Project].HttpApi` 项目。
- HttpApi/UseCase 项目
    - 启用 `GenerateDocumentationFile` 以生成 XML 文档。

### 备注
- 仓库遵循整洁架构风格，依赖方向由外向内汇聚至领域层。
- 日志通常使用 Serilog；API 文档通常通过 Swashbuckle/Swagger；认证常用 JWT Bearer；模块化由 ABP 模块与 Autofac 提供支持。

### 相关提示
- DbContext：`src/[Project].Infrastructure/EntityFrameworkCore/[Project]DbContext.cs`
- Controller 基类/约定：`src/[Project].HttpApi/[Project]Controller.cs`（若存在）或同项目内现有 Controller 示例
- 生成文件命名空间：优先遵循项目现有模块/目录结构；若无既定约定再按“模块名 + 领域模型英文复数形式”统一

## 通用约定（全局规则）
- **命名占位符**：`[Entity]` / `[Project]` / `[DbContext]` 代表按实际业务替换。
- **文件聚合原则**：
    - Command 与 Handler 必须同文件。
    - Query 接口与实现必须同文件。
    - Repository 接口与实现必须同文件。
- **CancellationToken**：所有公开 API/Query 方法必须显式接收 `CancellationToken`，并向下透传到 EF Core/仓储。
- **XML 注释**：对外接口（Controller、Query 接口/实现、DTO）遵循项目现有风格补齐注释；实现类可用 `/// <inheritdoc />`。
- **Controller 路由约定提示**：若项目使用 `[Route(".../[controller]/[action]")]`，则 `UpdateAsync` 与 `DeleteAsync` 都可以使用 `[HttpPost("{id}")]`，因为 action 名不同不会冲突。

## 推荐工作流
1. **澄清边界与不变式**：确认聚合根 `[Entity]` 的职责、必填字段、状态机/校验规则、集合唯一性规则。
2. **先写领域模型（Domain Model）**：
    - 构造函数只初始化必需字段与默认值；外部修改通过 `ChangeXxx`/`AddXxx`/`RemoveXxx`/`ClearXxx`。
    - 需要的校验（`Check.*` / 业务异常）在领域层完成。
3. **定义 DTO（Contract）**：
    - 先定 `CreateUpdateDto`（写入契约），再定 `Dto`（展示契约）。
    - DTO 字段补齐 XML 注释。
4. **EF Core 注册与映射（DB Configuration）**：
    - `DbSet` 注册到 `[DbContext]`。
    - 为实体添加 `EntityTypeConfiguration`（表名/Schema/主键/索引/长度/必填/关系）。
5. **仓储（Repository）**：创建 `I[Entity]Repository` + 实现（同文件），注入 `IDbContextProvider<[DbContext]>`。
6. **写侧（Commands）**：Create/Update/Delete 命令与处理器同文件；更新时调用领域方法；集合更新遵循“先清空再添加”的一致策略（或按业务写增量更新，但需统一）。
7. **读侧（Queries）**：
    - 单条查询：通过仓储取领域对象并手动映射到 DTO。
    - 分页查询：`DedsiEfCoreQuery<[DbContext]>` + `WhereIf` + `CountAsync` + `PageBy` + `Select` 投影。
8. **Controller 暴露接口**：只做编排与透传（入参校验/调用 Query/Mediator/导出），不写业务规则。
9. **最后做一次一致性检查**：
    - 命名/路由/返回类型是否符合项目现有 Controller 模式。
    - `CancellationToken` 是否全链路透传。
    - Update/Query 示例中的 `id` 是否确实用于谓词。

## 目录
- [项目结构（Clean Architecture CQRS）](#项目结构clean-architecture-cqrs)
- [生成领域模型](#生成领域模型)
- [生成 DTO](#生成-dto)
- [配置数据库](#配置数据库)
- [生成仓储](#生成仓储)
- [生成命令 (Commands)](#生成命令-commands)
- [生成查询 (Queries)](#生成查询-queries)
- [生成控制器 (Controller)](#生成控制器-controller)

---

## 生成领域模型

### 何时使用
当需要在 Dedsi DDD 项目中创建新的领域实体或聚合根时。

### 规则
- 聚合根 **必须** 继承 `AggregateRoot<string>` 并实现 `IDedsiCreationAuditedObject`。
- `AggregateRoot` 的命名空间为 `Volo.Abp.Domain.Entities`。
- `IDedsiCreationAuditedObject` 的命名空间为 `Dedsi.Ddd.Domain.Auditing.Contracts`。
- **命名**：使用领域名称（例如 `Risk`）。
- **构造函数**：
  - 必须包含一个 `protected` 无参构造函数（供 ORM 使用）。
  - 必须包含一个 `public` 构造函数，用于初始化必填属性并设置默认值。
- **属性**：所有属性必须使用 `private set`。
- **方法**：
  - **领域行为优先**：属性不可被外部直接修改；所有状态变更必须通过聚合根方法完成。
  - **单值属性变更**：提供 `Change+属性名` 方法（例如 `ChangeRiskNumber`）。
    - 入参校验：
      - 字符串：`Check.NotNullOrWhiteSpace`；有长度限制则再用 `Check.Length`（或对应约束）。
      - 引用类型：`Check.NotNull`。
      - 枚举：仅允许合法值（例如 `Enum.IsDefined` / 约束集合）。
    - **幂等**：新旧值相同（或语义等同）直接返回。
    - **维护不变式**：不满足业务不变式时抛 `BusinessException`，避免进入非法状态。
    - `Check.*` 的 `paramName` 建议使用 `nameof(参数)`。
  - **集合属性变更**：集合在声明处初始化为非空，并提供：
    - `Add+元素名`：`Check.NotNull`；如需唯一性则防重复（忽略或抛异常，保持一致）。
    - `Remove+元素名`：允许“未找到即无操作”的幂等；如业务要求可改为抛异常。
    - `Clear+集合名`：如清空会破坏不变式必须阻止并抛异常。
  - 方法命名体现业务含义，避免 `SetXxx`；XML 注释描述业务目的。

### 模板参考
```csharp
using Dedsi.Ddd.Domain.Auditing.Contracts;
using Volo.Abp;
using Volo.Abp.Domain.Entities;

/// <summary>
/// 风险
/// </summary>
public class Risk : AggregateRoot<string>, IDedsiCreationAuditedObject
{
    /// <summary>
    /// 构造函数，初始化必填属性与默认值
    /// </summary>
    protected Risk()
    {
    }

    /// <summary>
    /// 构造函数，初始化必填属性与默认值
    /// </summary>
    public Risk(
        string id,
        string riskNumber,
        string? involvingProject)
        : base(id)
    {
        ChangeRiskNumber(riskNumber);
        ChangeInvolvingProject(involvingProject);

        CreationTime = DateTime.Now;
    }

    public string? CreatorName { get; private set; }

    public Guid? CreatorId { get; private set; }

    public DateTime CreationTime { get; private set; }

    /// <summary>
    /// 数据状态
    /// </summary>
    public RiskDataStatus DataStatus { get; private set; }

    /// <summary>
    /// 设置数据状态
    /// </summary>
    public void ChangeDataStatus(RiskDataStatus dataStatus)
    {
        DataStatus = dataStatus;
    }

    /// <summary>
    /// 风险编号
    /// </summary>
    public string RiskNumber { get; private set; }

    /// <summary>
    /// 更改风险编号
    /// </summary>
    public void ChangeRiskNumber(string riskNumber)
    {
        RiskNumber = Check.NotNullOrWhiteSpace(riskNumber, nameof(riskNumber));
    }

    /// <summary>
    /// 涉及项目
    /// </summary>
    public string? InvolvingProject { get; private set; }

    /// <summary>
    /// 更改涉及项目
    /// </summary>
    public void ChangeInvolvingProject(string? involvingProject)
    {
        InvolvingProject = involvingProject;
    }

    /// <summary>
    /// 评审人
    /// </summary>
    public ICollection<RiskReviewer> Reviewers { get; private set; } = [];

    public void AddReviewer(RiskReviewer reviewer)
    {
        Reviewers.Add(reviewer);
    }

    public void RemoveReviewer(RiskReviewer reviewer)
    {
        Reviewers.Remove(reviewer);
    }

    public void ClearReviewers()
    {
        Reviewers.Clear();
    }
}
```

---

## 生成 DTO

### 何时使用
用于创建 API 响应（`Dto`）和 API 请求（`CreateUpdateDto`）的 DTO。

### 规则
- **展示 DTO**：
  - 命名：`[EntityName]Dto`。
  - 对照领域模型检查属性，确保公开所有必填字段。
  - 使用 `public get; set;`。
- **创建/更新 DTO**：
  - 命名：`[EntityName]CreateUpdateDto`。
  - 如有必要，包含验证属性。
  - 使用 `public get; set;`。
- **字段注释（强制）**：DTO 的每个字段/属性都必须有 XML 注释（`/// <summary>...</summary>`）。

### 模板参考
```csharp
/// <summary>
/// 风险Dto
/// </summary>
public class RiskDto
{
  /// <summary>
  /// 标识
  /// </summary>
  public string Id { get; set; }

  /// <summary>
  /// 创建人名称
  /// </summary>
  public string? CreatorName { get; set; }

  /// <summary>
  /// 创建人Id
  /// </summary>
  public Guid? CreatorId { get; set; }

  /// <summary>
  /// 创建时间
  /// </summary>
  public DateTime CreationTime { get; set; }

  /// <summary>
  /// 数据状态
  /// </summary>
  public RiskDataStatus DataStatus { get; set; }

  /// <summary>
  /// 风险编号
  /// </summary>
  public string RiskNumber { get; set; }

  /// <summary>
  /// 涉及项目
  /// </summary>
  public string? InvolvingProject { get; set; }

  /// <summary>
  /// 评审人列表
  /// </summary>
  public IEnumerable<RiskReviewer> Reviewers { get; set; } = [];
}

/// <summary>
/// 风险创建Dto
/// </summary>
public class RiskCreateUpdateDto
{
  /// <summary>
  /// 数据状态
  /// </summary>
  public RiskDataStatus DataStatus { get; set; }

  /// <summary>
  /// 风险编号
  /// </summary>
  public string RiskNumber { get; set; }

  /// <summary>
  /// 涉及项目
  /// </summary>
  public string? InvolvingProject { get; set; }

  /// <summary>
  /// 评审人列表
  /// </summary>
  public IEnumerable<RiskReviewer> Reviewers { get; set; } = [];
}
```

---

## 配置数据库

### 何时使用
当需要在 `DbContext` 中注册新实体并配置其 EF Core 映射时。

### 规则

#### DbSet
- **命名**：聚合根复数形式（例如 `Risks`）。
- **类型**：`DbSet<聚合根>`。
- **位置**：`DbContext` 接口与实现（`I[Project]DbContext` / `[Project]DbContext`）。
- **连接字符串**：接口与实现类上标注 `[ConnectionStringName([Project]DomainConsts.ConnectionStringName)]`。
- **基类/接口**：
  - `I[Project]DbContext : IDedsiEfCoreDbContext`
  - `[Project]DbContext : DedsiEfCoreDbContext<[Project]DbContext>, I[Project]DbContext`
- **构造函数**（与模板一致）：`public [Project]DbContext(DbContextOptions<[Project]DbContext> options) : base(options) { }`
- **属性形态**：
  - 接口：`DbSet<[Entity]> [Entities] { get; }`
  - 实现：`public DbSet<[Entity]> [Entities] { get; set; }`
- **应用配置**：在 `OnModelCreating` 中：空值校验 -> `ApplyConfigurationsFromAssembly` -> `base.OnModelCreating(modelBuilder)`。

#### EntityConfigurations（模型映射）
- 每个实体一个配置类：`[EntityName]Configuration`，放在 `EntityFrameworkCore/EntityConfigurations`。
- 命名空间：`[Project].EntityFrameworkCore.EntityConfigurations`。
- 建议 `internal class`。
- `using`：`Microsoft.EntityFrameworkCore`（必要时补 `Microsoft.EntityFrameworkCore.Metadata.Builders`）。
- 实现 `IEntityTypeConfiguration<[EntityName]>`，在 `Configure` 里配置：表名、Schema、主键等。

### 模板参考
```csharp
using Dedsi.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;
using Volo.Abp.Data;

namespace ProjectNameCQRS.EntityFrameworkCore;

[ConnectionStringName(ProjectNameCQRSDomainConsts.ConnectionStringName)]
public interface IProjectNameCQRSDbContext: IDedsiEfCoreDbContext
{
    /// <summary>
    /// 风险
    /// </summary>
    DbSet<Risk> Risks { get; }
}

[ConnectionStringName(ProjectNameCQRSDomainConsts.ConnectionStringName)]
public class ProjectNameCQRSDbContext(DbContextOptions<ProjectNameCQRSDbContext> options)
    : DedsiEfCoreDbContext<ProjectNameCQRSDbContext>(options), IProjectNameCQRSDbContext
{
    /// <summary>
    /// 风险
    /// </summary>
    public DbSet<Risk> Risks { get; set; }


    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        if (modelBuilder is null)
        {
            throw new ArgumentNullException(nameof(modelBuilder));
        }

        modelBuilder.ApplyConfigurationsFromAssembly(typeof(ProjectNameCQRSDbContext).Assembly);
        base.OnModelCreating(modelBuilder);
    }
}
```

```csharp
using DedsiDemo.Students;
using Microsoft.EntityFrameworkCore;

namespace DedsiDemo.EntityFrameworkCore.EntityConfigurations;

internal class DeliverRecordConfiguration : IEntityTypeConfiguration<DeliverRecord>
{
    public void Configure(EntityTypeBuilder<DeliverRecord> builder)
    {
        builder.ToTable("DeliverRecords", DedsiDemoDomainConsts.DbSchemaName);
        builder.HasKey(t => t.Id);
    }
}
```

---

## 生成仓储

### 何时使用
为聚合根创建数据访问层（仓储）。

### 规则
- **接口**：
  - 命名：`I[AggregateRoot]Repository`（例如 `IRiskRepository`）。
  - 继承：`IDedsiCqrsRepository<AggregateRoot, KeyType>`。
  - 命名空间：`Dedsi.Ddd.Domain.Repositories`。
- **实现**：
  - 命名：`[AggregateRoot]Repository`（例如 `RiskRepository`）。
  - 继承：`DedsiCqrsEfCoreRepository<DbContext, AggregateRoot, KeyType>`。
  - 命名空间：`Dedsi.EntityFrameworkCore.Repositories`。
  - 实现接口。
- **文件结构**：接口和实现 **必须** 在同一个文件中。
- **构造函数**：注入 `IDbContextProvider<DbContext>`。

### 模板参考
```csharp
using Dedsi.Ddd.Domain.Repositories;
using Dedsi.EntityFrameworkCore.Repositories;
using Volo.Abp.EntityFrameworkCore;

namespace SafetyEnvProtection.Repositories;

/// <summary>
/// 字典数据仓储
/// </summary>
public interface IDictionaryDataRepository : IDedsiCqrsRepository<DictionaryData, string>;

/// <summary>
/// 字典数据仓储
/// </summary>
/// <param name="dbContextProvider"></param>
public class DictionaryDataRepository(IDbContextProvider<SafetyEnvProtectionDbContext> dbContextProvider)
    : DedsiCqrsEfCoreRepository<SafetyEnvProtectionDbContext, DictionaryData, string>(dbContextProvider), IDictionaryDataRepository;
```

---

## 生成命令 (Commands)

### 何时使用
实现 CQRS 写操作（Write side）：创建、更新、删除。

### 规则
- 命令和处理器 **必须** 在同一个文件中。

#### 创建命令 (Create Command)
- **命令**：record `Create[Entity]Command(CreateUpdateDto Dto)` : `DedsiCommand<string>`。
- **处理器**：继承 `DedsiCommandHandler<Create[Entity]Command, string>`。
- **逻辑**：生成新的 ULID -> 创建领域对象 -> `Repository.InsertAsync` -> 返回 ID。

#### 更新命令 (Update Command)
- **命令**：record `Update[Entity]Command(string Id, CreateUpdateDto Dto)` : `DedsiCommand<bool>`。
- **处理器**：继承 `DedsiCommandHandler<Update[Entity]Command, bool>`。
- **逻辑**：`Repository.GetAsync` -> 调用领域方法修改 -> `Repository.UpdateAsync` -> 返回 true。
- **集合**：先 `Clear` 再 `Add`。

#### 删除命令 (Delete Command)
- **命令**：record `Delete[Entity]Command(string Id)` : `DedsiCommand<bool>`。
- **处理器**：继承 `DedsiCommandHandler<Delete[Entity]Command, bool>`。
- **逻辑**：`Repository.DeleteAsync` -> 返回 true。

### 模板参考
```csharp
using Dedsi.Ddd.CQRS.CommandHandlers;
using Dedsi.Ddd.CQRS.Commands;
using Dedsi.Ddd.CQRS.Mediators;
using Volo.Abp;

// Create
public record CreateRiskCommand(RiskCreateUpdateDto RiskDto): DedsiCommand<string>;

public class CreateRiskCommandHandler(IRiskRepository riskRepository) : DedsiCommandHandler<CreateRiskCommand, string>
{
    public override async Task<string> Handle(CreateRiskCommand command, CancellationToken cancellationToken)
    {
        var domainId = Ulid.NewUlid().ToString();
        var domain = new Risk(domainId, command.RiskDto.RiskNumber, command.RiskDto.InvolvingProject);
        await riskRepository.InsertAsync(domain, true, cancellationToken);
        return domainId;
    }
}

// Update
public record UpdateRiskCommand(string Id, RiskCreateUpdateDto RiskDto) : DedsiCommand<bool>;

public class UpdateRiskCommandHandler(IRiskRepository riskRepository) : DedsiCommandHandler<UpdateRiskCommand, bool>
{
    public override async Task<bool> Handle(UpdateRiskCommand command, CancellationToken cancellationToken)
    {
        var domain = await riskRepository.GetAsync(command.Id, cancellationToken: cancellationToken);
        domain.ChangeRiskNumber(command.RiskDto.RiskNumber);

        domain.ClearReviewers();
        foreach (var reviewer in command.RiskDto.Reviewers)
        {
            domain.AddReviewer(reviewer);
        }

        await riskRepository.UpdateAsync(domain, true, cancellationToken);
        return true;
    }
}

// Delete
public record DeleteRiskCommand(string Id) : DedsiCommand<bool>;

public class DeleteRiskCommandHandler(IRiskRepository riskRepository) : DedsiCommandHandler<DeleteRiskCommand, bool>
{
    public override async Task<bool> Handle(DeleteRiskCommand command, CancellationToken cancellationToken)
    {
        await riskRepository.DeleteAsync(a => a.Id == command.Id, true, cancellationToken);
        return true;
    }
}
```

---

## 生成查询 (Queries)

### 何时使用
实现 CQRS 读操作（Read side）：单条查询与分页查询。

### 规则
- **接口与实现**：每种查询类型必须在同一个文件中。
- **分页查询与 EF Core 查询基类**：使用 `DedsiEfCoreQuery<[DbContext]>`，通过 `GetDbContextAsync()` 获取 DbContext。
- **注释（强制）**：所有 Query（接口 / class / 方法）都必须有 XML 注释；实现类与其实现方法统一使用 `/// <inheritdoc />`。
- **CancellationToken（强制）**：所有公开查询方法必须显式接收 `CancellationToken cancellationToken`，并传递给 EF Core/仓储调用。

#### 单个查询 (Single Query)
- **接口**：`I[Entity]Query : IDedsiQuery`。
- **方法**：`Task<[Entity]Dto> GetAsync(string id, CancellationToken cancellationToken)`。
- **实现**：`[Entity]Query : I[Entity]Query`。
- **依赖注入**：优先注入仓储 `I[Entity]Repository`；确需 DbContext 才注入相关 Provider/DbContext。
- **逻辑**：
  - `await repository.GetAsync(predicate, includeDetails: true, cancellationToken)`。
  - 手动映射到 `[Entity]Dto`。
  - `id` 入参必须用于谓词。

#### 分页查询 (Paged Query)
- **入参**：`[Entity]PagedInputDto : DedsiPagedRequestDto`。
- **行 DTO**：`[Entity]PagedRowDto`。
- **结果**：`[Entity]PagedResultDto : DedsiPagedResultDto<RowDto>`。
- **接口**：`I[Entity]PagedQuery : IDedsiQuery`。
- **方法**：`Task<[Entity]PagedResultDto> PagedQueryAsync([Entity]PagedInputDto input, CancellationToken cancellationToken)`。
- **DTO 要求**：
  - `PagedInputDto` 必须包含 `IsExport`（导出时不分页）以及必要筛选字段。
  - 每个 DTO 字段/属性都必须有 XML 注释。
- **实现**：`[Entity]PagedQuery : DedsiEfCoreQuery<[DbContext]>, I[Entity]PagedQuery`。
- **依赖注入**：`IDbContextProvider<[DbContext]> dbContextProvider`（必要时 `ICurrentUser`）。
- **逻辑步骤（模板）**：
  - `GetDbContextAsync()`
  - `WhereIf` 应用筛选条件
  - `CountAsync(cancellationToken)`
  - `OrderByDescending(...)`
  - `if (!input.IsExport) query = query.PageBy(input.GetSkipCount(), input.PageSize)`
  - `Select` 投影到 RowDto
  - `ToListAsync(cancellationToken)`
  - 返回 `new ... { TotalCount = totalCount, Items = items }`

### 模板参考
```csharp
// Paged DTOs

/// <summary>
/// 风险分页查询入参
/// </summary>
public class RiskPagedInputDto : DedsiPagedRequestDto
{
    /// <summary>
    /// 是否导出（导出时不分页）
    /// </summary>
    public bool IsExport { get; set; }

    /// <summary>
    /// 风险编号
    /// </summary>
    public string? RiskNumber { get; set; }

    /// <summary>
    /// 涉及项目
    /// </summary>
    public string? InvolvingProject { get; set; }
}

/// <summary>
/// 风险分页列表行
/// </summary>
public class RiskPagedRowDto
{
    /// <summary>
    /// 主键Id
    /// </summary>
    public string Id { get; set; }

    /// <summary>
    /// 创建人姓名
    /// </summary>
    public string? CreatorName { get; set; }

    /// <summary>
    /// 创建人Id
    /// </summary>
    public Guid? CreatorId { get; set; }

    /// <summary>
    /// 创建时间
    /// </summary>
    public DateTime CreationTime { get; set; }

    /// <summary>
    /// 数据状态
    /// </summary>
    public RiskDataStatus DataStatus { get; set; }

    /// <summary>
    /// 风险编号
    /// </summary>
    public string RiskNumber { get; set; }

    /// <summary>
    /// 涉及项目
    /// </summary>
    public string? InvolvingProject { get; set; }
}

/// <summary>
/// 风险分页查询结果
/// </summary>
public class RiskPagedResultDto : DedsiPagedResultDto<RiskPagedRowDto>
{
}

/// <summary>
/// 风险分页查询
/// </summary>
public interface IRiskPagedQuery: IDedsiQuery
{
    /// <summary>
    /// 分页查询
    /// </summary>
    Task<RiskPagedResultDto> PagedQueryAsync(RiskPagedInputDto input, CancellationToken cancellationToken);
}

/// <inheritdoc />
public class RiskPagedQuery(
    ICurrentUser currentUser,
    IDbContextProvider<SafetyEnvProtectionDbContext> dbContextProvider)
    : DedsiEfCoreQuery<SafetyEnvProtectionDbContext>(dbContextProvider), IRiskPagedQuery
{
    /// <inheritdoc />
    public async Task<RiskPagedResultDto> PagedQueryAsync(RiskPagedInputDto input, CancellationToken cancellationToken)
    {
        var dbContext = await GetDbContextAsync();
        var query = dbContext.Risks
            .WhereIf(!string.IsNullOrWhiteSpace(input.RiskNumber), a => a.RiskNumber.Contains(input.RiskNumber))
            .WhereIf(!string.IsNullOrWhiteSpace(input.InvolvingProject), a => a.InvolvingProject.Contains(input.InvolvingProject));

        var totalCount = await query.CountAsync(cancellationToken);
        query = query.OrderByDescending(a => a.CreationTime);

        if (!input.IsExport)
        {
            query = query.PageBy(input.GetSkipCount(), input.PageSize);
        }

        var items = await query
        .Select(a => new RiskPagedRowDto()
        {
            Id = a.Id,
            CreatorName = a.CreatorName,
            CreatorId = a.CreatorId,
            CreationTime = a.CreationTime,
            DataStatus = a.DataStatus,
            RiskNumber = a.RiskNumber,
            InvolvingProject = a.InvolvingProject
        })
        .ToListAsync(cancellationToken);

        return new RiskPagedResultDto { TotalCount = totalCount, Items = items };
    }
}

/// <summary>
/// 风险单个查询
/// </summary>
public interface IRiskQuery : IDedsiQuery
{
    /// <summary>
    /// 获取详情
    /// </summary>
    Task<RiskDto> GetAsync(string id, CancellationToken cancellationToken);
}

/// <inheritdoc />
public class RiskQuery(
    IRiskRepository riskRepository): IRiskQuery
{
    /// <inheritdoc />
    public async Task<RiskDto> GetAsync(string id, CancellationToken cancellationToken)
    {
        var domain = await riskRepository.GetAsync(a => a.Id == id, true, cancellationToken);

        var dto = new RiskDto()
        {
            CreatorName = domain.CreatorName,
            CreatorId = domain.CreatorId,
            CreationTime = domain.CreationTime,
            DataStatus = domain.DataStatus,
            RiskNumber = domain.RiskNumber,
            InvolvingProject = domain.InvolvingProject,
            Reviewers = domain.Reviewers
        };

        return dto;
    }
}
```

---

## 生成控制器 (Controller)

### 何时使用
为实体创建 API 端点，暴露 CQRS 功能。

### 规则
- **名称**：`[Entity]Controller`。
- **继承**：继承项目的基础控制器（例如 `SafetyEnvProtectionController`）。
- **依赖**：注入 `I[Entity]Query`、`I[Entity]PagedQuery`、`IDedsiMediator`。
- **端点**：
  - `PagedQueryAsync` `[HttpPost]`：调用 PagedQuery。
  - `ExportExcelAsync` `[HttpPost]`：调用 PagedQuery 并设置 `IsExport=true`，使用 `MiniExcel` 返回 Excel。
  - `GetAsync` `[HttpGet("{id}")]`：调用 `Query.GetAsync`。
  - `CreateAsync` `[HttpPost]`：Mediator 发送 CreateCommand。
  - `UpdateAsync` `[HttpPost("{id}")]`：Mediator 发送 UpdateCommand。
  - `DeleteAsync` `[HttpPost("{id}")]`：Mediator 发送 DeleteCommand。

### 模板参考
```csharp
public class RiskController(
    IRiskQuery riskQuery,
    IRiskPagedQuery riskPagedQuery,
    IDedsiMediator dedsiMediator) : SafetyEnvProtectionController
{
    [HttpPost]
    public Task<RiskPagedResultDto> PagedQueryAsync(RiskPagedInputDto input)
    {
        input.IsExport = false;
        return riskPagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);
    }

    [HttpPost]
    public async Task<IActionResult> ExportExcelAsync(RiskPagedInputDto input)
    {
        input.IsExport = true;
        var result = await riskPagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);
        // ... MiniExcel logic ...
        var memoryStream = new MemoryStream();
        await memoryStream.SaveAsAsync(result.Items, ...);
        memoryStream.Seek(0, SeekOrigin.Begin);
        return File(memoryStream, "application/vnd...", "export.xlsx");
    }

    [HttpGet("{id}")]
    public Task<RiskDto> GetAsync([FromRoute] string id) => riskQuery.GetAsync(id, HttpContext.RequestAborted);

    [HttpPost]
    public Task<string> CreateAsync(CreateRiskRequest request) => dedsiMediator.SendAsync(new CreateRiskCommand(request.Risk), HttpContext.RequestAborted);

    [HttpPost("{id}")]
    public Task<bool> UpdateAsync([FromRoute] string id, [FromBody] UpdateRiskRequest request) => dedsiMediator.SendAsync(new UpdateRiskCommand(id, request.Risk), HttpContext.RequestAborted);

    [HttpPost("{id}")]
    public Task<bool> DeleteAsync([FromRoute] string id) => dedsiMediator.SendAsync(new DeleteRiskCommand(id), HttpContext.RequestAborted);
}
```
