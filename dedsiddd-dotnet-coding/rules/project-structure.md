# 项目结构

## Rule

遵循 DDD 分层架构和依赖规则，确保代码组织的清晰性和可维护性。

## Impact

HIGH

## Problem

错误的项目结构会导致依赖混乱、违反架构原则和维护困难。

## Solution

### 架构分层

```
[Project].Host          # 宿主层
    ↓
[Project].HttpApi       # HTTP API 层
    ↓
[Project].UseCase       # 用例层
    ↓
[Project].Domain        # 领域层
    ↑
[Project].Infrastructure # 基础设施层
```

### 依赖规则

```
[Project].Host → [Project].HttpApi → [Project].UseCase → [Project].Domain
                                        ↓
                           [Project].Infrastructure → [Project].Domain
```

**关键规则**：
- Domain 层不依赖任何其他层
- Infrastructure 依赖 Domain
- UseCase 依赖 Domain 和 Infrastructure
- HttpApi 依赖 UseCase
- Host 依赖 HttpApi

### 项目职责

#### [Project].Domain

**目录结构**：
```
[Entities]/
  [Domain].cs
[ValueObjects]/
[Constants]/
  [Domain]Consts.cs
```

**包含内容**：
- 聚合根实体
- 值对象
- 领域常量
- 领域接口
- 业务异常

#### [Project].Infrastructure

**目录结构**：
```
EntityFrameworkCore/
  [Project]DbContext.cs
  EntityConfigurations/
    [Domain]Configuration.cs
Repositories/
  [Domain]Repository.cs
```

**包含内容**：
- EF Core DbContext
- Entity Configuration
- Repository 实现
- 数据库相关配置

#### [Project].UseCase

**目录结构**：
```
[Entities]/
  Dtos/
    [Domain]Dto.cs
    [Domain]CreateUpdateDto.cs
  CommandHandlers/
    Create[Domain]CommandHandler.cs
    Update[Domain]CommandHandler.cs
    Delete[Domain]CommandHandler.cs
  Queries/
    [Domain]Query.cs
    [Domain]PagedQuery.cs
```

**包含内容**：
- DTO（数据传输对象）
- Command Handler（命令处理器）
- Query（查询）
- 应用服务

#### [Project].HttpApi

**目录结构**：
```
Controllers/
  [Domain]Controller.cs
Requests/  (可选)
  Create[Domain]Request.cs
  Update[Domain]Request.cs
```

**包含内容**：
- Controller（控制器）
- API 契约
- Request 模型

#### [Project].Host

**目录结构**：
```
[Project]HostModule.cs
Program.cs
appsettings.json
```

**包含内容**：
- 启动配置
- 依赖注入注册
- 中间件配置
- 应用入口

### 依赖方向

**允许的依赖**：
- Infrastructure → Domain ✓
- UseCase → Domain ✓
- UseCase → Infrastructure ✓
- HttpApi → UseCase ✓
- Host → HttpApi ✓

**禁止的依赖**：
- Domain → 任何其他层 ✗
- Infrastructure → UseCase ✗
- UseCase → HttpApi ✗
- HttpApi → Infrastructure ✗（应通过 UseCase）
- Infrastructure → Host ✗

## Related

- [file-organization.md](./file-organization.md)
