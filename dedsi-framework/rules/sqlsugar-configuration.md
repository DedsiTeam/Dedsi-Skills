# SqlSugar 配置

## Rule

在项目中正确配置和使用 SqlSugar ORM。

## Impact

MEDIUM

## Problem

不正确的 SqlSugar 配置会导致数据库连接失败或功能不可用。

## Solution

### 配置 SqlSugar

在模块的 `ConfigureServices` 中配置：

#### SqlServer

```csharp
public override void ConfigureServices(ServiceConfigurationContext context)
{
    context.Services.ConfigureSqlSugarBySqlServer("Default");
}
```

#### MySql

```csharp
public override void ConfigureServices(ServiceConfigurationContext context)
{
    context.Services.ConfigureSqlSugarByMySql("Default");
}
```

#### 自定义配置

```csharp
public override void ConfigureServices(ServiceConfigurationContext context)
{
    var connectionConfig = new ConnectionConfig()
    {
        DbType = DbType.SqlServer,
        ConnectionString = "your-connection-string",
        IsAutoCloseConnection = true,
    };
    context.Services.ConfigureSqlSugar(connectionConfig);
}
```

### 使用 ISqlSugarClient

```csharp
public class MyService
{
    private readonly ISqlSugarClient _sqlSugarClient;
    
    public MyService(ISqlSugarClient sqlSugarClient)
    {
        _sqlSugarClient = sqlSugarClient;
    }
    
    public async Task<List<MyEntity>> QueryAsync()
    {
        return await _sqlSugarClient.Queryable<MyEntity>().ToListAsync();
    }
}
```

### SQL 日志

在 `Development` 环境下，SqlSugar 会自动输出 SQL 日志到控制台：

```csharp
// 本地研发环境才会开启
if (environment.EnvironmentName == "Development")
{
    sqlSugarClient.Aop.OnLogExecuting = (sql, parameters) =>
    {
        Console.WriteLine(UtilMethods.GetSqlString(connectionConfig.DbType, sql, parameters));
    };
}
```

### Query 接口

实现 `IDedsiSqlSugarQuery` 以使用查询基类：

```csharp
public interface IMyQuery : IDedsiSqlSugarQuery
{
    Task<List<MyEntity>> QueryAsync();
}

public class MyQuery : IMyQuery
{
    private readonly ISqlSugarClient _sqlSugarClient;
    
    public MyQuery(ISqlSugarClient sqlSugarClient)
    {
        _sqlSugarClient = sqlSugarClient;
    }
    
    public async Task<List<MyEntity>> QueryAsync()
    {
        return await _sqlSugarClient.Queryable<MyEntity>().ToListAsync();
    }
}
```

## Related

- [模块接入](./module-dependencies.md)
- [Repository 模式](./repository-pattern.md)
