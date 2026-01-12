---
name: dedsiddd-dotnet-coding
description: 用于在 DedsiDDD + .NET 项目中，按统一约定生成 DDD/CQRS 常见代码骨架（领域模型、DTO、EF Core 映射、仓储、Commands、Queries、Controller）。
---

## 范围与目标

本 Skill 用于：
- 在 DedsiDDD + .NET（Clean Architecture + CQRS）项目中，按既有约定生成可编译、可落地的代码骨架与模板。
- 将“文件应该放在哪一层/哪个项目/如何组织文件”一次说明清楚，并提供可直接复用的参考模板。
- 强制对齐关键工程约束：依赖方向、文件聚合原则（Command/Handler 同文件等）、CancellationToken 全链路透传、分页/导出约定。

不在范围内：
- 在缺少上下文时编造不存在的模块、基类、包名或项目特有约定（以仓库为准）。
- 生成与现有解决方案分层冲突的强绑定实现（例如把 Infrastructure 逻辑塞进 Domain）。
- 在未确认业务不变式/字段约束的情况下“拍脑袋”补齐领域规则。

冲突优先级（从高到低）：
1. 当前仓库已有实现与约定（现有代码/模块/命名/路由/返回结构）
2. 本 SKILL 的通用约定
3. 一般 DDD/CQRS 的行业习惯

## 输入与输出

### 生成/改造前必须明确的信息（不明确就先问）
- 解决方案/模块边界：实体属于哪个模块（目录/命名空间前缀）、HttpApi 路由风格（是否 action route）。
- 聚合根：实体英文单数名（`[Entity]`）与复数名（`[Entities]`），主键类型（默认 `string`/ULID）。
- 字段清单：字段名、类型、是否必填、长度/精度、默认值、是否需要枚举、是否需要值对象。
- 关系与集合：一对多/多对多/聚合内集合的唯一性约束、更新策略（增量 vs Clear+Add）。
- 数据库映射：表名、Schema、索引、唯一性约束、是否软删（若项目已有）。
- API 契约：需要哪些端点（Get/Paged/Create/Update/Delete/Export），以及导出列（通常与 PagedRowDto 对齐）。

### 我会输出什么（交付标准）
- 明确的文件清单与落点（Domain/Infrastructure/UseCase/HttpApi）。
- 每个文件的代码模板（或在现有文件上给出最小变更补丁）。
- 所有公开入口（Controller/Query/Command）都包含 `CancellationToken` 并向下透传到 EF Core/仓储。
- 分页查询遵循固定顺序：筛选 → Count → 排序 →（非导出则分页）→ 投影 → ToList。

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
    - EF Core 持久化与集成；包含 `DbContext` 与实体映射配置（`EntityConfigurations`）。
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
    │     └─ EntityConfigurations/
    │        └─ [Entity]Configuration.cs
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

### 存放位置速查
> 目标：把“应该建哪些文件、分别放在哪”一次说清，减少来回翻找。

| 生成块 | 放置项目 | 目录（建议） | 典型文件 |
| --- | --- | --- | --- |
| 领域模型 | `src/[Project].Domain` | `src/[Project].Domain/[Entities]/` | `[Entity].cs` |
| DTO（含分页 DTO） | `src/[Project].UseCase` | `src/[Project].UseCase/[Entities]/Dtos/` | `[Entity]Dto.cs` / `[Entity]CreateUpdateDto.cs` / `[Entity]PagedDtos.cs` |
| EF Core DbContext | `src/[Project].Infrastructure` | `src/[Project].Infrastructure/EntityFrameworkCore/` | `[Project]DbContext.cs` |
| EF Core 映射 | `src/[Project].Infrastructure` | `src/[Project].Infrastructure/EntityFrameworkCore/EntityConfigurations/` | `[Entity]Configuration.cs` |
| 仓储（接口+实现同文件） | `src/[Project].Infrastructure` | `src/[Project].Infrastructure/Repositories/` | `[Entity]Repository.cs` |
| Commands（命令+处理器同文件） | `src/[Project].UseCase` | `src/[Project].UseCase/[Entities]/CommandHandlers/` | `Create[Entity]CommandHandler.cs` / `Update[Entity]CommandHandler.cs` / `Delete[Entity]CommandHandler.cs` |
| Queries（接口+实现同文件） | `src/[Project].UseCase` | `src/[Project].UseCase/[Entities]/Queries/` | `[Entity]Query.cs` / `[Entity]PagedQuery.cs` |
| Controller | `src/[Project].HttpApi` | （随项目现有 Controller 组织） | `[Entity]Controller.cs` |
| Controller Requests（推荐） | `src/[Project].HttpApi` | `src/[Project].HttpApi/Requests/` | `[Entity]Requests.cs` |

