# 文件组织

## Rule

遵循统一的文件组织规范，提高代码可维护性。

## Impact

MEDIUM

## Problem

不一致的文件组织导致查找困难和维护成本增加。

## Solution

### 同文件原则

以下类型必须在同一文件中：

1. **Repository 接口和实现**
   ```csharp
   // 文件: [Domain]Repository.cs
   public interface I[Domain]Repository : IDedsiCqrsRepository<[Domain], string>;
   public class [Domain]Repository : DedsiDddEfCoreRepository<...>, I[Domain]Repository;
   ```

2. **Query 接口和实现**
   ```csharp
   // 文件: [Domain]Query.cs
   public interface I[Domain]Query : IDedsiQuery { ... }
   public class [Domain]Query : I[Domain]Query { ... }
   ```

3. **Command 和 Handler**
   ```csharp
   // 文件: Create[Domain]CommandHandler.cs
   public record Create[Domain]Command(...) : DedsiCommand<string>;
   public class Create[Domain]CommandHandler : DedsiCommandHandler<...> { ... }
   ```

### 分离原则

以下类型必须分离到不同文件：

1. **Create/Update/Delete Command** 应分别在独立文件中
2. **DTO 类型** 应独立文件
3. **Entity Configuration** 应独立文件

### 目录结构

```
src/[Project].Domain/
  [Entities]/
    [Domain].cs

src/[Project].UseCase/
  [Entities]/
    Dtos/
      [Domain]Dto.cs
      [Domain]CreateUpdateDto.cs
      [Domain]PagedInputDto.cs
      [Domain]PagedRowDto.cs
      [Domain]PagedResultDto.cs
    CommandHandlers/
      Create[Domain]CommandHandler.cs
      Update[Domain]CommandHandler.cs
      Delete[Domain]CommandHandler.cs
    Queries/
      [Domain]Query.cs
      [Domain]PagedQuery.cs

src/[Project].Infrastructure/
  EntityFrameworkCore/
    EntityConfigurations/
      [Domain]Configuration.cs
    [Project]DbContext.cs
  Repositories/
    [Domain]Repository.cs

src/[Project].HttpApi/
  Controllers/
    [Domain]Controller.cs
```

## Related

- [repository-patterns.md](./repository-patterns.md)
- [query-handlers.md](./query-handlers.md)
- [command-handlers.md](./command-handlers.md)
