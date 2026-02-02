# EF Core 实体映射

## Rule

使用流式 API 约定配置 EF Core 实体映射。

## Impact

HIGH

## Problem

不一致的实体映射会导致数据库架构问题和运行时错误。

## Solution

### 配置类

每个实体应该有自己的配置类：

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace [Project].Infrastructure.EntityFrameworkCore.EntityConfigurations;

internal class [Domain]Configuration : IEntityTypeConfiguration<[Domain]>
{
    public void Configure(EntityTypeBuilder<[Domain]> builder)
    {
        builder.ToTable("[Entities]", [Project]DomainConsts.DbSchemaName);
        builder.HasKey(e => e.Id);

        builder.Property(e => e.RequiredField)
            .IsRequired()
            .HasMaxLength(128);

        builder.Property(e => e.OptionalField)
            .HasMaxLength(256);

        builder.HasIndex(e => e.RequiredField);
    }
}
```

### 常见模式

#### 表配置

```csharp
builder.ToTable("[TableName]", [Project]DomainConsts.DbSchemaName);
```

#### 主键

```csharp
builder.HasKey(e => e.Id);
```

#### 必填属性

```csharp
builder.Property(e => e.RequiredField)
    .IsRequired()
    .HasMaxLength(128);
```

#### 可选属性

```csharp
builder.Property(e => e.OptionalField)
    .HasMaxLength(256);
```

#### 索引

```csharp
builder.HasIndex(e => e.RequiredField);
```

#### 唯一约束

```csharp
builder.HasIndex(e => e.Code).IsUnique();
```

#### 一对多关系

```csharp
builder.HasMany(e => e.Children)
    .WithOne()
    .HasForeignKey(c => c.[Domain]Id)
    .OnDelete(DeleteBehavior.Cascade);
```

## Related

- [DbContext 配置](./dbcontext-configuration.md)