### 最小交付清单
> 如果你只想快速跑通 CRUD + 分页 + 导出：按此清单创建文件即可。

1. Domain：新增 `[Entity].cs`（聚合根/实体/值对象按需）
2. Infrastructure：
    - `[Project]DbContext.cs` 增加 `DbSet<[Entity]>`（若项目是每聚合根一个 DbSet）
    - 新增 `[Entity]Configuration.cs`（表名/Schema/主键/索引/长度/关系）
    - 新增 `[Entity]Repository.cs`（接口+实现）
3. UseCase：
    - `[Entity]Dto.cs` / `[Entity]CreateUpdateDto.cs`
    - `Create[Entity]CommandHandler.cs` / `Update[Entity]CommandHandler.cs` / `Delete[Entity]CommandHandler.cs`
    - `[Entity]Query.cs` + `[Entity]PagedQuery.cs`
4. HttpApi：
    - `[Entity]Controller.cs`
    - （推荐）`[Entity]Requests.cs`（把 Body 契约从 DTO 中隔离出来，便于演进）

## 通用约定（全局规则）
- **命名占位符**：`[Entity]` / `[Project]` / `[DbContext]` 代表按实际业务替换。
- **文件聚合原则**：
    - Command 与 Handler 必须同文件。
    - Query 接口与实现必须同文件。
    - Repository 接口与实现必须同文件。
- **Enum 约束（强制）**：所有 `enum` 必须显式赋值，且第一个业务值从 `1` 开始（禁止使用默认的 `0` 值）。
- **CancellationToken**：所有公开 API/Query 方法必须显式接收 `CancellationToken`，并向下透传到 EF Core/仓储。
- **XML 注释**：对外接口（Controller、Query 接口/实现、DTO）遵循项目现有风格补齐注释；实现类可用 `/// <inheritdoc />`。
- **Controller 路由约定提示**：若项目使用 `[Route(".../[controller]/[action]")]`，则 `UpdateAsync` 与 `DeleteAsync` 都可以使用 `[HttpPost("{id}")]`，因为 action 名不同不会冲突。
- **常用 using（生成后更容易一次编译通过）**：
    - Domain：`Volo.Abp`（Check/异常）、`Volo.Abp.Domain.Entities`（AggregateRoot）、`Dedsi.Ddd.Domain.Auditing.Contracts`（审计接口）
    - Infrastructure（EF Core）：`Microsoft.EntityFrameworkCore`、`Microsoft.EntityFrameworkCore.Metadata.Builders`、`Volo.Abp.Data`、`Volo.Abp.EntityFrameworkCore`
    - UseCase（Query）：`Volo.Abp.Linq`（WhereIf/PageBy 扩展）、`Microsoft.EntityFrameworkCore`（CountAsync/ToListAsync）
    - HttpApi：`Microsoft.AspNetCore.Mvc`、`Dedsi.Ddd.CQRS.Mediators`

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
6. **写侧（Commands）**：
    - Create/Update/Delete 命令与处理器同文件，并拆分为三个独立文件（每文件仅 1 个 Command + 1 个 Handler）。
    - Create：生成新 ULID -> 创建领域对象 -> `InsertAsync(..., cancellationToken)` -> 返回 `string id`。
    - Update：`GetAsync(id, cancellationToken)` -> 调用领域方法变更（禁止外部直接 set）-> `UpdateAsync(..., cancellationToken)` -> 返回 `bool`。
    - 集合更新：按约定“先 Clear 再 Add”（或按业务增量更新，但必须全项目一致）。
    - `CancellationToken`：Handler 内所有仓储/EF 调用必须透传。
7. **读侧（Queries）**：
    - 单条查询：优先注入仓储，通过 `GetAsync(a => a.Id == id, includeDetails: true, cancellationToken)` 获取领域对象并手动映射为 `Dto`（`id` 必须用于谓词）。
    - 分页查询：优先注入 `I[Project]DbContext`（或业务 DbContext 接口）拿 `DbSet<T>`，并按固定顺序组织：
        - `AsNoTracking()`（只读列表默认不跟踪）
        - `WhereIf(...)` 应用筛选
        - `CountAsync(cancellationToken)` 计算总数
        - `OrderBy...` 排序
        - `if (!input.IsExport) PageBy(...)`（导出不分页）
        - `Select` 投影到 RowDto -> `ToListAsync(cancellationToken)`
