# DedsiNative 架构与运行管线

## 目录
1. 项目结构
2. 分层依赖
3. 运行管线
4. EF Core 与数据库
5. 依赖注入
6. 日志与观测
7. OpenAPI 与 Scalar
8. 全局异常处理
9. 主键策略

## 项目结构
使用以下目录组织代码：
```text
content/
├─ src/
│  ├─ DedsiNative.Domain/
│  ├─ DedsiNative.Infrastructure/
│  ├─ DedsiNative.Operation/
│  └─ DedsiNative.HttpApi/
├─ frameworks/
└─ skill/
```
仅在 src 目录下修改业务代码。frameworks 为框架层，只读。

## 分层依赖
保持依赖方向如下：
DedsiNative.HttpApi -> DedsiNative.Operation -> DedsiNative.Infrastructure -> DedsiNative.Domain

避免反向引用，尤其不要让 Domain 依赖 Infrastructure 或 HttpApi。

## 运行管线
Program.cs 的关键步骤：
1. 配置 Serilog。
2. 使用 Scrutor 扫描并注册 Operation 与 Repository。
3. 注册 EF Core MySQL（AddMySqlDb）。
4. 开启 OpenAPI。
5. 构建 app。
6. 使用 Serilog 请求日志。
7. 开发环境 MapOpenApi 与 Scalar。
8. UseHttpsRedirection。
9. UseMiddleware<GlobalExceptionHandlingMiddleware>。
10. MapDedsiNativeEndpoints。
11. Run。

## EF Core 与数据库
- 连接字符串键为 `ConnectionStrings:DedsiNativeDB`。
- `DedsiNativeDbContext` 使用 `ApplyConfigurationsFromAssembly` 自动加载实体配置。
- 新增实体时，在 `EntityFrameworkCores/EntityConfigurations` 添加 `IEntityTypeConfiguration` 实现。
- 在 `DedsiNativeDbContext` 中新增 `DbSet` 属性。

## 依赖注入
- Scrutor 扫描以下程序集：HttpApi、Operation、Infrastructure。
- 实现 `IDedsiNativeOperation` 的类与名称以 `Repository` 结尾的类自动注册。
- 生命周期为 Transient。

## 日志与观测
- Serilog 输出到控制台与 `Logs/logs.txt`。
- 日志按小时滚动。
- `Microsoft.EntityFrameworkCore` 日志级别为 Warning。

## OpenAPI 与 Scalar
- 开发环境启用 OpenAPI 与 Scalar UI。
- OpenAPI 文档路径为 `/openapi/v1.json`。
- 访问根路径查看 Scalar UI。

## 全局异常处理
GlobalExceptionHandlingMiddleware 映射：
- ArgumentNullException -> 400
- ArgumentException -> 400
- InvalidOperationException -> 400
- UnauthorizedAccessException -> 401
- KeyNotFoundException -> 404
- TimeoutException -> 408
- NotImplementedException -> 501
- 其他异常 -> 500
非生产环境返回 ErrorDetails。

## 主键策略
- 使用 `GetStringPrimaryKey()` 生成 ULID 字符串。
- 使用 `GetGuidPrimaryKey()` 生成 UUIDv7 Guid。
