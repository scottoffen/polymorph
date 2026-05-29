# Polymorph

A free, open source .NET object-to-object mapping library and drop-in replacement for AutoMapper. Native AOT support, built-in security constraints, and architectural neutrality are foundational design premises, not afterthoughts.

[![NuGet](https://img.shields.io/nuget/v/Polymorph.svg)](https://www.nuget.org/packages/Polymorph)
[![License](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0-blue.svg)](#license)

---

## Why Polymorph?

AutoMapper moved to a commercial license with version 15.0. Polymorph fills the gap for teams that need a free, actively maintained, production-ready mapping library with a familiar profile-based API.

Polymorph is not a fork. It is a clean-room implementation designed from the ground up with the constraints that matter:

- **Native AOT support** via a Roslyn source generator that emits plain, readable C# at compile time
- **Security by design** - maximum object graph nesting depth and circular reference detection are core engine requirements, not opt-in patches
- **An abstractions package** so domain and application layers carry no dependency on the mapping implementation
- **No static API surface** - Polymorph is DI-friendly by construction and trivially mockable in unit tests
- **Strict mode on by default** - unmatched destination properties are configuration errors, not silent omissions
- **SemVer 2.0.0** with a formal deprecation cycle and no breaking changes without a major version increment

---

## Packages

| Package | Purpose |
|---|---|
| `Polymorph.Abstractions` | Contracts and interfaces. Reference this in domain and application layer projects. |
| `Polymorph` | Core implementation, source generator, and analyzer. Reference this at the composition root. |
| `Polymorph.Extensions.DependencyInjection` | Optional `Microsoft.Extensions.DependencyInjection` integration. |

All types live in the `Polymorph` namespace regardless of which package ships them.

---

## Getting Started

### Installation

```bash
dotnet add package Polymorph.Abstractions
dotnet add package Polymorph
```

If using Microsoft DI:

```bash
dotnet add package Polymorph.Extensions.DependencyInjection
```

### Define a profile

```csharp
public class OrderMappingProfile : MappingProfile
{
    public OrderMappingProfile()
    {
        CreateMap<Order, OrderDto>()
            .ForMember(dest => dest.CustomerName, opt => opt.MapFrom(src => src.Customer.Name))
            .ReverseMap();

        CreateMap<OrderLine, OrderLineDto>();
    }
}
```

### Build a mapper (without DI)

```csharp
var configuration = new MappingConfiguration(options =>
{
    options.AddProfile<OrderMappingProfile>();
});

IObjectMapper mapper = configuration.Build();
```

### Build a mapper (with Microsoft DI)

```csharp
services.AddPolymorph(options =>
{
    options.AddProfile<OrderMappingProfile>();
});
```

### Map objects

```csharp
var dto = mapper.Map<Order, OrderDto>(order);
var dtos = mapper.Map<Order, OrderDto>(orders); // IEnumerable, lazily evaluated
```

---

## Native AOT

On non-AOT targets, Polymorph uses expression trees compiled at startup. On Native AOT targets, the Roslyn source generator is activated automatically and emits statically compiled mapping code at build time. No code changes are required - the same profiles and the same `IObjectMapper` interface work on both paths.

To opt into the source generator on non-AOT targets:

```xml
<ItemGroup>
  <Polymorph UseSourceGenerator="true" />
</ItemGroup>
```

---

## Migrating from AutoMapper

Polymorph's configuration model is intentionally familiar. For most codebases, migration is a search-and-replace exercise:

| AutoMapper | Polymorph |
|---|---|
| `IMapper` | `IObjectMapper` |
| `Profile` | `MappingProfile` |
| `MapperConfiguration` | `MappingConfiguration` |
| `config.CreateMapper()` | `configuration.Build()` |
| `services.AddAutoMapper()` | `services.AddPolymorph()` |
| `AssertConfigurationIsValid()` | Automatic - runs during `Build()` |

A planned `Polymorph.Compatibility.AutoMapper` package will provide additional shims for larger codebases, including `CreateMapper()` as an alias for `Build()` and strict mode disabled by default to ease incremental migration.

See [Appendix A of the requirements document](./docs/Requirements.md) for a full compatibility reference.

---

## Target Frameworks

| Framework | Expression Trees | Source Generator | Native AOT |
|---|---|---|---|
| .NET Standard 2.0 | Default | - | - |
| .NET Standard 2.1 | Default | - | - |
| .NET 6 - .NET 10 | Default | Opt-in | - |
| .NET 6 - .NET 10 (AOT) | - | Mandatory | Yes |

---

## Test Coverage

You can generate and open a test coverage report by running the following command in the project root:

```bash
pwsh ./test-coverage.ps1
```

> [!NOTE]
> This is a [PowerShell](https://learn.microsoft.com/en-us/powershell/) script. You must have PowerShell installed to run this command.

---

## License

Polymorph is dual-licensed under the [MIT License](LICENSE-MIT) and the [Apache License 2.0](LICENSE-APACHE). You may use it under either license.