8. **HttpApi（Controller + Requests）**：只做编排与透传（路由/鉴权/入参/调用 Query/Mediator/导出），不写业务规则。
    - Request 模型：为 Create/Update 提供独立的 `Create[Entity]Request` / `Update[Entity]Request`，Body 内部字段使用 `[Entity]CreateUpdateDto`（写入契约），避免把展示 `Dto` 当作写入契约。
    - Update 端点：`[HttpPost("{id}")]` + `[FromRoute] string id` + `[FromBody] Update[Entity]Request request`，并返回 `Task<bool>`（对齐 `Update[Entity]Command : DedsiCommand<bool>`）。
    - 导出端点：设置 `input.IsExport = true` 复用分页查询筛选条件，使用 MiniExcel 输出。
    - `CancellationToken`：从 `HttpContext.RequestAborted` 贯穿到 Query/Mediator。
9. **最后做一次一致性检查**：
    - 命名/路由/返回类型是否与 Commands/Queries 的返回保持一致（尤其 Update 返回 `bool`）。
    - Create/Update 的 Request Body 类型是否使用 `CreateUpdateDto`（而非展示 `Dto`）。
    - `CancellationToken` 是否全链路透传到 EF/仓储。
    - Update/Query 示例中的 `id` 是否确实用于谓词/命令入参，避免“入参未使用”。

