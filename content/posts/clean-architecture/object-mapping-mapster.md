+++
title = 'Object Mapping'
date = 2024-10-28T19:58:16+03:00
draft = false
tags = ['Clean Architecture']
+++

# Object Mapping with Mapster

Object mapping is the process of transferring properties from one object to another, commonly used in modular or layered applications. This approach is particularly valuable in systems with clean architecture or domain-driven design, allowing seamless data transfer between modules or mapping request objects to data models, commands, or database entities.

We will explore object mapping using the Mapster library in C#, a fast and flexible alternative to other mapping libraries like AutoMapper.

## Installation

To add Mapster to your project, run:
```bash
dotnet add package Mapster
```

## Basic Mapping with `Adapt`

With Mapster, we can replace manual mapping code with a single `Adapt` method, which automatically maps properties by matching names between source and destination objects.

```csharp
using Mapster;

var user = UserGenerator.GenerateRandom();
var userResponse = user.Adapt<UserResponse>();

Console.WriteLine(user);
Console.WriteLine(userResponse);
```

### Models

Define `User` and `UserResponse` records to be mapped:

```csharp
namespace MapsterPlayground.Entities
{
    public record User(
        int Id,
        string FirstName,
        string LastName
    );
}

namespace MapsterPlayground.Contracts
{
    public record UserResponse(
        int Id,
        string FirstName,
        string LastName
    );
}
```

## Configuring Custom Mappings with `TypeAdapterConfig`

In cases where properties don’t match by name, we can create custom mapping configurations. For example, if `UserResponse` has a `FullName` property that combines `FirstName` and `LastName` from `User`, we can specify this in a custom configuration.

```csharp
var config = new TypeAdapterConfig();
config.NewConfig<User, UserResponse>()
    .Map(dest => dest.FullName, src => $"{src.FirstName} {src.LastName}");

var userResponse = user.Adapt<UserResponse>(config);

Console.WriteLine(user);
Console.WriteLine(userResponse);
```

### Updated `UserResponse` Model

```csharp
namespace MapsterPlayground.Contracts
{
    public record UserResponse(
        int Id,
        string FullName
    );
}
```

## Ignoring Unmapped Properties

Use `IgnoreNonMapped` to ignore properties in the destination that do not have corresponding mapped properties in the source.

```csharp
config.NewConfig<User, UserResponse>()
    .Map(dest => dest.FullName, src => $"{src.FirstName} {src.LastName}")
    .IgnoreNonMapped(true);
```

## Global Configuration with `TypeAdapterConfig.GlobalSettings`

Mapster supports global configuration settings, allowing you to avoid passing custom configurations for each mapping instance.

```csharp
var config = TypeAdapterConfig.GlobalSettings;
config.NewConfig<User, UserResponse>()
    .Map(dest => dest.FullName, src => $"{src.FirstName} {src.LastName}");

var userResponse = user.Adapt<UserResponse>();
```

## Difference Between `NewConfig` and `ForType`

`NewConfig` creates a fresh configuration for a mapping, overriding any previous settings for that pair (e.g., `User` to `UserResponse`). This can replace existing mappings, potentially losing previously defined rules.

In contrast, `ForType` appends new rules to an existing configuration, preserving prior mappings. Use `ForType` to add additional mappings without discarding previous ones.

## Combining Multiple Mapping Rules

Mapster allows defining multiple rules. For example, we can add a rule to adjust the `Id` property.

```csharp
TypeAdapterConfig<User, UserResponse>.NewConfig()
    .Map(dest => dest.FullName, src => $"{src.FirstName} {src.LastName}");

config.ForType<User, UserResponse>()
    .Map(dest => dest.Id, src => src.Id + 1);
```

## Conditional Mapping

Mapster supports conditional mapping. For instance, only map `FullName` if `FirstName` starts with the letter "A":

```csharp
TypeAdapterConfig<User, UserResponse>.NewConfig()
    .Map(
        dest => dest.FullName,
        src => $"{src.FirstName} {src.LastName}",
        src => src.FirstName.StartsWith("A", StringComparison.OrdinalIgnoreCase)
    );
```

## Mapping Multiple Objects to One

You can map multiple objects to a single destination object, such as combining a `User` object and a `TraceId` GUID into `UserResponse`.

```csharp
var traceId = Guid.NewGuid();
var userResponse = (user, traceId).Adapt<UserResponse>();
```

Configuration:

```csharp
TypeAdapterConfig<(User User, Guid TraceId), UserResponse>.NewConfig()
    .Map(dest => dest.TraceId, src => src.TraceId)
    .Map(dest => dest, src => src.User);
```

### Updated `UserResponse` Model with `TraceId`

```csharp
namespace MapsterPlayground.Contracts
{
    public record UserResponse(
        int Id,
        string FirstName,
        string LastName,
        string TraceId
    );
}
```

## Using `AfterMapping` for Post-Mapping Actions

`AfterMapping` can be used for actions after mapping. Here, we log the mapped object to the console.

```csharp
TypeAdapterConfig<(User User, Guid TraceId), UserResponse>.NewConfig()
    .Map(dest => dest.TraceId, src => src.TraceId)
    .Map(dest => dest, src => src.User)
    .AfterMapping(dest => Console.WriteLine(dest));
```

## Using `IValidatable` Interface for Conditional Validation

Mapster can perform actions like validation on destination objects implementing an interface, such as `IValidatable`.

```csharp
public interface IValidatable
{
    void Validate()
    {
        Console.WriteLine("Validating...");
    }
}

config.ForDestinationType<IValidatable>()
    .AfterMapping(dest => dest.Validate());
```

### `UserResponse` Implements `IValidatable`

```csharp
namespace MapsterPlayground.Contracts
{
    public record UserResponse(
        int Id,
        string FirstName,
        string LastName,
        string TraceId
    ) : IValidatable;
}
```

## Using the `Mapper` Instance for Mapping

Mapster also supports using the `Mapper` instance to handle mapping operations, which will utilize global configurations.

```csharp
IMapper mapper = new Mapper();
var userResponse = mapper.Map<UserResponse>(user);

Console.WriteLine(user);
Console.WriteLine(userResponse);
```

## Conclusion

Mapster provides a flexible and fast approach to object mapping in C#. Its support for custom configurations, global settings, and advanced features like conditional mapping and post-mapping actions makes it a powerful tool for projects with clean architecture or domain-driven design.