## 目录
- [范围与目标](#范围与目标)
- [输入与输出](#输入与输出)
- [项目结构（Clean Architecture CQRS）](#项目结构clean-architecture-cqrs)
- [存放位置速查](#存放位置速查)
- [最小交付清单](#最小交付清单)
- [通用约定（全局规则）](#通用约定全局规则)
- [推荐工作流](#推荐工作流)
- [生成领域模型](#生成领域模型)
- [生成 DTO](#生成-dto)
- [配置数据库](#配置数据库)
- [生成仓储](#生成仓储)
- [生成命令 (Commands)](#生成命令-commands)
- [生成查询 (Queries)](#生成查询-queries)
- [生成控制器 (Controller)](#生成控制器-controller)
- [常见坑与验收清单](#常见坑与验收清单)

---

## 生成领域模型

### 何时使用
当需要在 Dedsi DDD 项目中创建新的领域实体或聚合根时。

### 存放位置（与项目结构一致）
- 项目：`src/[Project].Domain`
- 目录：`src/[Project].Domain/[Entities]/`
- 文件：`src/[Project].Domain/[Entities]/[Entity].cs`
- 命名空间：建议与目录一致（例如 `namespace [Project].Domain.[Entities];`）
- 说明：领域模型只放领域层；不要把 DTO/EF Core 映射/查询投影等放进 Domain。

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
            - 枚举：仅允许合法值（例如 `Enum.IsDefined` / 约束集合）；并遵循“所有 enum 显式赋值且从 1 开始”的全局约束。
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

### 存放位置（与项目结构一致）
- 项目：`src/[Project].UseCase`
- 目录：`src/[Project].UseCase/[Entities]/Dtos/`
- 文件建议：
    - `src/[Project].UseCase/[Entities]/Dtos/[Entity]Dto.cs`
    - `src/[Project].UseCase/[Entities]/Dtos/[Entity]CreateUpdateDto.cs`
    - 分页 DTO：`src/[Project].UseCase/[Entities]/Dtos/[Entity]PagedDtos.cs`（或按项目既有拆分方式）
- 命名空间：建议 `namespace [Project].UseCase.[Entities].Dtos;`
- 说明：DTO 属于用例层（Application/UseCase），不要放到 Domain。

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
- **边界（推荐）**：DTO 尽量不要直接暴露领域对象类型（例如不要把 `RiskReviewer` 直接作为返回 DTO 字段类型）。
    - 推荐做法：为集合/复杂对象单独定义 `XxxDto`/`XxxCreateUpdateDto`，并在 Query/Command 里做映射。
    - 例外：若该类型本质是“稳定的对外契约值对象”（字段与含义长期稳定、不会牵动领域规则），且项目已有先例，则允许直接复用。

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

### 存放位置（与项目结构一致）
- 项目：`src/[Project].Infrastructure`
- DbContext：
    - `src/[Project].Infrastructure/EntityFrameworkCore/[Project]DbContext.cs`
- 实体映射：`src/[Project].Infrastructure/EntityFrameworkCore/EntityConfigurations/[Entity]Configuration.cs`
- 命名空间：
    - DbContext：通常 `namespace [Project].Infrastructure.EntityFrameworkCore;`
    - 配置类：通常 `namespace [Project].Infrastructure.EntityFrameworkCore.EntityConfigurations;`
- 说明：EF Core 映射与 DbContext 仅放 Infrastructure；Domain 不引用 EF Core。

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
using Microsoft.EntityFrameworkCore.Metadata.Builders;

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

### 存放位置（与项目结构一致）
- 项目：`src/[Project].Infrastructure`
- 目录：`src/[Project].Infrastructure/Repositories/`
- 文件：`src/[Project].Infrastructure/Repositories/[Entity]Repository.cs`
- 命名空间：建议 `namespace [Project].Infrastructure.Repositories;`（或按仓库现有约定）
- 说明：本仓库约定“接口与实现同文件”，因此不要把接口拆到 Domain；UseCase 通过依赖 Infrastructure 使用仓储。

### 规则
- **接口**：
  - 命名：`I[AggregateRoot]Repository`（例如 `IRiskRepository`）。
    - 继承：`IDedsiCqrsRepository<AggregateRoot, KeyType>`（类型来自 `Dedsi.Ddd.Domain.Repositories`）。
    - 命名空间：按项目现有约定（通常与存放位置一致，例如 `namespace [Project].Infrastructure.Repositories;`）。
- **实现**：
  - 命名：`[AggregateRoot]Repository`（例如 `RiskRepository`）。
        - 继承：`DedsiDddEfCoreRepository<DbContext, AggregateRoot, KeyType>`
            - 说明：`DedsiDddEfCoreRepository` 为**框架基类**，命名空间为 `Dedsi.EntityFrameworkCore.Repositories`。
        - 命名空间：按项目现有约定（通常与存放位置一致，例如 `namespace [Project].Infrastructure.Repositories;`）。
  - 实现接口。
- **文件结构**：接口和实现 **必须** 在同一个文件中。
- **构造函数**：注入 `IDbContextProvider<DbContext>`。
- **依赖方向**：UseCase 只依赖仓储接口（通过 DI 注入）；仓储实现位于 Infrastructure 并依赖 EF Core DbContext。

### 模板参考

#### 模板 A：直接使用框架基类（`DedsiDddEfCoreRepository`）
```csharp
using Dedsi.Ddd.Domain.Repositories;
using Dedsi.EntityFrameworkCore.Repositories;
using DigitalEmployeeDdd.Domain.Teachers;
using DigitalEmployeeDdd.EntityFrameworkCore;
using Volo.Abp.EntityFrameworkCore;

namespace DigitalEmployeeDdd.Infrastructure.Repositories;

/// <summary>
/// 老师仓储
/// </summary>
public interface ITeacherRepository : IDedsiCqrsRepository<Teacher, string>;

/// <summary>
/// 老师仓储
/// </summary>
/// <param name="dbContextProvider"></param>
public class TeacherRepository(IDbContextProvider<DigitalEmployeeDddDbContext> dbContextProvider)
    : DedsiDddEfCoreRepository<DigitalEmployeeDddDbContext, Teacher, string>(dbContextProvider), ITeacherRepository;
```

---

## 生成命令 (Commands)

### 何时使用
实现 CQRS 写操作（Write side）：创建、更新、删除。

### 规则
- Create/Update/Delete 三个命令 **必须** 拆分为三个独立的 `.cs` 文件。
- 每个命令与其处理器 **必须** 在同一个文件中（即：一个 `.cs` 文件只承载一个 Command + 对应 Handler）。

### 存放位置（与项目结构一致）
- 项目：`src/[Project].UseCase`
- 目录：`src/[Project].UseCase/[Entities]/CommandHandlers/`
- 文件建议（按命令拆分）：
    - `src/[Project].UseCase/[Entities]/CommandHandlers/Create[Entity]CommandHandler.cs`
    - `src/[Project].UseCase/[Entities]/CommandHandlers/Update[Entity]CommandHandler.cs`
    - `src/[Project].UseCase/[Entities]/CommandHandlers/Delete[Entity]CommandHandler.cs`
- 命名空间：建议 `namespace [Project].UseCase.[Entities].CommandHandlers;`
- 说明：Commands/Handlers 属于用例层写侧；不要放到 HttpApi（Controller 只负责编排与透传）。

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
// Create[Entity]CommandHandler.cs
using Dedsi.Ddd.CQRS.CommandHandlers;
using Dedsi.Ddd.CQRS.Commands;
using Dedsi.Ddd.CQRS.Mediators;
using Volo.Abp;

/// <summary>
/// 创建风险命令
/// </summary>
/// <param name="RiskDto">风险创建/更新 DTO</param>
public record CreateRiskCommand(RiskCreateUpdateDto RiskDto): DedsiCommand<string>;

/// <summary>
/// 创建风险命令处理器
/// </summary>
public class CreateRiskCommandHandler(IRiskRepository riskRepository) : DedsiCommandHandler<CreateRiskCommand, string>
{
    /// <inheritdoc />
    public override async Task<string> Handle(CreateRiskCommand command, CancellationToken cancellationToken)
    {
        var domainId = Ulid.NewUlid().ToString();
        var domain = new Risk(domainId, command.RiskDto.RiskNumber, command.RiskDto.InvolvingProject);
        await riskRepository.InsertAsync(domain, true, cancellationToken);
        return domainId;
    }
}
```

```csharp
// Update[Entity]CommandHandler.cs
using Dedsi.Ddd.CQRS.CommandHandlers;
using Dedsi.Ddd.CQRS.Commands;
using Dedsi.Ddd.CQRS.Mediators;
using Volo.Abp;

/// <summary>
/// 更新风险命令
/// </summary>
/// <param name="Id">风险 Id</param>
/// <param name="RiskDto">风险创建/更新 DTO</param>
public record UpdateRiskCommand(string Id, RiskCreateUpdateDto RiskDto) : DedsiCommand<bool>;

/// <summary>
/// 更新风险命令处理器
/// </summary>
public class UpdateRiskCommandHandler(IRiskRepository riskRepository) : DedsiCommandHandler<UpdateRiskCommand, bool>
{
    /// <inheritdoc />
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
```

```csharp
// Delete[Entity]CommandHandler.cs
using Dedsi.Ddd.CQRS.CommandHandlers;
using Dedsi.Ddd.CQRS.Commands;
using Dedsi.Ddd.CQRS.Mediators;
using Volo.Abp;

/// <summary>
/// 删除风险命令
/// </summary>
/// <param name="Id">风险 Id</param>
public record DeleteRiskCommand(string Id) : DedsiCommand<bool>;

/// <summary>
/// 删除风险命令处理器
/// </summary>
public class DeleteRiskCommandHandler(IRiskRepository riskRepository) : DedsiCommandHandler<DeleteRiskCommand, bool>
{
    /// <inheritdoc />
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
- **接口与实现同文件**：每种 Query（单个查询 / 分页查询等）必须将“接口 + 实现 + 方法”放在同一个 `.cs` 文件中。
- **文件拆分（推荐）**：单个查询与分页查询建议分为两个文件（对应模板 A/B），避免一个文件混放多种 Query。
- **数据访问方式（强制）**：
    - **优先（与模板一致）**：通过注入“DbContext 接口”（例如 `I[Project]DbContext` / 业务 `ISafetyEnvProtectionDbContext`）获取 `DbSet<T>` 来查询。
    - **约束**：禁止在 Query 中直接注入具体 `DbContext` 实现类；用接口承载 `DbSet<T>`，更利于解耦与测试。
- **分页查询行为约定（强制）**：
    - 必须 `AsNoTracking()`（分页列表默认只读）。
    - 必须先应用筛选（`WhereIf` 等）再 `CountAsync(cancellationToken)` 获取总数。
    - 必须先排序（例如 `OrderByDescending(...)`），再按需分页。
    - 必须支持导出模式：当 `input.IsExport == true` 时不分页；否则使用 `input.GetSkipCount()` + `input.PageSize` 分页。
    - 必须使用 `Select` 投影到 RowDto/Dto（避免直接返回实体或导航属性）。
    - 所有 EF Core 异步调用必须传入 `cancellationToken`。
- **单个查询依赖注入（推荐）**：优先注入仓储 `I[Entity]Repository` 并使用 `GetAsync(predicate, includeDetails: true, cancellationToken)`。
- **入参有效性（强制）**：`id` 入参必须用于谓词（例如 `a => a.Id == id`），禁止出现“入参未使用”的示例。
- **注释（强制）**：
    - Query 接口 / 实现类 / 方法必须有 XML 注释；实现类与其实现方法统一使用 `/// <inheritdoc />`。
    - 分页 DTO（Input/Row/Result）每个字段/属性必须有 XML 注释。
- **CancellationToken（强制）**：所有公开查询方法必须显式接收 `CancellationToken cancellationToken`，并向下透传到 EF Core/仓储调用。

### 存放位置（与项目结构一致）
- 项目：`src/[Project].UseCase`
- 目录：`src/[Project].UseCase/[Entities]/Queries/`
- 文件建议：
    - 单条查询：`src/[Project].UseCase/[Entities]/Queries/[Entity]Query.cs`
    - 分页查询：`src/[Project].UseCase/[Entities]/Queries/[Entity]PagedQuery.cs`（或与分页 DTO 同文件，按项目习惯）
- 命名空间：建议 `namespace [Project].UseCase.[Entities].Queries;`
- 说明：Query 是读侧用例逻辑；HttpApi 只注入并调用，不承载查询实现。

#### 单个查询 (Single Query)
- **接口**：`I[Entity]Query : IDedsiQuery`。
- **方法**：`Task<[Entity]Dto> GetAsync(string id, CancellationToken cancellationToken)`。
- **实现**：`[Entity]Query : I[Entity]Query`。
- **逻辑**：
  - `await repository.GetAsync(predicate, includeDetails: true, cancellationToken)`。
  - 手动映射到 `[Entity]Dto`。
  - `id` 入参必须用于谓词。

#### 分页查询 (Paged Query)
- **入参**：`[Entity]PagedInputDto : DedsiPagedRequestDto`, `DedsiPagedRequestDto`的命名空间为：Dedsi.Ddd.Application.Contracts.Dtos。
- **行 DTO**：`[Entity]PagedRowDto`。
- **结果**：`[Entity]PagedResultDto : DedsiPagedResultDto<RowDto>`。
- **接口**：`I[Entity]PagedQuery : IDedsiQuery`。
- **方法**：`Task<[Entity]PagedResultDto> PagedQueryAsync([Entity]PagedInputDto input, CancellationToken cancellationToken)`。
- **DTO 要求**：
  - `PagedInputDto` 必须包含 `IsExport`（导出时不分页）以及必要筛选字段。
  - 每个 DTO 字段/属性都必须有 XML 注释。
- **实现**：`[Entity]PagedQuery : I[Entity]PagedQuery`。
- **依赖注入**：注入 DbContext 接口（例如 `I[Project]DbContext` / 业务 `ISafetyEnvProtectionDbContext`）（必要时 `ICurrentUser`）。
- **逻辑步骤（模板）**：
    - `DbSet.AsNoTracking()`
    - `WhereIf` 应用筛选条件
    - `CountAsync(cancellationToken)`
    - `OrderByDescending(...)`
    - `if (!input.IsExport) query = query.PageBy(input.GetSkipCount(), input.PageSize)`
    - `Select` 投影到 RowDto
    - `ToListAsync(cancellationToken)`
    - 返回 `new ... { TotalCount = totalCount, Items = items }`

### 模板参考

#### 模板 A：分页查询（Paged Query）
```csharp
// RiskPagedQuery.cs

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


using Dedsi.Ddd.Domain.Queries;
using DigitalEmployeeDdd.EntityFrameworkCore;
using DigitalEmployeeDdd.UseCase.Orders.Dtos;
using Microsoft.EntityFrameworkCore;
using Volo.Abp.Users;

/// <summary>
/// 风险分页查询
/// </summary>
public interface IRiskPagedQuery : IDedsiQuery
{
    /// <summary>
    /// 分页查询
    /// </summary>
    Task<RiskPagedResultDto> PagedQueryAsync(RiskPagedInputDto input, CancellationToken cancellationToken);
}

/// <inheritdoc />
public class RiskPagedQuery(
    ICurrentUser currentUser,
    ISafetyEnvProtectionDbContext safetyEnvProtectionDbContext): IRiskPagedQuery
{
    /// <inheritdoc />
    public async Task<RiskPagedResultDto> PagedQueryAsync(RiskPagedInputDto input, CancellationToken cancellationToken)
    {
        var query = safetyEnvProtectionDbContext
            .Risks
            .AsNoTracking()
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
```

#### 模板 B：单个查询（Single Query）
```csharp
using Dedsi.Ddd.Domain.Queries;
using DigitalEmployeeDdd.EntityFrameworkCore;
using DigitalEmployeeDdd.Infrastructure.Repositories;
using DigitalEmployeeDdd.UseCase.Orders.Dtos;

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
    IRiskRepository riskRepository,
    ICurrentUser currentUser,
    ISafetyEnvProtectionDbContext safetyEnvProtectionDbContext): IRiskQuery
{
    /// <inheritdoc />
    public async Task<RiskDto> GetAsync(string id, CancellationToken cancellationToken)
    {
        var domain = await riskRepository.GetAsync(a => a.Id == id, true, cancellationToken);

        var dto = new RiskDto()
        {
            Id = domain.Id,
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
- **CancellationToken（强制）**：Controller Action 建议显式接收 `CancellationToken cancellationToken`（ASP.NET Core 会自动绑定到 `HttpContext.RequestAborted`），并向下透传到 Query/Mediator。
- **端点（推荐最小集）**：
    - `PagedQueryAsync`（`[HttpPost]`）：调用 PagedQuery。
    - `ExportExcelAsync`（`[HttpPost]`）：调用 PagedQuery 并设置 `IsExport=true`，使用 `MiniExcel` 返回 Excel。
    - `GetAsync`（`[HttpGet("{id}")]`）：调用 `Query.GetAsync`。
    - `CreateAsync`（`[HttpPost]`）：Mediator 发送 CreateCommand。
    - `UpdateAsync`（`[HttpPost("{id}")]`）：Mediator 发送 UpdateCommand。
    - `DeleteAsync`（`[HttpPost("{id}")]`）：Mediator 发送 DeleteCommand。

#### 路由风格提示（不要拍脑袋，优先跟随仓库现有 Controller）
- 若项目使用 action route（例如 `[Route("api/.../[controller]/[action]")]`），`UpdateAsync` 与 `DeleteAsync` 都可以使用 `[HttpPost("{id}")]`，因为 action 名不同不会冲突。
- 若项目使用 REST 资源路由（例如 `[Route("api/.../[controller]")]`），建议为分页/导出显式增加子路由（例如 `[HttpPost("paged")]` / `[HttpPost("export")]`），避免与 Create 的 `[HttpPost]` 冲突。

#### 更新（Update）规则（按模板落地，保证可编译/可调用）
- **路由**：优先使用 `[HttpPost("{id}")]`，并显式标注 `[FromRoute] string id`。
- **请求体**：必须使用独立 Request 模型承载 Body（推荐 `Update[Entity]Request`），并显式标注 `[FromBody]`。
- **Body 类型**：Update 的 Body 内部字段必须使用 `[Entity]CreateUpdateDto`（写入契约），不要使用 `[Entity]Dto`（展示契约），避免“照抄即类型不匹配”。
- **返回类型**：与 `Update[Entity]Command : DedsiCommand<bool>` 保持一致，Controller 的 `UpdateAsync` 返回 `Task<bool>`。
- **调用方式**：`return dedsiMediator.SendAsync(new Update[Entity]Command(id, request.[Entity]), HttpContext.RequestAborted);`（`CancellationToken` 必须透传）。

### Request 模型（推荐补齐，避免示例不可用）
> 你的 Controller 示例里使用了 `CreateRiskRequest`/`UpdateRiskRequest`。为保证“照抄能编译”，建议为每个实体补齐请求模型。

- 存放位置：
    - 与 Controller 同目录/同文件末尾
- 约定：
    - Create：`public record Create[Entity]Request([Entity]CreateUpdateDto [Entity]);`
    - Update：`public record Update[Entity]Request([Entity]CreateUpdateDto [Entity]);`
    - 字段必须加 XML 注释（与 DTO 一致）。

### 导出 Excel（MiniExcel）落地要点
- 依赖：项目需引用 `MiniExcel`（或仓库统一的导出组件）；`SaveAsAsync` 的扩展方法命名空间通常为 `MiniExcelLibs`。
- 推荐做法：导出只复用分页查询的“筛选条件”，但返回用 RowDto 投影（避免导出整实体/导航属性）。
- 文件名：包含实体名 + 时间戳（例如 `risk-20260110.xlsx`），避免并发下载冲突。

### 存放位置（与项目结构一致）
- 项目：`src/[Project].HttpApi`
- 目录：优先按项目既有 Controller 组织；若无既定约定可用 `src/[Project].HttpApi/[Entities]/`
- 文件：`src/[Project].HttpApi/[Entities]/[Entity]Controller.cs`
- 命名空间：建议与目录一致（例如 `namespace [Project].HttpApi.[Entities];`）
- 说明：Controller 只做路由/鉴权/入参/编排/透传；业务规则留在 Domain，写侧用例在 Commands，读侧用例在 Queries。

### 模板参考
```csharp
using Dedsi.Ddd.CQRS.Mediators;
using Microsoft.AspNetCore.Mvc;
using MiniExcelLibs;
using SafetyEnvProtection.Risks.CommandHandlers;
using SafetyEnvProtection.Risks.Dtos;
using SafetyEnvProtection.Risks.Queries;

/// <summary>
/// 风险
/// </summary>
public class RiskController(
    IRiskQuery riskQuery,
    IRiskPagedQuery riskPagedQuery,
    IDedsiMediator dedsiMediator) : SafetyEnvProtectionController
{
    /// <summary>
    /// 风险：分页查询
    /// </summary>
    /// <param name="input"></param>
    /// <returns></returns>
    [HttpPost]
    public Task<RiskPagedResultDto> PagedQueryAsync(RiskPagedInputDto input)
    {
        input.IsExport = false;
        return riskPagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);
    }
    
    /// <summary>
    /// 风险：导出
    /// </summary>
    /// <param name="input"></param>
    /// <returns></returns>
    [HttpPost]
    public async Task<IActionResult> ExportExcelAsync(RiskPagedInputDto input)
    {
        // 导出场景
        input.IsExport = true;
        var result = await riskPagedQuery.PagedQueryAsync(input, HttpContext.RequestAborted);

        var memoryStream = new MemoryStream();
        await memoryStream.SaveAsAsync(
            result.Items,
            cancellationToken: cancellationToken
        );
        memoryStream.Seek(0, SeekOrigin.Begin);

        return File(memoryStream, "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", $"风险导出{DateTime.Now:yyyyMMddHHmmss}.xlsx");
    }
    
    /// <summary>
    /// 风险：查询详情
    /// </summary>
    /// <param name="id"></param>
    /// <returns></returns>
    [HttpGet("{id}")]
    public Task<RiskDto> GetAsync([FromRoute] string id)
    {
        return riskQuery.GetAsync(id, HttpContext.RequestAborted);
    }
    
    /// <summary>
    /// 风险：创建
    /// </summary>
    /// <param name="request"></param>
    /// <returns></returns>
    [HttpPost]
    public Task<string> CreateAsync(CreateRiskRequest request)
    {
        var cmd = new CreateRiskCommand(request.Risk);
        return dedsiMediator.SendAsync(cmd, HttpContext.RequestAborted);
    }
    
    /// <summary>
    /// 风险：修改
    /// </summary>
    /// <param name="id"></param>
    /// <param name="request"></param>
    /// <returns></returns>
    [HttpPost("{id}")]
    public Task<bool> UpdateAsync([FromRoute] string id, [FromBody] UpdateRiskRequest request)
    {
        return dedsiMediator.SendAsync(new UpdateRiskCommand(id, request.Risk), HttpContext.RequestAborted);
    }
    
    /// <summary>
    /// 删除
    /// </summary>
    /// <param name="id"></param>
    /// <returns></returns>
    [HttpPost("{id}")]
    public Task<bool> DeleteAsync([FromRoute] string id)
    {
        return dedsiMediator.SendAsync(new DeleteRiskCommand(id), HttpContext.RequestAborted);
    }
}

/// <summary>
/// 创建风险请求对象
/// </summary>
/// <param name="Risk"></param>
public record CreateRiskRequest(RiskCreateUpdateDto Risk);

/// <summary>
/// 修改风险请求对象
/// </summary>
/// <param name="Risk"></param>
public record UpdateRiskRequest(RiskCreateUpdateDto Risk);
```

---

## 常见坑与验收清单

### 常见坑
- Update 端点 Body 误用展示 `Dto`：必须用写入契约 `CreateUpdateDto`（否则 Command 入参类型不匹配）。
- Query 忘记 `AsNoTracking()`：分页列表默认只读，避免不必要的跟踪开销。
- 分页顺序错误：必须“筛选 → Count → 排序 →（非导出则分页）→ 投影 → ToList”。
- `CancellationToken` 断链：Controller/Query/Command 接了 token 但没有传到 EF Core/仓储异步方法。
- `id` 入参未用于谓词：示例/实现必须 `a => a.Id == id`，否则既不安全也容易出现“入参未使用”。
- enum 未显式赋值：必须显式赋值且业务值从 1 开始（禁止默认 0）。

### 验收清单（生成后自检）
1. 文件落点与依赖方向正确（Domain 不依赖 EF/Core/HttpApi）。
2. Command/Handler、Query 接口/实现、Repository 接口/实现均同文件，且拆分粒度符合约定。
3. Controller/Query/Command 公开入口显式接收并透传 `CancellationToken`（Controller 建议以参数形式接入）。
4. Create/Update/Delete 的返回类型与 Command 返回类型一致（尤其 Update/Delete 常用 `bool`）。
5. Update/Delete 路由不冲突：action route 可复用 `[HttpPost("{id}")]`；资源路由需为 paged/export 增加子路由。
6. PagedQuery 支持导出模式：`IsExport=true` 不分页；导出列与 RowDto 对齐（避免导出实体/导航属性）。
7. Create/Update 的 Body 使用写入契约 `CreateUpdateDto`，展示 `Dto` 仅用于输出。
8. 分页查询实现顺序固定：筛选 → Count → 排序 →（非导出则分页）→ 投影 → ToList。