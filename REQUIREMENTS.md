# Requirements Document
## C# Class Library: Polymorph
**Status:** Draft
**Version:** 0.1

---

- [Introduction](#introduction)
- [1. Motivation](#1-motivation)
  - [1.1 Background](#11-background)
  - [1.2 Problems with AutoMapper](#12-problems-with-automapper)
    - [1.2.1 Licensing](#121-licensing)
    - [1.2.2 Native AOT Incompatibility](#122-native-aot-incompatibility)
    - [1.2.3 Debugging and Transparency](#123-debugging-and-transparency)
    - [1.2.4 Configuration Complexity](#124-configuration-complexity)
    - [1.2.5 Implicit Behavior](#125-implicit-behavior)
  - [1.3 Why Microsoft Has Not Solved This Problem](#13-why-microsoft-has-not-solved-this-problem)
  - [1.4 The Case for Polymorph](#14-the-case-for-polymorph)
- [2. Scope, Goals, and Non-Goals](#2-scope-goals-and-non-goals)
  - [2.1 Scope](#21-scope)
  - [2.2 Goals](#22-goals)
    - [2.2.1 Open Source Replacement for AutoMapper](#221-open-source-replacement-for-automapper)
    - [2.2.2 Security by Design](#222-security-by-design)
    - [2.2.3 Native AOT Support](#223-native-aot-support)
    - [2.2.4 Enterprise Suitability](#224-enterprise-suitability)
    - [2.2.5 Compatibility Commitments](#225-compatibility-commitments)
    - [2.2.6 .NET Target Coverage](#226-net-target-coverage)
    - [2.2.7 Architecture-Friendly Design](#227-architecture-friendly-design)
    - [2.2.8 Structural Neutrality](#228-structural-neutrality)
    - [2.2.9 Testability](#229-testability)
    - [2.2.10 Extensibility](#2210-extensibility)
    - [2.2.11 Simplicity](#2211-simplicity)
    - [2.2.12 Transparency](#2212-transparency)
    - [2.2.13 Consistent Public API Surface](#2213-consistent-public-api-surface)
    - [2.2.14 Performance](#2214-performance)
  - [2.3 Non-Goals](#23-non-goals)
    - [2.3.1 IQueryable Projection and Entity Framework Core Integration](#231-iqueryable-projection-and-entity-framework-core-integration)
    - [2.3.2 Static API Facade](#232-static-api-facade)
    - [2.3.3 Explicit Mode](#233-explicit-mode)
    - [2.3.4 Object Flattening](#234-object-flattening)
    - [2.3.5 Native AOT on .NET Standard Targets](#235-native-aot-on-net-standard-targets)
    - [2.3.6 Memory Consumption and Large Collection Mapping](#236-memory-consumption-and-large-collection-mapping)
    - [2.3.7 `IMappingAction` and DI-Resolved Hooks](#237-imappingaction-and-di-resolved-hooks)
    - [2.3.8 Open Generic Type Mapping](#238-open-generic-type-mapping)
    - [2.3.9 Naming Convention Transforms](#239-naming-convention-transforms)
    - [2.3.10 `ForPath` - Nested Destination Member Mapping](#2310-forpath---nested-destination-member-mapping)
- [3. Functional Requirements](#3-functional-requirements)
  - [3.1 Profile-Based Configuration](#31-profile-based-configuration)
  - [3.2 Engine Selection](#32-engine-selection)
  - [3.3 Convention-Based Mapping](#33-convention-based-mapping)
    - [3.3.1 Name Matching](#331-name-matching)
    - [3.3.2 Type Compatibility](#332-type-compatibility)
    - [3.3.3 Source Property Accessibility](#333-source-property-accessibility)
    - [3.3.4 Inherited Properties](#334-inherited-properties)
    - [3.3.5 Static Properties](#335-static-properties)
    - [3.3.6 Getter-Only and Read-Only Destination Properties](#336-getter-only-and-read-only-destination-properties)
  - [3.4 Reverse Maps](#34-reverse-maps)
  - [3.5 Map Configuration Inheritance](#35-map-configuration-inheritance)
  - [3.6 ConstructUsing](#36-constructusing)
  - [3.7 BeforeMap Hooks](#37-beforemap-hooks)
  - [3.8 AfterMap Hooks](#38-aftermap-hooks)
  - [3.9 Custom Type Converters](#39-custom-type-converters)
  - [3.10 Custom Value Converters](#310-custom-value-converters)
  - [3.11 Custom Member Value Resolvers](#311-custom-member-value-resolvers)
  - [3.12 Conditional Member Mapping](#312-conditional-member-mapping)
  - [3.13 IgnoreAllExcept](#313-ignoreallexcept)
  - [3.14 Record Type Support](#314-record-type-support)
    - [3.14.1 Destination Type Construction](#3141-destination-type-construction)
    - [3.14.2 Convention Matching for Constructor Parameters](#3142-convention-matching-for-constructor-parameters)
    - [3.14.3 Improvement Over AutoMapper](#3143-improvement-over-automapper)
    - [3.14.4 Source Generator Path](#3144-source-generator-path)
  - [3.15 Nested Object Mapping](#315-nested-object-mapping)
  - [3.16 Collection Mapping](#316-collection-mapping)
  - [3.17 Null Handling](#317-null-handling)
  - [3.18 Member-Level Null Substitution](#318-member-level-null-substitution)
  - [3.19 Value Transformers](#319-value-transformers)
  - [3.20 Map Validation](#320-map-validation)
  - [3.21 Strict Mode](#321-strict-mode)
    - [3.21.1 Strict Mode Property Categories](#3211-strict-mode-property-categories)
  - [3.22 Compilation Behavior and Failure Timing](#322-compilation-behavior-and-failure-timing)
  - [3.23 Defensive Mapping Constraints](#323-defensive-mapping-constraints)
    - [3.23.1 Maximum Object Graph Depth](#3231-maximum-object-graph-depth)
    - [3.23.2 Circular Reference Detection](#3232-circular-reference-detection)
- [4. Non-Functional Requirements](#4-non-functional-requirements)
  - [4.1 Licensing](#41-licensing)
  - [4.2 Strong Naming](#42-strong-naming)
  - [4.3 Versioning](#43-versioning)
  - [4.4 Deprecation Requirements](#44-deprecation-requirements)
  - [4.5 No Static API Surface](#45-no-static-api-surface)
  - [4.6 Extensibility](#46-extensibility)
  - [4.7 Thread Safety](#47-thread-safety)
  - [4.8 API Design Constraints](#48-api-design-constraints)
  - [4.9 XML Documentation](#49-xml-documentation)
  - [4.10 MSBuild Configuration](#410-msbuild-configuration)
    - [4.10.1 Analyzer Diagnostics](#4101-analyzer-diagnostics)
  - [4.11 Source Generator SDK Requirements](#411-source-generator-sdk-requirements)
  - [4.12 Performance Requirements](#412-performance-requirements)
  - [4.13 Performance Benchmarking](#413-performance-benchmarking)
  - [4.14 Observability](#414-observability)
    - [4.14.1 `IPolymorphLogger` Interface](#4141-ipolymorphlogger-interface)
    - [4.14.2 Provided Implementations](#4142-provided-implementations)
  - [4.15 Non-Shipping Projects](#415-non-shipping-projects)
- [5. API Design Principles](#5-api-design-principles)
  - [5.1 General Principles](#51-general-principles)
    - [5.1.1 Interfaces First](#511-interfaces-first)
    - [5.1.2 Immutability](#512-immutability)
    - [5.1.3 Explicit Over Implicit](#513-explicit-over-implicit)
    - [5.1.4 Pit of Success](#514-pit-of-success)
    - [5.1.5 Fail Fast](#515-fail-fast)
    - [5.1.6 Minimal Surface Area](#516-minimal-surface-area)
    - [5.1.7 Null Handling and Argument Validation](#517-null-handling-and-argument-validation)
    - [5.1.8 Stability Commitment](#518-stability-commitment)
    - [5.1.9 Namespace Conventions](#519-namespace-conventions)
  - [5.2 Primary Abstraction](#52-primary-abstraction)
  - [5.3 Profile-Based Configuration](#53-profile-based-configuration)
  - [5.4 Mapper Initialization](#54-mapper-initialization)
  - [5.5 Enumeration Types](#55-enumeration-types)
  - [5.6 `PolymorphOptions` Members](#56-polymorphoptions-members)
  - [5.7 Implementable vs. Consumable Interfaces](#57-implementable-vs-consumable-interfaces)
    - [5.7.1 IMappingExpression Members](#571-imappingexpression-members)
  - [5.8 Exception Hierarchy](#58-exception-hierarchy)
    - [MissingMapException](#missingmapexception)
    - [MappingConfigurationException](#mappingconfigurationexception)
    - [MappingExecutionException](#mappingexecutionexception)
  - [5.9 `DepthExceededBehavior`](#59-depthexceededbehavior)
- [6. Package Structure](#6-package-structure)
  - [6.1 Governing Principles](#61-governing-principles)
    - [6.1.1 Packages Are Contracts](#611-packages-are-contracts)
    - [6.1.2 Three Tiers](#612-three-tiers)
    - [6.1.3 Dependency Graph Integrity](#613-dependency-graph-integrity)
    - [6.1.4 Independent Installability](#614-independent-installability)
  - [6.2 Polymorph.Abstractions](#62-polymorphabstractions)
  - [6.3 Polymorph](#63-polymorph)
  - [6.4 Polymorph.Extensions.DependencyInjection](#64-polymorphextensionsdependencyinjection)
    - [AddPolymorph](#addpolymorph)
    - [UsePolymorph](#usepolymorph)
    - [Assembly Scanning (Compatibility)](#assembly-scanning-compatibility)
    - [Multiple Mapper Instances](#multiple-mapper-instances)
  - [6.5 `Polymorph.Compatibility.AutoMapper`](#65-polymorphcompatibilityautomapper)
  - [6.6 Package Metadata Requirements](#66-package-metadata-requirements)
- [References](#references)
- [Appendix A: Compatibility and Migration](#appendix-a-compatibility-and-migration)
  - [A.1 AutoMapper Compatibility](#a1-automapper-compatibility)
  - [A.2 Migration Guidance](#a2-migration-guidance)
  - [A.3 Compatibility Package](#a3-compatibility-package)
- [Appendix B: Technical Risks](#appendix-b-technical-risks)
- [Appendix C: Competitive Landscape](#appendix-c-competitive-landscape)
  - [C.1 AutoMapper](#c1-automapper)
  - [C.2 Mapster](#c2-mapster)
  - [C.3 Mapperly](#c3-mapperly)
  - [C.4 Manual Mapping](#c4-manual-mapping)
  - [C.5 Differentiation Summary](#c5-differentiation-summary)
- [Appendix D: Framework and Engine Matrix](#appendix-d-framework-and-engine-matrix)

---

## Introduction

This document defines the requirements for Polymorph, an open-source .NET class library providing convention-based, AOT-compatible object-to-object mapping. It is intended to serve as the authoritative reference for the library's design, scope, and implementation constraints, and as a communication tool between the author and any contributors, stakeholders, or consumers who want to understand the intent behind the library's design decisions.

The name Polymorph is a nod to polymorphism, a foundational concept in object-oriented programming. Just as polymorphism allows a single interface to represent many underlying types, Polymorph allows a single mapping interface to transform objects across many type pairs - cleanly, predictably, and without ceremony.

---

## 1. Motivation

### 1.1 Background

Object-to-object mapping is a routine concern in layered .NET applications. As data moves between architectural layers - from a database entity to a domain model, from a domain model to a DTO, from a DTO to a view model - it must be transformed from one type into another. The properties may differ in name, type, or structure. Writing this transformation code by hand is straightforward for a single type pair, but across a non-trivial application it becomes a significant source of boilerplate. It is repetitive, error-prone, and expensive to maintain as types evolve.

Dedicated mapping libraries address this by automating the transformation based on conventions, configuration, or generated code. Rather than writing the same property assignments repeatedly, a developer registers a map between two types and lets the library handle the mechanics.

AutoMapper has been the de facto standard for this in the .NET ecosystem for over a decade. It introduced the profile-based configuration model that has become the familiar idiom for mapping in .NET, and its adoption across the ecosystem is widespread. For most of that time, it was the obvious choice: mature, well-documented, open source, and free.

### 1.2 Problems with AutoMapper

AutoMapper's longevity and widespread adoption have also exposed a number of genuine limitations that have frustrated developers over time.

#### 1.2.1 Licensing

Beginning with version 15.0, AutoMapper is no longer open source. It is now licensed under a commercial dual-license model [R1]. Organizations that fall within the community tier may continue to use it at no cost, but are required to obtain and configure a license key [R2][R3]. Failure to do so results in warning messages in application logs [R1]. Organizations that exceed the community tier thresholds must purchase a commercial license [R2]. Previous versions of AutoMapper remain available under their original open source licenses, but they will not receive updates, bug fixes, or security patches. For organizations that cannot accept a commercial dependency, a mandatory license key, or the uncertainty of future tier changes, AutoMapper is no longer a viable option.

This situation is compounded by a high-severity security vulnerability (GHSA-rvv3-g6hj-g44x / CVE-2026-32933) affecting all pre-commercial versions of AutoMapper [R4][R5]. The vulnerability allows a Denial of Service attack by providing a deeply nested object graph that exhausts the thread's stack memory, triggering an unrecoverable StackOverflowException. The maintainer has confirmed that no patch will be released for version 14.x or earlier [R6]. The fix is available only in the commercial versions 15.1.1 and 16.1.1 and later. This vulnerability is symptomatic of a broader class of undefended failure modes in AutoMapper's mapping engine - including circular reference cycles and mutually recursive type converters - that the library has never formally addressed.

#### 1.2.2 Native AOT Incompatibility

AutoMapper relies heavily on runtime reflection and expression tree compilation, both of which are incompatible with Native AOT and aggressive assembly trimming [R7]. As the .NET ecosystem moves toward AOT compilation - particularly in cloud-native, mobile, and performance-sensitive workloads - AutoMapper becomes an obstacle. It has no stated plans to address this.

#### 1.2.3 Debugging and Transparency

Because AutoMapper generates mapping logic at runtime via expression trees, stack traces are difficult to follow when something goes wrong. Convention-based mapping can silently ignore unmatched properties, making it hard to know whether a mapping is complete or correct. There is no compile-time feedback when a type change breaks a mapping.

#### 1.2.4 Configuration Complexity

AutoMapper's configuration API has changed significantly across major versions, making upgrades painful. Its global configuration state makes unit testing harder, as tests can interfere with one another through shared static configuration.

#### 1.2.5 Implicit Behavior

Convention-based mapping can produce surprising results. Properties can be silently mapped - or silently not mapped - in ways that are not obvious from reading the profile. AutoMapper's implicit nested type mapping compounds this: a new property added to a nested type that happens to name-match a destination property begins being mapped without the consumer's explicit intent, with no diagnostic or warning. This "magic" makes code harder to reason about and harder to review. AutoMapper also provides member-level options such as conditional mapping and value transformation, but the interaction between these options and the order in which they are evaluated is underdocumented and inconsistent. Polymorph addresses this by defining an explicit, documented evaluation order for all member-level options: condition first, null substitution second, value transformation third. The behavior of any combination of options is predictable from the documentation alone.

### 1.3 Why Microsoft Has Not Solved This Problem

A reasonable question is why Microsoft has not addressed object mapping in the .NET base class libraries or in a first-party package. The answer has several dimensions.

Object mapping is an inherently opinionated problem. Different teams want different conventions, different configuration styles, and different trade-offs between explicitness and convenience. Microsoft has generally preferred to provide primitives and let the ecosystem build higher-level abstractions on top of them. A first-party mapper would need to be all things to all developers, which is precisely the kind of scope that produces over-engineered, difficult-to-maintain libraries.

AutoMapper filled the gap well enough, for long enough, that there was no compelling pressure on Microsoft to act. The ecosystem had a solution, even if that solution had limitations.

Microsoft's investment in this space has been channeled into adjacent problems: `System.Text.Json` provides source-generated serialization for JSON, and Entity Framework Core provides its own projection capabilities for database queries. These solve related but distinct problems. Neither is a substitute for general-purpose in-memory object mapping.

Microsoft has not filled this gap, and has given no indication that it intends to.

### 1.4 The Case for Polymorph

Polymorph exists because the combination of requirements that define it is not met by any existing library (see [Appendix C: Competitive Landscape](#appendix-c-competitive-landscape)).

AutoMapper is no longer free. Mapster is free and actively maintained, but its default mode is reflection-based and AOT-incompatible, and it provides no formal compatibility guarantees. Mapperly is a capable AOT-safe source generator, but its API surface is fundamentally different from AutoMapper's, it does not provide a standard mapper interface suitable for DI, it does not offer an abstractions package, and it makes no formal SemVer commitments. See Appendix C for a detailed comparison.

Polymorph addresses all of these gaps. It is dual-licensed under MIT and Apache 2.0, making it freely usable in any context. It supports Native AOT through a Roslyn source generator that emits strongly-typed mapping code at compile time. It provides a profile-based configuration model that is familiar to any AutoMapper user. It ships an Abstractions package that allows consumers to limit transitive dependencies at architectural boundaries. It provides strong-named assemblies for enterprise environments. And it commits explicitly to SemVer compatibility, with no breaking changes without a major version increment.

What distinguishes Polymorph is not merely that these capabilities exist, but that they are foundational. Native AOT support, an abstractions package, a DI-free core, and a no-static-API-surface constraint are not additions to Polymorph - they are premises. The architecture follows from them, not the other way around.

Polymorph is intentionally narrow in scope. It does not attempt to solve every object mapping problem - it solves the problems that the majority of real-world line-of-business applications actually encounter, and it solves them well. Its public API is designed to be open at its boundaries, permitting third-party extension packages - such as an IQueryable or Entity Framework Core projection extension - without requiring changes to the core library.

Polymorph is also more secure by design than any of its alternatives. The AutoMapper vulnerability (GHSA-rvv3-g6hj-g44x / CVE-2026-32933) exposed a broader class of undefended failure modes - unbounded recursion, circular reference cycles, and mutually recursive type converters - that reflect an engine designed without defensive constraints. Polymorph addresses all of these explicitly from the outset, through a per-operation mapping context that enforces a configurable recursion depth limit and detects circular references by instance identity. These are not patches applied in response to a known vulnerability - they are requirements that shape the engine's design before a single line of implementation is written.

Polymorph also improves on AutoMapper's API design in places where the surface looks identical. A consumer migrating from AutoMapper will find the same call sites - `opt.NullSubstitute(value)`, `opt.Condition(src => ...)`, `AddValueTransformer`, `MaxDepth(n)` - but with stronger guarantees. `opt.NullSubstitute` is typed as the destination member type, so an incompatible substitute value is a compile error rather than a runtime exception. When the maximum depth is reached, `DepthExceededBehavior` gives consumers explicit control over the outcome - silent truncation, hard failure, or a custom side-effect action - rather than always throwing. These are not breaking changes from AutoMapper's perspective - correct existing code compiles unchanged - but they make incorrect code fail earlier and more clearly. Migration compatibility is a floor, not a ceiling.

---

## 2. Scope, Goals, and Non-Goals

### 2.1 Scope

Polymorph is a .NET object-to-object mapping library targeting line-of-business applications. It is designed for developers who need to map objects between architectural layers - entities to domain models, domain models to DTOs, DTOs to view models - without writing repetitive, hand-maintained transformation code, and without taking a dependency on a commercially licensed library.

Polymorph covers the mapping scenarios that the vast majority of real-world applications actually encounter: convention-based property mapping, custom type converters, member value resolvers, reverse maps, collection mapping, and nested object mapping. It does so across a range of .NET targets, from .NET Standard 2.0 through current .NET releases, with a dual-path architecture that provides expression tree-based mapping for conventional targets and a Roslyn source generator for Native AOT.

While the layered architecture description above implies a server-side application with a DI container, Polymorph is explicitly designed for use without one. `MappingConfiguration` can be instantiated directly and `IObjectMapper` resolved manually, making Polymorph equally suitable for contexts where DI is unavailable, undesirable, or simply overkill. These include Unity game development - where `Microsoft.Extensions.DependencyInjection` is typically not used - console utilities and CLI tools, library authors who need mapping internally without imposing a DI dependency on their consumers, constrained or embedded environments, and unit test projects that want to instantiate a mapper directly without a full DI container. DI integration is opt-in and isolated in `Polymorph.Extensions.DependencyInjection`. The core library has no knowledge of or dependency on any DI container.

Concerns beyond in-memory object mapping - IQueryable projection, Entity Framework Core integration, and similar capabilities - are explicitly out of scope for the core library. These may be addressed by future extension packages built on top of Polymorph's public API surface, which is designed to accommodate them without modification to the core.

### 2.2 Goals

#### 2.2.1 Open Source Replacement for AutoMapper

Polymorph must provide a free, permissively licensed alternative to AutoMapper for the subset of its functionality most commonly used in line-of-business applications. The mapping configuration model - profile-based, fluent, and familiar - must be close enough to AutoMapper's that experienced developers can adopt Polymorph with minimal relearning, and that migration from AutoMapper is a tractable effort rather than a rewrite.

#### 2.2.2 Security by Design

Polymorph must defend against the class of failure modes that have historically gone unaddressed in AutoMapper and similar libraries. Specifically, the mapping engine must enforce a configurable maximum recursion depth, detect circular references by instance identity, and propagate these constraints through custom type converter invocations. These constraints must be requirements that shape the engine's design from the outset, not patches applied in response to known vulnerabilities.

The known AutoMapper vulnerability (GHSA-rvv3-g6hj-g44x / CVE-2026-32933) and the broader class of undefended failure modes it represents - unbounded recursion, circular reference cycles, and mutually recursive type converters - are documented in §1.2.1 and addressed by the defensive mapping constraints in §3.23.

#### 2.2.3 Native AOT Support

Polymorph must support Native AOT compilation on .NET 6 and later targets via a Roslyn source generator that emits strongly-typed mapping code at compile time. AOT support must not require consumers to change their mapping configuration or application code - the same profiles and the same `IObjectMapper` interface work regardless of the execution path. On AOT targets, the source generator is mandatory and no runtime reflection or expression tree compilation may occur.

#### 2.2.4 Enterprise Suitability

Polymorph must provide strong-named assemblies, enabling use in environments that require assembly identity verification, including the Global Assembly Cache and enterprise security policies. All packages must be dual-licensed under MIT and Apache 2.0, ensuring that both permissive and patent-protective licensing needs are met without requiring consumers to negotiate a commercial agreement.

#### 2.2.5 Compatibility Commitments

Polymorph must follow Semantic Versioning 2.0.0 [R10] and must not introduce breaking changes without a major version increment. Consumers must be able to upgrade within a major version with confidence that their existing mapping profiles, type converters, and application code will continue to work.

#### 2.2.6 .NET Target Coverage

All shipping Polymorph packages must multi-target the following frameworks:

- `netstandard2.0`
- `netstandard2.1`
- `net6.0`
- `net7.0`
- `net8.0`
- `net9.0`
- `net10.0`

This list represents the union of currently supported .NET LTS releases, the current STS release, and the .NET Standard contracts needed for Unity and legacy .NET Framework consumers. New target frameworks must be added when Microsoft releases a new LTS or current version; deprecated targets may be removed only with a major version increment, consistent with §4.3.

The analyzer, source generator, and code-fix projects target `netstandard2.0` exclusively, as required by the Roslyn host. Non-shipping support projects target frameworks documented in §4.15 and in the design document.

Support for a new target must not introduce breaking changes to existing targets.

#### 2.2.7 Architecture-Friendly Design

Polymorph must be designed to fit cleanly into layered architectures without imposing unnecessary dependencies. The `Polymorph.Abstractions` package must contain all contracts required for consuming the mapper, with no dependency on the mapping implementation. Domain and application layer projects must be able to depend on `Polymorph.Abstractions` alone. The full implementation must be referenced only at the composition root. DI integration must be isolated in a separate package so that consumers who do not use `Microsoft.Extensions.DependencyInjection` are not forced to take that transitive dependency.

#### 2.2.8 Structural Neutrality

Polymorph must impose no application structure on consuming code. It receives a source object and returns a destination object - how that result is used, how the mapper is obtained, and how the application is structured around it are entirely the consuming application's concern. The DI integration package offers a convenient registration model for applications that use `Microsoft.Extensions.DependencyInjection`, but it is opt-in and isolated in a separate package. The core library must never make structural choices on behalf of the consuming application.

#### 2.2.9 Testability

Polymorph must be designed so that consuming code can be unit tested without instantiating a real mapper. The primary abstraction, `IObjectMapper`, must be an interface that can be mocked or substituted in tests without any dependency on the mapping implementation or configuration. The absence of a static API surface is a direct consequence of this goal - a static facade cannot be substituted in a test without global state manipulation, which makes tests fragile and order-dependent. Consumers who wrap `IObjectMapper` behind their own interface gain an additional layer of substitutability at no extra cost.

#### 2.2.10 Extensibility

Polymorph must provide well-defined extensibility points that allow third-party packages to extend its capabilities without modification to the core library. In particular, the public API surface must expose sufficient abstraction that a future IQueryable or Entity Framework Core projection extension could be built on top of `IObjectMapper` and `MappingProfile` without requiring changes to either. Extensibility points must be designed against interfaces, consistent with the testability goal in 2.2.9, so that extension packages themselves remain testable in isolation.

#### 2.2.11 Simplicity

Polymorph must be simple for consuming developers to adopt and use. A developer familiar with AutoMapper must be able to register profiles, configure the mapper, and perform mappings with minimal relearning. A developer unfamiliar with any mapping library must be able to accomplish the same without consulting extensive documentation. Simplicity is a design constraint that applies to the public API surface, the configuration model, and the getting-started experience. Complexity that is not justified by a concrete consumer need must not be introduced. Where trade-offs arise between internal elegance and external simplicity, external simplicity wins.

#### 2.2.12 Transparency

Polymorph must make mapping behavior visible and verifiable at the earliest possible point.

On the source generator path, Polymorph must emit plain, readable C# mapping code that is steppable in a debugger and visible in the generated output. When strict mode is enabled via the `<Polymorph>` MSBuild item, unmatched destination properties must be reported as build-time diagnostics, surfacing configuration errors at compile time. This applies to the statically analyzable subset of profile configuration - direct `CreateMap<TSource, TDestination>()` calls with compile-time-known type arguments and lambda-based member configuration. Profile configurations that cannot be resolved at compile time fall outside this subset and will not benefit from source generator diagnostics, though they remain fully supported on the expression tree path.

On the expression tree path, eager compilation and automatic map validation at startup must ensure that misconfigured or incomplete maps are caught before any mapping is performed. Taken together, these mechanisms must ensure that a correctly configured Polymorph setup leaves no mapping behavior hidden or deferred to production.

#### 2.2.13 Consistent Public API Surface

Polymorph must deliver a single, consistent public API surface across all supported target frameworks and execution paths. A consumer targeting .NET Standard 2.0 with the expression tree engine and a consumer targeting .NET 8 with the source generator must write identical application code - the same `MappingProfile` subclasses, the same `IObjectMapper` interface, and the same fluent configuration methods. The choice of engine must be an infrastructure concern, invisible to the code that performs and consumes mappings.

This consistency applies to the application-level API surface. Certain infrastructure-level behaviors differ between the expression tree and source generator paths - specifically, how strict mode is configured, how and when configuration errors are surfaced, and what subset of profile configuration benefits from build-time diagnostics. These differences are documented explicitly in the relevant sections and are considered acceptable variation at the infrastructure level, not violations of this goal.

#### 2.2.14 Performance

Polymorph must not impose unnecessary runtime overhead on mapping operations. On non-AOT targets, the expression tree engine must compile maps eagerly at startup by default, ensuring that no compilation penalty is incurred on the first mapping call. On AOT targets, the source generator emits plain C# mapping code at compile time, producing performance equivalent to hand-written mapping with no runtime overhead whatsoever. Consumers who opt into the source generator on non-AOT targets gain the same benefit. Polymorph must perform at least as well as AutoMapper 14.x for equivalent operations on the expression tree path. Concrete performance requirements are defined in §4.12 and benchmarking obligations are defined in §4.13. Compilation mode is configurable via `MappingConfiguration` as defined in §5.6. Engine selection for non-AOT targets is configured via the `<Polymorph>` MSBuild item as defined in §4.10.

### 2.3 Non-Goals

Defining what Polymorph does not do is as important as defining what it does. The boundaries below are deliberate - they keep the core library focused and ensure that optional or specialized concerns are addressed at the right layer rather than accumulated in the core. They are the concrete expression of the simplicity and structural neutrality goals in §§2.2.8 and 2.2.11.

#### 2.3.1 IQueryable Projection and Entity Framework Core Integration

Mapping IQueryable sources requires translating mapping configuration into expression trees suitable for database query providers, which is a substantially different problem from in-memory object mapping. It is not a responsibility of the core library. This may be addressed in a future extension package built on top of Polymorph's public API surface.

#### 2.3.2 Static API Facade

Polymorph does not provide a static `Mapper.Map<T>()` entry point. Consumers who require one may build it on top of `IObjectMapper`. This is a direct consequence of the testability and structural neutrality goals - a static facade introduces global state, makes consuming code harder to test, and imposes an implicit structural choice on the application. It is a deliberate omission, not an oversight.

#### 2.3.3 Explicit Mode

A mode in which every mapped member must be explicitly configured, with no conventions applied, is not a v1 requirement. Strict mode, which is enabled by default and treats unmatched destination properties as configuration errors, addresses the most common motivation for explicit mode without the verbosity of requiring every member to be spelled out. Explicit mode may be added in a future minor version based on consumer feedback.

#### 2.3.4 Object Flattening

Automatic flattening of nested properties - for example, mapping `Order.Customer.Name` to a destination property named `CustomerName` by convention - is not a v1 requirement. Consumers who need this behavior may configure it explicitly using `ForMember`. Flattening may be added as a convention in a future minor version.

#### 2.3.5 Native AOT on .NET Standard Targets

Native AOT is a runtime feature that requires .NET 6 or later. It is not available on .NET Standard 2.0 or 2.1 regardless of the SDK version used to build. This is a platform constraint, not a Polymorph limitation.

#### 2.3.6 Memory Consumption and Large Collection Mapping

Polymorph mitigates memory pressure from large collection mapping through lazy evaluation and async streaming. Collection mapping uses `yield return` so that destination collections are not materialized until iterated, and the `MapAsync` overload supports async streaming via `IAsyncEnumerable<T>` for consumers whose source data arrives asynchronously.

However, Polymorph does not attempt to defend against `OutOfMemoryException` in all cases. Consumers who force materialization of very large collections - by calling `ToList()`, passing a mapped collection to a method that buffers it, or mapping synchronously from a large in-memory source - are responsible for bounding the data appropriately. Similarly, object graphs that are wide or deeply nested but within the configured recursion depth limit may consume significant memory. This is the consumer's responsibility to manage. Polymorph's defensive constraints (§3.23) address unbounded recursion and circular references, but do not address general memory pressure.

#### 2.3.7 `IMappingAction` and DI-Resolved Hooks

AutoMapper supports `IMappingAction<TSource, TDestination>` as a DI-resolvable alternative to `BeforeMap` and `AfterMap`, allowing hook logic to receive injected dependencies. Polymorph does not support this pattern. `IObjectMapper` is a singleton whose extension points - converters, resolvers, and hooks - are cached and reused across concurrent mapping operations. Introducing DI-resolved instances into that model would require per-operation resolution from the container, which reintroduces ambient service locator patterns and makes the mapping pipeline dependent on the DI container's lifetime management.

Consumers who need dependency-aware hook logic have two clean migration paths. The first is to inject the dependency into the `MappingProfile` via constructor injection and capture it in a `BeforeMap` or `AfterMap` lambda - one extra line of code with no behavioral difference. The second is to move the post-mapping logic out of the mapper entirely and call it explicitly after `Map()` returns, which is often the cleaner design since a mapper's responsibility is transformation, not side effects.

#### 2.3.8 Open Generic Type Mapping

AutoMapper supports registering maps for open generic types via `CreateMap(typeof(List<>), typeof(Collection<>))`, allowing a single registration to cover all closed variants of a generic type pair. Polymorph does not support this pattern.

Open generic mapping requires runtime type construction - closing the generic type arguments based on the concrete source object type at mapping time. This is fundamentally reflection-based and cannot be statically analyzed by the source generator, which would make open generic maps permanently expression-tree-only and incompatible with AOT. It would also require significant changes to the map registry's lookup strategy - which currently keys on closed `Type` instances - to support an open generic fallback path.

Consumers who use open generic maps in AutoMapper have two migration paths. The first is to register concrete maps for each closed type pair they actually use - in most codebases the number of distinct closed variants is small. The second is to implement an `ITypeConverter` for the generic conversion logic, which encapsulates the transformation without requiring engine support for open generics.

#### 2.3.9 Naming Convention Transforms

AutoMapper supports custom naming conventions at the profile level via `SourceMemberNamingConvention` and `DestinationMemberNamingConvention`, allowing automatic transformation between naming styles - for example, mapping a snake_case source property `customer_name` to a PascalCase destination property `CustomerName` without explicit `ForMember` calls.

Polymorph's convention matching is case-insensitive exact name matching. Naming convention transforms are not supported in v1. This is a deliberate exclusion rather than an oversight. In modern .NET, naming style differences between source and destination objects are typically resolved at the serialisation boundary - via `System.Text.Json` naming policies, Dapper column mapping, or similar - before objects reach the mapper. Consumers who do encounter naming style mismatches may configure the affected members explicitly via `ForMember`. Naming convention transforms may be added in a future minor version based on consumer feedback.

#### 2.3.10 `ForPath` - Nested Destination Member Mapping

AutoMapper supports `ForPath(dest => dest.Address.City, opt => opt.MapFrom(src => src.City))` for mapping to nested destination properties via a path expression. Polymorph does not support this in v1. It is a planned fast-follow for v1.x, targeting consumers with structural asymmetry between source and destination types - a common pattern in large enterprise codebases and legacy system integrations.

The migration path for v1 consumers who use `ForPath` is to register a map for the intermediate type and let convention resolution handle the nested assignment, or to use `ConstructUsing` to construct the destination with the correct nested values directly. Neither requires significant rework for most cases.

The v1 implementation must not foreclose the addition of `ForPath` in a future minor version. See the design document for specific implementation considerations.

---

## 3. Functional Requirements

### 3.1 Profile-Based Configuration

Mapping configuration must be organized into profiles by subclassing `MappingProfile`. Multiple profiles may be registered with a single `MappingConfiguration` instance. Profiles are the canonical means of registering maps and are the only mechanism that supports the full feature set, including source generator diagnostics and AOT-safe mapping emission.

As a convenience, consumers may register individual maps directly using `AddMap<TSource, TDestination>()` on `PolymorphOptions`. Internally, this registers a convention-only map as if it had been defined in a profile, with no fluent configuration surface available. Maps registered this way fall outside the statically analyzable subset and will not benefit from source generator diagnostics or AOT emission. For AOT-safe mapping, explicit profile classes are required. The formal definition of `AddMap` and the registration options object is in §5.6.

Because `AddMap<TSource, TDestination>()` provides no fluent configuration surface, consumers cannot use `ForMember`, `Ignore`, or `WithNullHandling` to resolve strict mode violations on maps registered this way. If strict mode is enabled and the source and destination types do not align perfectly by convention, the engine will throw a `MappingConfigurationException` at initialization with no way to resolve it through the `AddMap` API. Consumers who encounter this must switch to a named profile where full fluent configuration is available.

### 3.2 Engine Selection

On non-AOT targets, Polymorph must default to the expression tree engine. Consumers may opt into the source generator path on .NET 6 and later targets by setting `UseSourceGenerator="true"` in the `<Polymorph>` MSBuild item (§4.10). This opt-in is not available on .NET Standard 2.0 or 2.1, where the source generator is not supported.

On Native AOT targets, the source generator is always used regardless of the `UseSourceGenerator` setting. The expression tree engine must not be used in an AOT context, as it relies on runtime code generation that is incompatible with AOT compilation.

Engine selection must be a build-time decision. On the source generator path, the engine is determined at compile time by the presence and configuration of the `<Polymorph>` MSBuild item and the AOT publication settings. There must be no mechanism to change the active engine at runtime after `MappingConfiguration` has been initialized. The performance implications of each engine are defined in §§4.12 and 4.13. The framework and engine matrix is documented in Appendix D.

### 3.3 Convention-Based Mapping

Polymorph must support automatic property mapping between a source and destination type based on matching property names and compatible types. Convention-based mapping must not require the consumer to enumerate every mapped property explicitly.

Maps must be explicitly registered, even when relying entirely on conventions. The minimum explicit registration for a convention-based map is:

```csharp
CreateMap<SourceType, DestinationType>();
```

No map may be inferred or constructed at runtime without an explicit registration.

The following rules govern convention-based property matching and must be applied consistently on both the expression tree and source generator paths.

#### 3.3.1 Name Matching

Property names are matched case-insensitively. A source property named `customerId` matches a destination property named `CustomerId`.

#### 3.3.2 Type Compatibility

When names match, the source property type must be assignable to the destination property type. Exact type equality is not required - a source property of type `int` may match a destination property of type `long`, and a source property of a derived type may match a destination property of a base type, provided the assignment is valid in C#. When names match but types are not assignable, no match is made. The unmatched destination property is then subject to strict mode if enabled. A type-specific error is not raised; the strict mode unmatched-property error is sufficient and produces a clearer message.

#### 3.3.3 Source Property Accessibility

Convention matching considers only source properties with a public getter. Properties with non-public or absent getters are not considered during convention matching.

#### 3.3.4 Inherited Properties

Convention matching considers all public properties on both the source and destination types, including those inherited from base classes. A property defined on a base class is treated identically to one defined directly on the type being mapped. Note that this applies to property *discovery* within a single `CreateMap<TSource, TDestination>()` call. Inheritance of *map configurations* between related type pairs - for example, applying a base map's `ForMember` configurations to a derived map - requires explicit opt-in via `Include` or `IncludeBase` as defined in §3.5.

#### 3.3.5 Static Properties

Static properties are excluded from convention matching on both the source and destination sides. They may only be mapped via explicit `ForMember` configuration. When a static property appears in a `ForMember` expression on either side, the analyzer must emit `POLYMORPH020` (see §4.10) to alert the consumer that mapping a static property is unusual and likely unintentional. This warning may be suppressed per-map using `IgnoreStaticWarnings()` on the mapping expression (see §5.7.1), or globally via `#pragma warning disable POLYMORPH020`.

#### 3.3.6 Getter-Only and Read-Only Destination Properties

Destination properties vary in writability along two independent axes: whether the property reference itself is settable, and whether the collection type it exposes (if any) is mutable. The rules below cover all combinations.

**Init-only destination properties.** Destination properties with an `init` accessor are included in convention matching. An `init`-only property is semantically writable at object construction time and must be treated equivalently to a settable property for the purposes of mapping and strict mode.

**Getter-only scalar destination properties.** Destination properties with no setter and no `init` accessor - that is, non-collection properties that are not writable under any circumstances - are excluded from convention matching and are also exempt from strict mode. The engine has no write path to such properties, so neither matching them nor flagging them as unmatched serves any useful purpose.

**Settable read-only collection destination properties.** A destination property that has a setter but whose declared type is a read-only collection interface (`IReadOnlyCollection<T>`, `IReadOnlyList<T>`, `IReadOnlyDictionary<TKey, TValue>`, or similar) is included in convention matching. The engine maps it by constructing a new concrete destination collection instance, populating it with the mapped elements, and assigning the new instance through the setter. The consumer may also map it explicitly with `ForMember`. Because the collection reference itself is replaceable through the setter, this case presents no write-path obstacle.

**Getter-only mutable collection destination properties.** A destination property with no setter whose declared type is a mutable collection interface (`ICollection<T>`, `IList<T>`, `ISet<T>`, or a concrete mutable collection type) is excluded from convention matching but is subject to strict mode if unmatched. The consumer must explicitly map or explicitly ignore such a property. Explicit mapping is supported - the engine populates the existing collection instance by calling `Add()` or the equivalent method appropriate for the interface - but this must not occur implicitly by convention. If the existing collection instance is null at mapping time, the engine must throw a `MappingExecutionException`. Explicit mapping of getter-only mutable collection properties via `ForMember` is within the statically analyzable subset when the property type and element types are compile-time-known. The source generator emits the same `Add()`-based population logic as the expression tree path.

**Getter-only read-only collection destination properties.** A destination property with no setter whose declared type is a read-only collection interface (`IReadOnlyCollection<T>`, `IReadOnlyList<T>`, `IReadOnlyDictionary<TKey, TValue>`, or similar) is excluded from convention matching and is exempt from strict mode. There is no write path to either the property reference or the collection contents, so flagging such properties as unmatched strict mode violations would only force the consumer to add a meaningless `Ignore()`. Any attempt to explicitly map such a property must result in a `MappingConfigurationException` at initialization.

**Deliberate divergences from AutoMapper.** The getter-only scalar and collection rules above are applied uniformly in Polymorph. AutoMapper's behavior differs in one respect: AutoMapper automatically ignores getter-only scalar destination properties but does not automatically ignore getter-only collection destination properties, treating the latter as subject to strict mode validation. This inconsistency has no logical basis - it is an AutoMapper oversight rather than a design decision - and Polymorph corrects it by applying the same exclusion rule to all getter-only non-writable destination properties. Consumers migrating from AutoMapper who have getter-only collection properties they have previously been required to explicitly `Ignore()` may remove those `Ignore()` calls in Polymorph.


### 3.4 Reverse Maps

Polymorph must support reversing a registered map from `TSource` to `TDestination` to also support mapping from `TDestination` to `TSource` via `ReverseMap()` on the mapping expression. Reverse maps are subject to the same convention-based rules as forward maps. Custom member configurations defined on the forward map are not automatically applied in reverse and must be configured explicitly on the reverse map if required.

`ReverseMap()` returns an `IMappingExpression<TDestination, TSource>` that supports the same fluent configuration methods as a forward map. Reverse-map member configurations are chained directly after `ReverseMap()`:

```csharp
CreateMap<Order, OrderDto>()
    .ForMember(dest => dest.CustomerName, opt => opt.MapFrom(src => src.Customer.Name))
    .ReverseMap()
    // ReverseMap() returns an IMappingExpression<OrderDto, Order>.
    // The forward ForMember does not apply in reverse - configure it explicitly here.
    .ForMember(dest => dest.Customer, opt => opt.MapFrom(src => new Customer { Name = src.CustomerName }));
```

### 3.5 Map Configuration Inheritance

Polymorph must support explicit inheritance of map configurations between related type pairs via `Include<TDerivedSource, TDerivedDestination>()` and `IncludeBase<TBaseSource, TBaseDestination>()` on the mapping expression. This allows explicit member configurations - `ForMember`, `Ignore`, and `WithNullHandling` - defined on a base type map to be inherited by derived type maps, eliminating the need to repeat those configurations on every derived map.

Map configuration inheritance is opt-in and explicit. Convention-based property matching for the properties of the derived types is always applied regardless of whether inheritance is configured. Configuration inheritance only propagates explicit member configurations; it does not infer or constrain which type pairs must be registered.

`Include` is specified on the base map and names the derived pair that should inherit from it:

```csharp
CreateMap<Order, OrderDto>()
    .ForMember(dest => dest.Status, opt => opt.MapFrom(src => src.StatusCode.ToString()))
    .Include<OnlineOrder, OnlineOrderDto>()
    .Include<MailOrder, MailOrderDto>();

CreateMap<OnlineOrder, OnlineOrderDto>();
CreateMap<MailOrder, MailOrderDto>();
```

`IncludeBase` is specified on the derived map and names the base pair from which it should inherit:

```csharp
CreateMap<Order, OrderDto>()
    .ForMember(dest => dest.Status, opt => opt.MapFrom(src => src.StatusCode.ToString()));

CreateMap<OnlineOrder, OnlineOrderDto>()
    .IncludeBase<Order, OrderDto>();
```

Both forms are equivalent in effect. Consumers may use whichever is more natural for their profile structure.

**Priority order.** When a destination member can be resolved by more than one mechanism, the following priority order applies, from highest to lowest:

1. Explicit configuration on the derived map - `ForMember`, `Ignore()`, and any other `ForMember` shorthand. Because `Ignore()` is syntactic sugar for `ForMember(..., opt => opt.Ignore())`, it carries the same precedence as any other explicit derived map configuration and will override an inherited `ForMember` from the base map.
2. Inherited explicit configuration from a base map via `Include` or `IncludeBase`
3. Convention-based matching

**Recursive inheritance.** `Include` and `IncludeBase` apply recursively through the inheritance chain. A map that inherits from a base map will also inherit any configurations that the base map itself inherits. Consumers need only specify the closest level of inheritance; transitive inheritance is resolved automatically.

**Strict mode interaction.** Inherited `Ignore` configurations satisfy strict mode for the corresponding destination properties on derived maps, exactly as if they had been configured directly. Inherited `ForMember` configurations satisfy strict mode for the corresponding destination properties. Destination properties that are neither matched by convention, explicitly mapped, nor covered by an inherited configuration remain subject to strict mode on derived maps.

**Source generator path.** `Include` and `IncludeBase` are within the statically analyzable subset when the type arguments are compile-time-known. The source generator must correctly resolve and emit inherited configurations when generating mapping code for derived type pairs.

### 3.6 ConstructUsing

Consumers must be able to explicitly configure how a destination object is constructed when the engine cannot automatically select a constructor. `ConstructUsing` accepts a `Func<TSource, TDestination>` factory delegate that receives the source object and returns a fully constructed destination instance.

`ConstructUsing` is required when the destination type has no parameterless constructor and more than one public constructor, since the engine cannot automatically select among multiple constructors. It is not required for destination types with a parameterless constructor or exactly one public constructor - those cases are handled automatically. See §3.14.1 for the full construction rules.

```csharp
CreateMap<Order, OrderDto>()
    .ConstructUsing(src => new OrderDto(src.Id, src.Region));
```

The destination object returned by the factory delegate is passed to `BeforeMap` hooks and then populated by member mapping in the normal way. `ConstructUsing` controls only construction - it does not affect which members are mapped or how.

When `ConstructUsing` is configured, member mapping runs after the factory delegate returns, exactly as it does after any other construction path. For settable destination properties, `ForMember` configurations and convention-based assignments execute normally. Any values the factory sets on settable properties will be overwritten if those properties are also targeted by `ForMember` configurations or convention matching.

For init-only properties and constructor parameters, `ConstructUsing` is in full control - these members can only be set during construction and cannot be assigned afterward. Configuring `ForMember` for an init-only property or constructor parameter on a destination type that uses `ConstructUsing` is a configuration error and throws a `MappingConfigurationException` at initialization. Consumers who need to map init-only properties or constructor parameters on such types must do so inside the factory delegate itself.

`ConstructUsing` with an arbitrary factory lambda is outside the statically analyzable subset. Maps that use `ConstructUsing` fall back to the expression tree path on the source generator path. This fallback is silent and the map remains fully functional at runtime.

### 3.7 BeforeMap Hooks

Polymorph must support pre-mapping hooks via `BeforeMap((src, dest) => { })` on the mapping expression. A `BeforeMap` hook is an `Action<TSource, TDestination>` delegate that is invoked after the destination object has been constructed but before any member mapping occurs. The hook receives the constructed source and destination objects and may inspect or initialize the destination before mapping runs.

The destination object passed to `BeforeMap` is fully constructed - via parameterless constructor, single-constructor convention matching, or `ConstructUsing` factory delegate - but has no mapped values yet. `BeforeMap` fires after construction regardless of which construction path was used.

The full execution order for a single mapping operation is:

1. Destination object constructed - via parameterless constructor, single-constructor convention matching, or `ConstructUsing`
2. `BeforeMap` hooks invoked in registration order
3. Member mapping - convention assignments, `ForMember` configurations, value converters, member value resolvers, and nested map calls
4. `AfterMap` hooks invoked in registration order

Multiple `BeforeMap` calls on the same mapping expression are permitted and are invoked in registration order.

`BeforeMap` does not receive a `MappingContext`, for the same reasons as `AfterMap`. A consumer who invokes the mapper inside a `BeforeMap` lambda initiates a new, independent mapping operation with its own fresh context. A type that is already being mapped in the outer call chain will not be detected as a cycle if it appears in a mapper invocation inside `BeforeMap`, because the inner invocation creates a fresh context in which the outer call chain's active instances are not visible.

`BeforeMap` with an arbitrary lambda is outside the statically analyzable subset. Maps that use `BeforeMap` fall back to the expression tree path on the source generator path. This fallback is silent and the map remains fully functional at runtime.

### 3.8 AfterMap Hooks

Polymorph must support post-mapping hooks via `AfterMap((src, dest) => { })` on the mapping expression. An `AfterMap` hook is an `Action<TSource, TDestination>` delegate that is invoked after all member mapping for the type pair has completed - after convention-based assignments, explicit `ForMember` configurations, value converters, and member value resolvers have all run. The hook receives the fully mapped source and destination objects and may perform additional mutations on the destination.

Multiple `AfterMap` calls on the same mapping expression are permitted and are invoked in registration order.

`AfterMap` does not receive a `MappingContext`. A consumer who invokes the mapper inside an `AfterMap` lambda is initiating a new, independent mapping operation. A type that is already being mapped in the outer call chain will not be detected as a cycle if it appears in a mapper invocation inside `AfterMap`, because the inner invocation creates a fresh context in which the outer call chain's active instances are not visible. That invocation is subject to its own depth and circular reference constraints independently of the outer operation. This is an accepted and documented limitation.

`AfterMap` with an arbitrary lambda is outside the statically analyzable subset. The source generator cannot emit the lambda body as static code. Maps that use `AfterMap` fall back to the expression tree path on the source generator path. This fallback is silent - no build-time diagnostic is emitted - and the map remains fully functional at runtime.

### 3.9 Custom Type Converters

Consumers must be able to define custom conversion logic for a source and destination type pair by implementing `ITypeConverter<TSource, TDestination>`. When a custom type converter is registered for a type pair, it takes precedence over convention-based mapping for that pair. `ITypeConverter<TSource, TDestination>` operates at the whole-type level - it replaces the entire mapping for a type pair.

For lightweight per-member value transformations where only a single value needs converting, consumers should use `IValueConverter<TSourceMember, TDestinationMember>` via `opt.UseConverter<TConverter>()` in a member configuration expression instead. The distinction is: `ITypeConverter` replaces an entire type mapping; `IValueConverter` transforms a single member value.

Each direction of conversion requires its own implementation. Converting `TypeA` to `TypeB` requires an `ITypeConverter<TypeA, TypeB>`, and converting `TypeB` to `TypeA` requires a separate `ITypeConverter<TypeB, TypeA>`. Custom type converters are not automatically reversed by `ReverseMap()` - both directions must be explicitly implemented and registered if bidirectional conversion is required.

Custom type converters must be constructable by the consumer and must not require a DI container.

### 3.10 Custom Value Converters

Consumers must be able to define lightweight per-member value conversion logic by implementing `IValueConverter<TSourceMember, TDestinationMember>`. Value converters are registered per-member using `opt.UseConverter<TConverter>()` inside a `ForMember` configuration expression.

A value converter receives a single source member value and returns a single destination member value. It does not have access to the full source or destination objects. When access to both objects is required to resolve a member value, `IMemberValueResolver<TSource, TDestination, TMember>` should be used instead.

Custom value converters must be constructable by the consumer and must not require a DI container.

Unlike `ITypeConverter<TSource, TDestination>`, `IValueConverter<TSourceMember, TDestinationMember>` operates on a single scalar member value and does not receive a `MappingContext`. It must not invoke `IObjectMapper` internally. A value converter that calls back into the mapper would bypass circular reference detection and depth tracking, since no context is available to propagate. If a member transformation requires invoking the mapper - for example, because the member is a complex type - consumers must use `ITypeConverter` or `IMemberValueResolver` instead, both of which receive the mapping context. This is an enforced constraint, not a convention: the `IValueConverter` interface must not expose a parameter or overload that accepts a `MappingContext`, and its contract documentation must explicitly prohibit internal mapper invocations.

### 3.11 Custom Member Value Resolvers

Consumers must be able to define custom resolution logic for an individual destination member by implementing `IMemberValueResolver<TSource, TDestination, TMember>`. Member value resolvers are configured per-member in the `MappingProfile` using `opt.UseResolver<TResolver>()` inside a `ForMember` configuration expression. Unlike `IValueConverter`, a member value resolver receives both the full source and destination objects, making it suitable for cases where the resolved value depends on multiple source properties or on the state of the partially-mapped destination.

### 3.12 Conditional Member Mapping

Consumers must be able to conditionally suppress a member assignment based on the state of the source object. A condition is specified via `opt.Condition(src => ...)` inside a `ForMember` configuration expression. When the predicate returns `false`, the member assignment is skipped entirely - the destination member retains whatever value it had after construction, and no conversion or resolution logic is evaluated for that member.

The condition predicate receives the full source object of type `TSource`. It must be a pure function with no side effects. The predicate is evaluated at mapping time, not at configuration time.

```csharp
CreateMap<Order, OrderDto>()
    .ForMember(dest => dest.Discount,
        opt => opt.Condition(src => src.Discount > 0));
```

A condition may be combined with any other `ForMember` option - `MapFrom`, `UseConverter`, `UseResolver` - on the same member. The condition is always evaluated first; if it returns `false`, the other options are not invoked.

`opt.Condition` is within the statically analyzable subset when the predicate lambda contains only simple member access or comparison expressions resolvable at compile time. Predicates that reference local variables, call methods, or use complex expressions cause the map to fall outside the statically analyzable subset and fall back to the expression tree path silently.

### 3.13 IgnoreAllExcept

Consumers must be able to suppress all unmapped destination members on a map except a named set, via `IgnoreAllExcept(dest => dest.Prop1, dest => dest.Prop2, ...)` on the mapping expression. All destination members subject to strict mode that are not named in the call are treated as if `Ignore()` had been called on each of them individually.

```csharp
CreateMap<Order, OrderSummaryDto>()
    .IgnoreAllExcept(dest => dest.Id, dest => dest.CustomerName, dest => dest.Total);
```

`IgnoreAllExcept` is additive with explicit `ForMember` configurations on the same expression. A member named in `IgnoreAllExcept` that also has a `ForMember` configuration retains that configuration unchanged. A member not named in `IgnoreAllExcept` that has an explicit `ForMember` configuration also retains it - `IgnoreAllExcept` adds `Ignore` descriptors only for members that have no descriptor at all after all explicit `ForMember` calls have been processed.

`IgnoreAllExcept` may be called at any point in the fluent chain. It is resolved during convention resolution when the full destination member list is known, not at call time. Members ignored via `IgnoreAllExcept` satisfy strict mode exactly as if `Ignore()` had been called on each individually.

`IgnoreAllExcept` is within the statically analyzable subset when all member expressions are simple property access lambdas with compile-time-known members. The compatibility package provides `IgnoreAllPropertiesWithAnException` as a deprecated alias, marked `[Obsolete]` and directing consumers to migrate to `IgnoreAllExcept`.

### 3.14 Record Type Support

Polymorph must support mapping to C# record types, including records with primary constructors that have no parameterless constructor. This is a first-class feature, not a workaround - the consumer's profile code must be identical whether the destination is a class or a record.

#### 3.14.1 Destination Type Construction

Polymorph determines how to construct a destination object based on the destination type's constructor availability:

**Types with a public parameterless constructor** - the engine constructs the object with `new()` and assigns properties individually. `init`-only properties are assigned via object initializer syntax in the compiled expression.

**Types with no parameterless constructor and exactly one public constructor** - the engine matches constructor parameters to source members by name and type, using the same case-insensitive convention rules as property matching (§3.3). Properties covered by constructor parameters are not additionally assigned after construction. `init`-only properties not covered by the constructor are assigned via object initializer syntax. This is the natural path for record types with primary constructors.

**Types with no parameterless constructor and multiple public constructors** - the engine cannot automatically select a constructor. The consumer must explicitly configure construction using `ConstructUsing` on the mapping expression, as defined in §3.6. Attempting to map such a type without this configuration produces a `MappingConfigurationException` at initialization.

**Types with no accessible public constructor** - always a `MappingConfigurationException` at initialization.

#### 3.14.2 Convention Matching for Constructor Parameters

Constructor parameters on the destination type participate in convention matching using the same rules as properties (§3.3). A constructor parameter is considered matched if a source property exists with a case-insensitively matching name and an assignable type. Matched constructor parameters satisfy strict mode for the corresponding destination member - the consumer does not need to add explicit `ForMember` or `Ignore` calls to silence strict mode for parameters covered by convention.

Explicit `ForMember` configurations on the mapping expression apply to both constructor parameters and properties transparently. The engine determines at compile time whether a configured member maps to a constructor parameter or an init-only property and emits the appropriate expression. The consumer uses `ForMember` in either case - there is no separate `ForCtorParam` API.

#### 3.14.3 Improvement Over AutoMapper

AutoMapper requires consumers to use `ForCtorParam` for constructor parameters in addition to `ForMember` for the corresponding properties, resulting in redundant dual configuration for each custom-mapped member on a record type. Additionally, AutoMapper requires `ForAllMembers(opt => opt.Ignore())` to silence strict mode for properties already covered by the constructor, even when no custom mapping is needed.

Polymorph eliminates both requirements. `ForMember` works directly on record destination types. Constructor parameters matched by convention satisfy strict mode without additional configuration. The following example illustrates the difference:

```csharp
// AutoMapper - six property record with two custom source expressions
// requires 6 explicit calls for 2 custom mappings
CreateMap<Order, OrderDto>()
    .ForCtorParam("customerName", opt => opt.MapFrom(src => src.Customer.Name))
    .ForCtorParam("customerEmail", opt => opt.MapFrom(src => src.Customer.Email))
    .ForCtorParam("statusLabel", opt => opt.MapFrom(src => src.Status.ToString()))
    .ForMember(dest => dest.CustomerName, opt => opt.MapFrom(src => src.Customer.Name))
    .ForMember(dest => dest.CustomerEmail, opt => opt.MapFrom(src => src.Customer.Email))
    .ForMember(dest => dest.StatusLabel, opt => opt.MapFrom(src => src.Status.ToString()));

// Polymorph - same record, same mappings
// identical to mapping a class-based destination type
CreateMap<Order, OrderDto>()
    .ForMember(dest => dest.CustomerName, opt => opt.MapFrom(src => src.Customer.Name))
    .ForMember(dest => dest.CustomerEmail, opt => opt.MapFrom(src => src.Customer.Email))
    .ForMember(dest => dest.StatusLabel, opt => opt.MapFrom(src => src.Status.ToString()));
```

#### 3.14.4 Source Generator Path

Record type mapping is within the statically analyzable subset when the destination type and its constructor are compile-time known. The source generator emits object initializer syntax for record construction, using the same `ForMember` analysis as for class-based destinations. No special source generator configuration is required for records.

### 3.15 Nested Object Mapping

When a source type contains a property whose type has a registered map to a corresponding destination property type, Polymorph must apply that registered map automatically when mapping the parent types. Nested mapping does not require additional configuration beyond registering the maps for the nested types.

All nested type pairs must be registered explicitly. If the engine encounters a nested property whose source and destination types have no registered map, it must throw a `MappingConfigurationException` at initialization time - not at runtime when the property is first mapped. Implicit convention-based fallback for unregistered nested type pairs is not supported.

This is a deliberate deviation from AutoMapper, which applies implicit convention mapping to unregistered nested type pairs on the expression tree path. AutoMapper's implicit nested mapping is one of the most common sources of silent misconfiguration: a new property added to a nested type that happens to name-match a destination property begins being mapped without the consumer's explicit intent, with no diagnostic or warning. Requiring explicit registration eliminates this class of surprise entirely. The consumer knows exactly which maps are active because they registered all of them.

### 3.16 Collection Mapping

When a map is registered for `TSource → TDestination`, Polymorph must automatically support mapping between the following collection types without additional registration:

- `IEnumerable<TSource>` → `IEnumerable<TDestination>`
- `ICollection<TSource>` → `ICollection<TDestination>`
- `IList<TSource>` → `IList<TDestination>`
- `TSource[]` → `TDestination[]`

Collection mapping must be implemented lazily using `yield return`. The destination collection must not be materialized until the consumer iterates it. This allows consumers to compose mapped collections with LINQ, paginate, or process elements one at a time without holding the entire destination collection in memory.

When a consumer iterates the returned `IEnumerable<TDestination>` to completion - for example, by calling `ToList()` or passing it to a `foreach` - the engine must produce a `List<TDestination>` as the materialized type. This is an observable behavior: reflection-based code or consumers that cast the result must be able to rely on `List<TDestination>` as the concrete runtime type. Mapping exceptions thrown during element mapping surface at the point of iteration, not at the point of calling `Map`. Consumers who require exceptions to surface immediately must materialize the collection themselves.

Polymorph must also provide a `MapAsync<TSource, TDestination>(IAsyncEnumerable<TSource> source)` overload on `IObjectMapper` that accepts an `IAsyncEnumerable<TSource>` and returns an `IAsyncEnumerable<TDestination>`. This allows consumers to map a stream of objects as they arrive from an async source - such as a database cursor or an API response stream - without buffering the entire source in memory. This overload is not available on .NET Standard 2.0, where `IAsyncEnumerable<T>` is not supported.

On the source generator path, `MapAsync` must be emitted as a statically typed `async IAsyncEnumerable<TDestination>` method using `yield return` with `await foreach` over the source. No runtime type dispatch or reflection may occur. This ensures `MapAsync` is AOT-safe on the source generator path. On the expression tree path, `MapAsync` uses the same compiled per-element delegate as the synchronous collection overload, wrapped in `async IAsyncEnumerable<TDestination>` iteration.

### 3.17 Null Handling

When the source object passed to a mapping operation is null, Polymorph must return null for the destination object rather than throwing an exception. This is the global default behavior.

Null handling is configurable at two levels. At the global level, consumers may opt into throwing a `MappingExecutionException` on null source objects via `MappingConfiguration`. At the per-map level, consumers may override the global setting for a specific type pair using the `WithNullHandling()` fluent method on the mapping expression returned by `CreateMap<TSource, TDestination>()`. The per-map setting takes precedence over the global setting when both are present.

```csharp
CreateMap<Order, OrderDto>()
    .WithNullHandling(NullHandling.Throw);
```

### 3.18 Member-Level Null Substitution

Consumers must be able to specify a substitute value to use when a source member value is null, via `opt.NullSubstitute(TMember value)` inside a `ForMember` configuration expression. When the source member evaluates to null at mapping time, the substitute value is used in place of null for the destination member assignment.

`opt.NullSubstitute` is typed as `TMember` - the same type parameter carried by `IMemberConfigurationExpression<TSource, TDestination, TMember>` at the point of the call. This provides compile-time type safety: the substitute value must be assignment-compatible with the destination member type, and incompatible values produce a compile error at the call site. This is an improvement over AutoMapper, which accepts `object` and defers type checking to runtime.

```csharp
CreateMap<Order, OrderDto>()
    .ForMember(dest => dest.Discount, opt => opt.NullSubstitute(0m))
    .ForMember(dest => dest.CustomerName, opt => opt.NullSubstitute("Unknown"));
```

Member-level null substitution is distinct from object-level null handling (§3.17). Object-level null handling governs the behavior when the source object itself is null. Member-level null substitution governs the behavior when a specific source property value is null while mapping proceeds normally. The two settings are independent and may both be configured on the same map.

`opt.NullSubstitute` may be combined with `opt.Condition` on the same member. The condition is evaluated first; if it returns `false`, the member assignment is skipped entirely and null substitution is not evaluated. If the condition passes and the source member value is null, the substitute value is used.

`opt.NullSubstitute` is within the statically analyzable subset when the substitute value is a compile-time constant or a simple literal expression. The source generator emits the substitution as a null-coalescing expression against the source member.

### 3.19 Value Transformers

Consumers must be able to register global value transformers that apply a type-level transformation to member assignments across all maps. A value transformer is registered on `PolymorphOptions` and specifies a source member type, a destination member type, and a transformation function. When the engine assigns a member whose source and destination member types exactly match a registered transformer's type pair, the transformer is applied to the source value before assignment.

```csharp
options.AddValueTransformer<string, string>(s => s.Trim());
options.AddValueTransformer<decimal, double>(d => (double)d);
```

Value transformers apply to all member assignments where both member types match exactly, regardless of whether the member was resolved by convention or configured explicitly via `ForMember` with `MapFrom`. Type matching is exact - nullability is stripped for the purpose of lookup, so a transformer registered for `string → string` applies to both `string` and `string?` members. Assignability is not considered; a transformer for `int → string` does not apply to `short → string`.

A transformer does not apply to a member when any of the following is true:

- The member is `Ignore` - no assignment occurs.
- The member has an explicit `UseConverter<TConverter>()` or `UseResolver<TResolver>()` configured - the consumer is handling value transformation explicitly.

`Condition` and `NullSubstitute` are orthogonal to value transformers. A condition gates whether the assignment occurs at all; a null substitute provides a fallback when the source value is null. When all three are present on the same member, the condition is evaluated first, the null substitute is applied if the source value is null, and the transformer is applied to the resulting non-null value before assignment.

If two transformers are registered for the same type pair, the last registration wins, consistent with the last-write-wins behavior elsewhere in the configuration model.

Value transformers are global in scope - they apply across all maps registered with the same `PolymorphOptions` instance. They are not configurable per-map or per-profile.

`AddValueTransformer` is AOT-safe. Both type arguments are compile-time known and no reflection is involved. On the source generator path, the transformation is emitted inline at each applicable member assignment site.

### 3.20 Map Validation

Polymorph must validate all registered maps automatically during `MappingConfiguration` initialization. Validation failures must result in a `MappingConfigurationException`. Map validation may be disabled via configuration for scenarios such as conditional profile registration, incremental migration, or partial test configurations. When `CompilationMode` is `Lazy`, map validation at initialization is limited to checks that do not require compilation - full validation occurs when each map is first compiled on use.

The untyped `Map<TDestination>(object source)` overload must be excluded from map validation and strict mode checks. Because the source type is not known until runtime, it cannot be validated at initialization time. If the runtime source type has no registered map to `TDestination`, the engine must throw a `MissingMapException` at the point of invocation.

### 3.21 Strict Mode

Polymorph must treat any destination property that is not matched by a source property or explicitly configured as a configuration error. Strict mode must be enabled by default on both the expression tree and source generator paths.

On the expression tree path, strict mode mismatches are detected during mapper initialization via the `EnforceStrictMode` option in `MappingConfiguration`, resulting in a `MappingConfigurationException`. Strict mode applies to all registered maps regardless of how they were registered - maps created via `AddMap<TSource, TDestination>()` are subject to strict mode in exactly the same way as maps defined in named profiles. `EnforceStrictMode` may be set to `false` in `MappingConfiguration` to disable runtime strict mode enforcement.

On the source generator path, build-time strict mode is configured via the `StrictMode` attribute of the `<Polymorph>` MSBuild item (§4.10). When enabled, unmatched destination properties are reported as build-time diagnostics at the severity specified by `DiagnosticLevel`. This applies to the statically analyzable subset of profile configuration - direct `CreateMap<TSource, TDestination>()` calls with compile-time-known type arguments and lambda-based member configuration. Profile configurations that cannot be resolved at compile time fall outside this subset and will not produce build-time diagnostics. On the source generator path, `EnforceStrictMode` applies only to maps that fall outside the statically analyzable subset - pre-constructed profile instances, runtime-driven registrations, and `AddMap<TSource, TDestination>()` calls that the source generator never analyzed. For maps within the statically analyzable subset, runtime strict mode has no additional effect: if the project compiled, those maps are valid by construction and no unmatched properties remain to detect at startup.

Build-time strict mode and runtime strict mode are independent settings that serve different purposes and must be configured separately. The MSBuild `StrictMode` attribute controls whether unmatched properties are reported as build-time diagnostics during compilation. The `EnforceStrictMode` option in `MappingConfiguration` controls whether unmatched properties cause a `MappingConfigurationException` at application startup. Disabling one does not disable the other. A consumer who sets `StrictMode="false"` in the MSBuild item to suppress build-time diagnostics must also set `EnforceStrictMode` to `false` in `MappingConfiguration` if they wish to suppress runtime enforcement, since `EnforceStrictMode` defaults to `true` regardless of the MSBuild setting.

Consumers using `Polymorph.Compatibility.AutoMapper` must have both `EnforceStrictMode` and the MSBuild `StrictMode` attribute disabled by default to match AutoMapper's permissive behavior, easing incremental migration.

#### 3.21.1 Strict Mode Property Categories

The following destination property categories are subject to strict mode when unmatched:

- Public settable properties (including `init`-only properties)
- Inherited public settable properties from base classes
- Getter-only mutable collection properties (see §3.3.6)

The following destination property categories are explicitly exempt from strict mode regardless of whether they are matched:

- Getter-only scalar properties (no setter, no `init` accessor)
- Getter-only read-only collection properties (`IReadOnlyCollection<T>`, `IReadOnlyList<T>`, `IReadOnlyDictionary<TKey, TValue>`, and similar)
- Static properties

Inherited explicit `Ignore` configurations applied via `IncludeBase` or `Include` (§3.5) satisfy strict mode for the corresponding destination properties on derived maps.

### 3.22 Compilation Behavior and Failure Timing

On the expression tree path, Polymorph must default to eager compilation, wherein all registered maps are compiled during `MappingConfiguration` initialization. Consumers may opt into lazy compilation, wherein maps are compiled on first use. Compilation mode also determines when errors are surfaced - eager compilation surfaces errors at startup as a `MappingConfigurationException`, lazy compilation surfaces errors as a `MappingConfigurationException` the first time the map is invoked. The error is a configuration problem regardless of when it is detected. Eager compilation is strongly recommended and is the default. The `CompilationMode` configuration option is defined in §5.6.

On the source generator path, this requirement does not apply. Mapping code must be emitted as plain C# at compile time - there must be no runtime compilation step, no compilation penalty, and no deferred error surfacing. Configuration errors on the source generator path must be surfaced as build-time diagnostics when strict mode is enabled, or as `MappingConfigurationException` during `MappingConfiguration` initialization for any errors detectable at startup.

### 3.23 Defensive Mapping Constraints

Polymorph must implement defensive constraints to prevent unbounded recursion and circular reference cycles from causing unrecoverable failures - the class of vulnerability documented in §1.2.1 and addressed as a design goal in §2.2.2.

Both constraints are satisfied through a single per-operation mapping context object that tracks all object instances currently being mapped in the active call chain, using reference equality. The context is created fresh for each top-level single-object mapping operation and passed explicitly through every nested mapping call. It must never be shared across concurrent mapping operations. This design is thread-safe by construction and introduces no ambient or global state.

When mapping a collection via `yield return`, a fresh mapping context is created for each element in the collection. The context is scoped to the mapping of a single element and its nested object graph - it is not shared across elements. This is semantically correct: a circular reference within a single element's object graph should be caught, but a reference from one collection element to another is not a circular reference in any meaningful sense. It also ensures that lazy evaluation via `yield return` does not cause the context to go out of scope before mapping actually occurs.

The following illustrates the structure of the mapping context. The implementation details are internal and may differ, but the behavior described here must be preserved:

```csharp
internal class MappingContext
{
    public HashSet<object> ActiveInstances { get; } = new(ReferenceEqualityComparer.Instance);

    // Represents object graph nesting depth - the number of distinct object instances
    // currently in the process of being mapped in the active call chain. This equals
    // the depth of the object graph being traversed, not the number of call stack frames.
    // For a flat object with no nested mapped types, this will be 1. For a collection,
    // each element is mapped with a fresh context, so this never reflects collection size.
    public int CurrentDepth => ActiveInstances.Count;
}
```

Before mapping any object instance, the engine must add it to `ActiveInstances` and remove it in a `finally` block to ensure cleanup even if mapping throws. The two defensive checks serve distinct purposes and defend against different failure modes, as described below.

A shared reference - where the same source object instance is reachable via two distinct paths through the graph, but neither path forms a cycle - is not treated as a circular reference. Because the instance is removed from `ActiveInstances` once its first subtree has finished mapping, it will not be present in the set when encountered via the second path. The engine will map it again, producing a second, independent destination instance. This is intentional: Polymorph does not preserve reference sharing in the destination graph. Consumers who require shared-reference preservation in the destination must handle this themselves. The depth count at any point in the traversal reflects the length of the current path from the root to the node being mapped - not the total number of unique nodes visited - so a shared reference encountered via a second path does not inflate the depth count beyond what the current path warrants.

#### 3.23.1 Maximum Object Graph Depth

Polymorph must enforce a maximum object graph nesting depth when mapping nested object graphs. Without a depth limit, a sufficiently deep object graph can exhaust the thread's stack memory, triggering an unrecoverable `StackOverflowException`.

This constraint defends against the case where different instances of the same type are nested within each other - for example, a `Category` object with a `Children` property of `IList<Category>`, where each child is a distinct instance. Because each instance is different, circular reference detection alone would not catch this case. The depth limit catches it regardless of whether instances are repeated.

`CurrentDepth` represents the number of distinct object instances currently in the process of being mapped in the active call chain - that is, the nesting depth of the object graph being traversed. It does not count call stack frames, and it does not reflect collection size. The default maximum depth must be 32.

When `CurrentDepth` meets the configured limit before mapping an object, the engine must invoke the active `DepthExceededBehavior`. The global behavior is configured via `PolymorphOptions.OnDepthExceeded`. The default is `DepthExceededBehavior.ReturnNull`, which silently returns null for the member that would have exceeded the limit and allows the mapping operation to complete with truncated data. This default preserves application integrity when a malicious or unexpectedly deep payload is received - throwing in this scenario could itself be exploited as a denial-of-service vector.

Consumers who want to be alerted when the depth limit is reached should use `DepthExceededBehavior.Invoke` or `DepthExceededBehavior.InvokeAndThrow` to register a callback. The callback receives a `DepthExceededContext` with sufficient information to log, metric, or alert appropriately.

**Per-map `MaxDepth`:** Individual maps may specify their own depth limit via `MaxDepth(int)` on the mapping expression. The per-map limit is relative - it is added to `CurrentDepth` at the point the engine enters that map to produce an absolute ceiling for that map's subgraph. If the engine is at depth 7 when it enters a map with `MaxDepth(5)`, mapping of that subgraph halts at depth 12. When a per-map limit is set, it governs that type pair exclusively regardless of the global limit, allowing consumers to permit greater depth for known recursive types. The global limit applies to all type pairs without a per-map limit. A per-map `DepthExceededBehavior` may also be specified via `WithDepthExceededBehavior` on the mapping expression, taking precedence over the global behavior for that type pair.

#### 3.23.2 Circular Reference Detection

Polymorph must detect circular references during mapping and fail with a `MappingExecutionException` rather than recursing indefinitely. This constraint defends against the case where the same object instance appears more than once in the graph - for example, when object A holds a reference back to itself, or to an ancestor object that is already being mapped. This is a true circular reference where instance identity, not type identity, is the relevant condition.

Polymorph does not perform build-time cycle detection on the registered type graph. A cycle in the type graph - where type A maps to type B which maps back to type A - does not produce a configuration error, because whether a runtime circular reference occurs depends on the data, not the types. EF Core navigation properties and other bidirectional relationships are common examples of type graph cycles that are safe to map in practice.

Before mapping any object instance, the engine must check whether that specific instance is already present in `MappingContext.ActiveInstances` using reference equality. If it is, a circular reference has been detected and the engine must throw a `MappingExecutionException` with a clear message identifying the cycle.

This requirement also covers mutually recursive type converters - where an `ITypeConverter<A, B>` internally invokes the mapper to produce a value of type `A`, resulting in unbounded recursion across converter boundaries. The mapping context must be propagated through converter invocations so that circular reference detection spans the entire call chain, not only direct recursive calls within the mapping engine itself.

---

## 4. Non-Functional Requirements

### 4.1 Licensing

All Polymorph packages must be dual-licensed under the MIT License [R11] and the Apache License 2.0 [R12].

### 4.2 Strong Naming

All Polymorph packages must be strong-named. Strong naming enables use in environments that require assembly identity verification, including the Global Assembly Cache (GAC) and certain enterprise security policies. Consumers should be aware that strong naming has implications for transitive dependencies: all dependencies of a strong-named assembly must themselves be strong-named.

### 4.3 Versioning

All Polymorph packages must follow Semantic Versioning (SemVer 2.0.0) [R10].

The following constitute breaking changes and require a major version increment:

- Removing or renaming a public type or member.
- Changing a method signature.
- Changing observable behavior that consumers may depend on.
- Dropping support for a target framework.
- Adding members to interfaces that are intended to be implemented by consumers, when those consumers implement the interface directly rather than subclassing the provided abstract base class (see §5.7).

Adding members to implementable interfaces is a minor version change when a corresponding abstract base class exists in `Polymorph.Abstractions` and the new member has a default implementation on that base class. Consumers who subclass the base class are not broken. Consumers who implement the interface directly are responsible for the resulting compile error - this risk is documented in §5.7.

Additive changes to interfaces intended only for consumption (not implementation) are minor version changes. All other additive changes are minor version changes. Bug fixes that do not alter observable behavior are patch version changes.

### 4.4 Deprecation Requirements

Deprecated public API members must be marked with `[Obsolete(message, error: false)]` in the minor version where they are deprecated. The obsolescence message must clearly state what the consuming developer should use instead and which major version will remove the member.

Deprecated members must be removed in the next major version. Removals must never occur without a prior deprecation cycle - a member that has not been marked `[Obsolete]` in a published release must not be removed in the following major version.

Deprecations must be called out explicitly in release notes alongside the `[Obsolete]` annotation in code. A deprecation that appears only in code without a corresponding release notes entry is not considered complete.

### 4.5 No Static API Surface

Polymorph must not expose a static API surface. All functionality must be accessible through instances of `IObjectMapper`. Consumers who require a static entry point may build one on top of `IObjectMapper`. This requirement exists to prevent global state, preserve testability, and ensure compatibility with DI-first architectures.

### 4.6 Extensibility

The public API surface must be designed to permit third-party extension packages without requiring changes to the core library. In particular, `IObjectMapper` and `MappingProfile` must expose sufficient abstraction that a future IQueryable or Entity Framework Core projection extension could be built on top of them.

### 4.7 Thread Safety

`IObjectMapper` implementations must be safe for concurrent use from multiple threads simultaneously. Since all mapping configuration is fixed and immutable after `Build()` is called, mapping operations on the expression tree path must not require external synchronization. On the source generator path, generated mapping methods are statically compiled and must be inherently thread-safe.

`MappingConfiguration` itself is not required to be thread-safe during construction - it is expected to be built on a single thread at application startup and then used as a singleton. Concurrent calls to `AddProfile`, `Build`, or any other configuration method are not supported and produce undefined behavior.

### 4.8 API Design Constraints

The following constraints apply to the public API surface of all Polymorph packages and are verifiable requirements, not merely design guidance.

**Namespace.** All public types across all Polymorph packages must live in the `Polymorph` namespace, regardless of which NuGet package ships them. The sole exception is generated code emitted by the source generator, which must live in the namespace configured by the `Namespace` attribute of the `<Polymorph>` MSBuild item.

**Nullable annotations.** All Polymorph packages must be compiled with `<Nullable>enable</Nullable>`. Public API signatures must use nullable reference type annotations throughout. The return type of `IObjectMapper.Map` must be annotated as `TDestination?` to accurately reflect that null may be returned when the source is null. Non-nullable parameters must never receive null at runtime except where explicitly documented.

**Argument validation.** All public API methods must validate null arguments using `ArgumentNullException.ThrowIfNull` on .NET 6 and later targets. A polyfill implementation of `ArgumentNullException.ThrowIfNull` must be provided for .NET Standard 2.0 and 2.1 targets, maintaining identical validation behavior across all supported frameworks. Null inputs that are documented as valid - such as the source parameter of `IObjectMapper.Map` - are exempt from this requirement.

**`ObjectMapper` subclassability.** `ObjectMapper` must not be sealed. Its constructor must be accessible to derived classes. Subclasses must not be required to override or replicate any mapping logic - all mapping operations must be handled by the base class.

**Exception hierarchy.** All exceptions thrown by Polymorph must derive from `PolymorphException`. All exception types must live in the `Polymorph` namespace and must be shipped in `Polymorph.Abstractions`, so consumers may catch Polymorph exceptions without taking a dependency on the full implementation package.

**Internal encapsulation.** Internal implementation details - including `MappingContext`, the expression tree compilation pipeline, and source generator infrastructure - must not appear in the public API surface. Where Polymorph needs to share implementation details across assemblies within the library, the `internal` visibility modifier and `InternalsVisibleTo` must be used rather than making those details public.

### 4.9 XML Documentation

All public API members across all packages must have complete XML documentation comments. This requirement applies to interfaces, abstract classes, concrete public types, public methods, public properties, and public constructors.

### 4.10 MSBuild Configuration

Sections 4.10 and 4.11 define the build-time infrastructure that underpins the source generator path. Consumers who use only the expression tree engine may skip both sections.

Polymorph source generator behavior must be configurable via a `<Polymorph>` item in the consuming project's MSBuild configuration. Source generator configuration must be expressed at build time because the generator runs during compilation, before any runtime configuration exists - unlike the expression tree engine, which is configured via `MappingConfiguration` at application startup. This provides a single, predictable location for all build-time Polymorph configuration, regardless of whether the consumer uses EditorConfig or any other tooling convention.

```xml
<ItemGroup>
  <Polymorph UseSourceGenerator="true"
             Namespace="MyApp.Generated"
             ClassName="GeneratedMapper"
             StrictMode="false"
             DiagnosticLevel="Error" />
</ItemGroup>
```

All attributes are optional. When not specified, defaults are applied as described in the table below.

| Attribute | Values | Default | Description |
|---|---|---|---|
| `UseSourceGenerator` | `true`, `false` | `false` (non-AOT), `true` (AOT, forced) | Opts into the source generator path on non-AOT targets. On AOT targets, the source generator is always active regardless of this setting. |
| `Namespace` | Any valid namespace | `{AssemblyName}.Mapping` | Namespace for generated mapping code. The assembly name is sanitized to produce a valid C# identifier before use. |
| `ClassName` | Any valid class name | `GeneratedMapper` | Class name for the generated mapper. |
| `StrictMode` | `true`, `false` | `true` (`false` when using `Polymorph.Compatibility.AutoMapper`) | When `true`, unmatched destination properties are reported as build-time diagnostics on the source generator path. Independent of the `EnforceStrictMode` option in `MappingConfiguration`, which controls runtime strict mode enforcement. |
| `DiagnosticLevel` | `Warning`, `Error` | `Warning` | Severity of source generator diagnostics when `StrictMode` is `true`. |

The source generator must sanitize the assembly name when deriving the default namespace, replacing any characters that are not valid in a C# identifier - such as hyphens - with underscores or removing them as appropriate. The sanitization algorithm must be documented so that consumers can predict the derived namespace without having to inspect the generated output.

The `<Polymorph>` item is read by the source generator at compile time. It has no effect at runtime and no corresponding runtime configuration surface.

#### 4.10.1 Analyzer Diagnostics

The Polymorph analyzer ships in the `Polymorph.Abstractions` NuGet package as an analyzer asset, ensuring that any project referencing `Polymorph.Abstractions` receives diagnostics automatically - including domain and application layer projects that implement extension point interfaces without referencing the full implementation package. Code fix providers for selected diagnostics may be added in future releases and will ship alongside the analyzer in `Polymorph.Abstractions`. The source generator ships separately in the `Polymorph` package. Both the analyzer and source generator binaries target `netstandard2.0` as required by the Roslyn host. The analyzer binary must be marked as a `developmentDependency` so that it does not become a transitive runtime dependency of consuming libraries. The following diagnostics are emitted by the analyzer. This table is the authoritative reference for all Polymorph diagnostic identifiers. All diagnostics may be suppressed using standard `#pragma warning disable` or `[SuppressMessage]` mechanisms.

| ID | Severity | Description |
|---|---|---|
| `POLYMORPH000` | Configurable | Unmatched destination property detected at build time on the source generator path. This is the build-time complement to the `EnforceStrictMode` runtime setting - it fires during compilation rather than at application startup. Severity is controlled by the `DiagnosticLevel` attribute of the `<Polymorph>` MSBuild item. The diagnostic is reported at the `CreateMap` invocation expression, not at the individual unmatched property declarations, since there is no single source location for a missing configuration. |
| `POLYMORPH010` | Warning | Direct implementation of an implementable Polymorph interface detected. Subclass the corresponding abstract base class instead to avoid compile errors on future minor version upgrades. |
| `POLYMORPH011` | Warning | A non-readonly instance field or auto-property was detected on an extension point implementation (`ITypeConverter`, `IValueConverter`, or `IMemberValueResolver`). Converter and resolver instances are cached and reused across concurrent mapping operations. Declare the member `readonly`, or annotate it with `[ThreadSafeForMapping]` (defined in `Polymorph.Abstractions`) to suppress this warning when the member is intentionally mutable but thread-safe. |
| `POLYMORPH020` | Warning | A static property appears in a `ForMember` expression on the source or destination side. Mapping static properties is unusual and likely unintentional. Suppress per-map using `IgnoreStaticWarnings()` on the mapping expression, or globally using `#pragma warning disable POLYMORPH020`. |
| `POLYMORPH021` | Warning | `UseConverter<TConverter>()` was called after one or more fluent configuration methods on the same mapping expression. A whole-type converter replaces the entire mapping - `ForMember`, `ConstructUsing`, `BeforeMap`, and `AfterMap` configurations on the same expression are unreachable and will cause a `MappingConfigurationException` at startup. Move `UseConverter<TConverter>()` to be the only call on the mapping expression, or remove it. |

Additional diagnostics will be assigned identifiers and added to this table prior to v1 release. All diagnostic identifiers are stable once published and will not change between minor versions.

### 4.11 Source Generator and Analyzer SDK Requirements

The source generator and analyzer components are loaded by the consumer's Roslyn host at build time. They require a Roslyn host of version 4.8 or later. This is satisfied by:

- .NET 8 SDK or later (for `dotnet build` consumers)
- Visual Studio 2022 17.8 or later (for legacy MSBuild consumers)
- Unity 2022 LTS or later, with caveats described in §4.11.1

Consumers using older tooling can reference and use Polymorph's runtime libraries via the expression tree engine, but will not receive source generator output or analyzer diagnostics. The expression tree engine is fully supported on all target frameworks listed in §2.2.6, including .NET Framework 4.6.1+ via .NET Standard 2.0.

The source generator binary must target `netstandard2.0` as required by the Roslyn analyzer host [R9]. The source generator must implement `IIncrementalGenerator`; the deprecated `ISourceGenerator` interface must not be used [R8].

The source generator can statically analyze the subset of profile configuration expressed as direct `CreateMap<TSource, TDestination>()` calls with compile-time-known type arguments and lambda-based member configuration. This subset is sufficient for the majority of real-world mapping profiles. Profile configurations that cannot be resolved at compile time - such as those driven by runtime reflection, loops, or factory methods - fall outside this subset. They remain fully supported on the expression tree path but will not benefit from source generator diagnostics or AOT emission.

Changes to the output of the source generator that affect observable runtime behavior are breaking changes and require a major version increment. Changes to generated code that do not affect observable behavior are not breaking changes but must be noted in release notes, as some consumers commit generated files to source control.

The generated class must register itself with `MappingConfiguration` automatically at startup using a mechanism that requires no change to the consumer's profile or initialization code. Consumers must not be required to call any additional registration method, reference the generated class by name, or modify their `MappingConfiguration` setup to activate source-generator-emitted maps. The integration mechanism is an implementation detail internal to Polymorph; what is required here is the behavioral contract: the consumer's startup code must be identical regardless of whether the expression tree engine or the source generator is active. Any mechanism that requires the consumer to write different initialization code when switching engines violates the consistent public API surface goal in §2.2.13 and is not permitted.

#### 4.11.1 Unity Consumption

Unity does not consume NuGet analyzer packages via the standard `analyzers/dotnet/cs/` path. Unity consumers who want analyzer or source generator support must extract the analyzer or generator DLL from the published NuGet package and import it into their Unity project per Unity's documented `RoslynAnalyzer` asset label mechanism. Polymorph does not ship Unity-specific packages or asmdef files in v1.

Unity 2022 LTS and Unity 6 ship a Roslyn host compatible with the version pinned by Polymorph's analyzer and generator. Earlier Unity versions are not supported for analyzer or generator consumption. Runtime library consumption via the expression tree engine remains supported on all Unity versions that satisfy the .NET Standard 2.0 contract.

#### 4.11.2 IL2CPP and Native AOT

IL2CPP is Unity's AOT compilation backend, required for iOS, consoles, and WebGL targets. It is conceptually similar to .NET Native AOT but is a separate toolchain with its own compatibility characteristics. The source generator path is the supported route for both .NET Native AOT and Unity IL2CPP scenarios. Polymorph does not commit to expression tree engine compatibility under IL2CPP - consumers targeting AOT-required Unity platforms must use the source generator path or expect runtime failures during expression compilation. The `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]` attributes documented in §5 indicate APIs that are unsafe for both .NET Native AOT and IL2CPP, even though the IL2CPP toolchain does not enforce these attributes at build time.

### 4.12 Performance Requirements

On non-AOT targets, the expression tree engine must be the default. It provides a familiar migration path for AutoMapper users and requires no build-time tooling beyond the standard .NET SDK. Consumers who want compile-time mapping validation and readable, debuggable generated code may opt into the source generator path on .NET 6+ targets. Engine selection behavior is defined in §3.2.

On Native AOT targets, the source generator is always used regardless of the `UseSourceGenerator` setting. No runtime compilation or expression tree evaluation may occur in an AOT context; all mapping logic must be emitted by the source generator at compile time.

On the expression tree path, Polymorph must default to eager compilation to ensure predictable runtime performance. The first invocation of any mapping operation must not incur a compilation penalty under the default configuration. The source generator path has no runtime compilation step - mapping code is emitted at compile time and incurs no startup cost regardless of compilation mode settings.

Polymorph must perform at least as well as AutoMapper 14.x - the last open source version - for equivalent convention-based mapping operations on the expression tree path. AutoMapper 14.x is the minimum acceptable baseline: the library Polymorph replaces must not outperform its replacement. The source generator path is expected to outperform AutoMapper 14.x by construction, since it produces statically compiled mapping code with no runtime overhead.

### 4.13 Performance Benchmarking

The performance requirements stated in §4.12 must be verifiable. The following requirements define how performance is measured and enforced.

The following scenarios must be covered by the benchmark suite at minimum:

- Simple convention-based mapping between a flat source and destination type
- Nested object mapping with registered maps for nested types
- Collection mapping over a representative number of elements
- Mapping with a custom type converter
- Mapping with a custom member value resolver

A BenchmarkDotNet benchmark suite must be maintained alongside the library. Benchmarks must be reproducible and must run against all supported target frameworks. Benchmark results must be included in release notes for any release where performance characteristics change.

The AutoMapper 14.x baseline defined in §4.12 serves as the objective release gate. Any release in which Polymorph's expression tree path performance falls below the AutoMapper 14.x baseline for any benchmark scenario must be corrected before publication - this is a hard block, not a judgment call. Regressions that remain above the AutoMapper 14.x baseline may be released with a documented justification in the release notes explaining the cause and the rationale for accepting the regression.

### 4.14 Observability

`MappingConfiguration` must accept an optional `IPolymorphLogger` parameter. Polymorph must function correctly when no logger is provided - logging is always opt-in. `IPolymorphLogger` must be defined in `Polymorph.Abstractions` and must carry no external dependencies. Placing it in `Polymorph.Abstractions` ensures that non-DI consumers who want to implement logging do not need to reference the full implementation package to do so.

When an `IPolymorphLogger` is provided, Polymorph must log the following:

- Mapper initialization - profiles registered, maps compiled, and compilation duration
- Map validation failures - which maps failed and why
- Strict mode violations - which destination properties were unmatched on the expression tree path
- Mapping execution failures - which type pair failed and the underlying exception

#### 4.14.1 `IPolymorphLogger` Interface

`IPolymorphLogger` mirrors the `ILogger` interface from `Microsoft.Extensions.Logging` exactly, without taking a dependency on that package. It defines a single method:

```csharp
public interface IPolymorphLogger
{
    void Log<TState>(
        PolymorphLogLevel logLevel,
        PolymorphEventId eventId,
        TState state,
        Exception? exception,
        Func<TState, Exception?, string> formatter);
}
```

Convenience extension methods mirroring MEL's `LogTrace`, `LogDebug`, `LogInformation`, `LogWarning`, `LogError`, and `LogCritical` must be provided on `IPolymorphLogger` in `Polymorph.Abstractions`. These extension methods are what Polymorph's internal code uses at all call sites - the single `Log<TState>` method is the implementation contract, not the call contract.

`PolymorphLogLevel` is an enum in `Polymorph.Abstractions` mirroring `Microsoft.Extensions.Logging.LogLevel` exactly, with identical member names and integer values:

```csharp
public enum PolymorphLogLevel
{
    Trace = 0,
    Debug = 1,
    Information = 2,
    Warning = 3,
    Error = 4,
    Critical = 5,
    None = 6
}
```

`PolymorphEventId` is a struct in `Polymorph.Abstractions` mirroring `Microsoft.Extensions.Logging.EventId` exactly, with an `int Id` and `string? Name` property. The `Polymorph.Extensions.DependencyInjection` bridge maps between `PolymorphLogLevel` and `LogLevel`, and between `PolymorphEventId` and `EventId`, using the identical integer values.

#### 4.14.2 Provided Implementations

`Polymorph.Abstractions` must ship the following `IPolymorphLogger` implementations:

**`NullPolymorphLogger`** - discards all log calls. Used as the internal default when no logger is provided, and available to consumers who want to explicitly opt out of logging or suppress logging in tests without passing `null`. Implemented as a singleton via a static `Instance` property.

`Polymorph.Extensions.DependencyInjection` must provide an `ILoggerFactory`-backed implementation of `IPolymorphLogger` that is wired automatically during `AddPolymorph` registration. Consumers do not need to configure this explicitly - if `ILoggerFactory` is registered in the DI container, Polymorph uses it. If it is not registered, `NullPolymorphLogger.Instance` is used.

---

### 4.15 Non-Shipping Projects

The following projects are required as part of the Polymorph repository but do not ship as NuGet packages.

**`Polymorph.Benchmarks`** - A BenchmarkDotNet project measuring mapping performance across all supported target frameworks. Required for verifying performance requirements in §4.12 and producing the benchmark results described in §4.13. Must not be referenced by any shipping package. Must not be included in the NuGet pack output.

**`Polymorph.Generator.Tests`** - A dedicated test project for the source generator, analyzer, and any future code fix providers. Roslyn testing infrastructure requires a separate project to host compilation-based tests for these components. Must not be referenced by any shipping package. Must not be included in the NuGet pack output.

**`Polymorph.Tests`** - The primary unit and integration test project. Required for verifying all behavioral requirements in this document. Must not be referenced by any shipping package. Must not be included in the NuGet pack output.

---

## 5. API Design Principles

Polymorph's API is designed around clarity, predictability, and architectural compatibility. The package name carries the library's personality; the API surface does not. All public contracts use neutral, descriptive names that are immediately familiar to any .NET developer, regardless of their prior experience with object mapping libraries.

### 5.1 General Principles

#### 5.1.1 Interfaces First

All types that consuming code programs against must be defined as interfaces in `Polymorph.Abstractions`. Concrete implementations live in `Polymorph` and should only be referenced at the composition root - the single location in an application where dependencies are wired together. This applies to `IObjectMapper`, `ITypeConverter<TSource, TDestination>`, `IMemberValueResolver<TSource, TDestination, TMember>`, and `IMappingExpression<TSource, TDestination>`. The abstract base classes for implementable interfaces - `TypeConverterBase<TSource, TDestination>`, `ValueConverterBase<TSourceMember, TDestinationMember>`, and `MemberValueResolverBase<TSource, TDestination, TMember>` - also live in `Polymorph.Abstractions`, since consumers who implement these must be able to do so without referencing the implementation package.

The practical consequence is that consuming code depending only on `Polymorph.Abstractions` must be able to compile and be fully unit tested without a reference to `Polymorph`. Domain and application layer projects must never need to reference the implementation package directly.

`MappingProfile` is a deliberate exception to this principle. It is an abstract class that consumers subclass to define their mapping configuration, and it requires enough implementation detail to function that it cannot live in `Polymorph.Abstractions`. Consumers will reference `MappingProfile` in their profile classes, but those classes are themselves part of the composition root concern - they are registered with `MappingConfiguration` at startup and are never directly referenced by domain or application layer code.

#### 5.1.2 Immutability

`MappingConfiguration` must be immutable after `Build()` is called. Any attempt to register additional profiles, modify configuration options, or alter map definitions after `Build()` has been called must throw a descriptive `MappingConfigurationException`. This eliminates a class of concurrency bugs and makes configuration intent explicit - a mapper is built once, validated once, and then used. Calling `Build()` more than once is safe - subsequent calls return the already-built `IObjectMapper` instance without repeating the build pipeline.

`MappingProfile` subclasses are effectively immutable by design. Map definitions are expressed in the profile constructor and are not modifiable after construction. Consuming code must not attempt to modify a profile after it has been registered with `MappingConfiguration`.

`IObjectMapper` implementations must be thread-safe for concurrent mapping operations. Since all configuration is fixed at initialization time, the mapping operations themselves must not require synchronization on the expression tree path. On the source generator path, generated mapping methods are statically compiled and must be inherently thread-safe. The formal thread safety requirement is defined in §4.7.

#### 5.1.3 Explicit Over Implicit

Polymorph must never make silent behavioral decisions on behalf of the consuming application. Where a decision has observable consequences - whether a null source throws or returns null, whether unmatched destination properties are errors or silently ignored, whether maps are compiled eagerly or lazily - it must be explicit in consuming code or configuration. Default values must be documented and justified, and deviations from defaults must be visible at the call site.

Convention-based mapping is not a violation of this principle. Mapping properties by matching name and type is a well-understood, predictable convention that consuming developers can reason about without consulting documentation. Silent behavior that cannot be predicted from the code alone - such as silently dropping unmatched properties without warning - is a violation of this principle, and is addressed by strict mode being enabled by default (§3.21).

#### 5.1.4 Pit of Success

The API must be designed so that the correct, safe usage pattern is also the easiest one. Dangerous or incorrect usage must require more effort than correct usage. Concretely:

- Strict mode must be on by default. Consumers who want permissive behavior must explicitly opt out.
- Eager compilation must be the default. Consumers who want lazy compilation - and accept that errors will surface at runtime rather than startup - must explicitly opt in.
- `IObjectMapper` must be the natural dependency for consuming code. Depending on the concrete `ObjectMapper` class must require deliberate effort - it is never the path of least resistance.
- Maps registered via `AddMap<TSource, TDestination>()` on the DI container must clearly document their limitations - no AOT support, no source generator diagnostics - so that consumers who need those features are guided toward profiles rather than away from them.

#### 5.1.5 Fail Fast

Polymorph must surface errors at the earliest possible moment and avoid committing resources to work that will fail. This principle applies at every layer - configuration, compilation, and runtime dispatch - and governs the ordering of checks within each.

At the configuration layer, map validation and strict mode enforcement run during `Build()` by default. Consumers must not be able to invoke a misconfigured mapper. The `UsePolymorph()` middleware extension exists specifically to push this moment to application startup rather than first use.

At the compilation layer, cheaper checks run before more expensive ones. Validation runs before compilation - there is no point compiling a map that is already known to be invalid. Within a compiled delegate, the null source check is emitted before `context.Enter` - there is no point allocating context state or performing depth and circular reference checks for a source object that will be immediately rejected.

At the dispatch layer, map existence is verified before a `MappingContext` is allocated. A `MissingMapException` is thrown immediately if no map is registered for the type pair, before any per-operation state is created.

Lazy compilation mode is an explicit opt-out from this principle at the compilation layer. Consumers who opt in accept that map errors will surface on first use rather than at startup. This trade-off is documented and intentional, but lazy mode is not the default precisely because it violates fail fast.

#### 5.1.6 Minimal Surface Area

The public API surface must be kept as small as possible while still meeting all functional requirements. Types, methods, and properties that are not required by consuming code must not be public. A smaller surface area means fewer breaking changes, easier documentation, and less for consuming developers to learn.

This principle applies with particular force to `Polymorph.Abstractions` - every type added to the abstractions package creates an obligation to maintain it indefinitely, since it may be implemented or depended upon by third-party code outside Polymorph's control. New types must not be added to `Polymorph.Abstractions` without careful consideration of the long-term maintenance burden they represent. `IPolymorphLogger` is a deliberate example of this principle applied correctly - it is an interface that non-DI consumers must implement to integrate logging, so it belongs in `Polymorph.Abstractions` where it can be accessed without a dependency on the implementation package.

Internal implementation details - including `MappingContext`, the expression tree compilation pipeline, and source generator infrastructure - must not appear in the public API surface under any circumstances. The formal encapsulation requirement governing how implementation details are shared across assemblies is defined in §4.8.

#### 5.1.7 Null Handling and Argument Validation

All public API methods must validate null arguments consistently, and all packages must be compiled with nullable reference type annotations enabled. The return type of `IObjectMapper.Map` is annotated as `TDestination?` to accurately reflect that null may be returned when the source is null. These are formal requirements defined in §4.8. The design intent is that consuming code that depends only on `Polymorph.Abstractions` can rely on nullable annotations as a truthful contract, not merely a suggestion.

#### 5.1.8 Stability Commitment

Deprecated public API members must follow the deprecation cycle defined in §4.4 (Deprecation Requirements). The design intent behind this policy is that consuming developers - particularly those who implement or depend on types in `Polymorph.Abstractions`, which may be maintained by third parties outside Polymorph's control - deserve advance notice and a clear migration path before any breaking change takes effect.

#### 5.1.9 Namespace Conventions

All public types across all Polymorph packages must live in the `Polymorph` namespace, regardless of which NuGet package ships them. This is a formal requirement defined in §4.8. The design intent is that consuming code never needs to change its `using` statements when referencing types from `Polymorph.Abstractions`, `Polymorph`, or `Polymorph.Extensions.DependencyInjection` - a consumer who references `IObjectMapper` always writes `using Polymorph;` regardless of which package provides it.

The exception is generated code emitted by the source generator, which lives in the namespace configured by the `Namespace` attribute of the `<Polymorph>` MSBuild item (§4.10), defaulting to `{AssemblyName}.Mapping`. This namespace is consumer-controlled and intentionally separate from the `Polymorph` namespace.

### 5.2 Primary Abstraction

The primary abstraction is `IObjectMapper`, defined in the `Polymorph` namespace and shipped in `Polymorph.Abstractions`. All consumer code should depend on `IObjectMapper` rather than any concrete implementation. This makes the mapper trivially mockable in unit tests and substitutable at the composition root.

```csharp
public interface IObjectMapper
{
    TDestination? Map<TSource, TDestination>(TSource source);

    [RequiresDynamicCode("Runtime type dispatch cannot be statically analyzed. Use Map<TSource, TDestination> instead.")]
    [RequiresUnreferencedCode("Runtime type dispatch cannot be statically analyzed. Use Map<TSource, TDestination> instead.")]
    TDestination? Map<TDestination>(object source);

    IEnumerable<TDestination> Map<TSource, TDestination>(IEnumerable<TSource> source);

    // Not available on .NET Standard 2.0
    IAsyncEnumerable<TDestination> MapAsync<TSource, TDestination>(
        IAsyncEnumerable<TSource> source);
}
```

The fully generic overload `Map<TSource, TDestination>(TSource source)` is the preferred overload in all cases where the source type is known at compile time, which covers the vast majority of real-world mapping scenarios. Consumers should use this overload wherever possible.

The untyped overload `Map<TDestination>(object source)` exists for scenarios where the source type is genuinely unknown at compile time - for example, processing a heterogeneous collection of objects or integrating with a reflective framework. Because the source type is resolved at runtime, this overload has the following constraints:

- It must be marked with `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]` to signal to the .NET AOT toolchain that this overload is not AOT-safe. Polymorph's obligation is limited to applying these attributes correctly. The observable failure mode on an AOT target - whether a publish-time warning, a runtime exception, or both - is determined by the .NET AOT toolchain and the consuming project's warning configuration, and is subject to change across .NET versions.
- It must not be used in Native AOT applications. Consumers who call it in an AOT context do so at their own risk.
- On the source generator path, it must fall back to runtime type dispatch - the source type is inspected at runtime and the registered map is looked up dynamically. This is the only overload on the source generator path that requires runtime type resolution.
- It does not participate in strict mode validation or source generator diagnostics, since there is no compile-time source type to validate against.
- If the runtime type of `source` has no registered map to `TDestination`, the engine must throw a `MissingMapException` identifying the runtime type and the destination type.
- It is not available on .NET Standard 2.0. This is a deliberate design decision. The overload is inherently unsafe for AOT and trimming scenarios, and the attributes that communicate this risk - `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]` - cannot be applied on .NET Standard 2.0. Providing the overload without those warnings would silently expose consumers to a risk they have no way to detect. .NET Standard 2.0 consumers are also the group least likely to need runtime type dispatch - they are typically library authors and Unity developers working in constrained environments where dynamic dispatch is undesirable. Withholding the overload there is protective rather than incidental.

`ObjectMapper` is the concrete implementation of `IObjectMapper` and is designed to be subclassed. It must not be sealed, and its constructor must be accessible to derived classes. Subclasses must not need to override or replicate any mapping logic - the base class handles all mapping operations. Subclassing is the supported mechanism for registering multiple independent mapper instances in the same DI container, using the type system as the discriminator rather than named registrations. See §6.4 for the multi-instance DI pattern.

### 5.3 Profile-Based Configuration

Maps are defined by subclassing `MappingProfile`, which is defined in the `Polymorph` package. Consumers subclass `MappingProfile` and configure maps in the constructor. Multiple profiles may be registered with a single `MappingConfiguration`.

```csharp
public class OrderMappingProfile : MappingProfile
{
    public OrderMappingProfile()
    {
        CreateMap<Order, OrderDto>()
            .ForMember(dest => dest.CustomerName, opt => opt.MapFrom(src => src.Customer.Name))
            .WithNullHandling(NullHandling.Throw)
            .ReverseMap();

        CreateMap<OrderLine, OrderLineDto>();
    }
}
```

### 5.4 Mapper Initialization

`MappingConfiguration` must aggregate profiles, validate all registered maps, and produce an `IObjectMapper` instance via `Build()`. By default, all maps must be compiled eagerly during initialization - errors surface immediately at startup. `MappingConfiguration` must be immutable after `Build()` is called.

`MappingConfiguration` provides two constructors:

```csharp
public MappingConfiguration(Action<PolymorphOptions> configure)
public MappingConfiguration(Action<PolymorphOptions> configure, IPolymorphLogger logger)
```

The single-parameter constructor is the standard path. The two-parameter constructor is provided for non-DI consumers who want to supply a logger without using `Polymorph.Extensions.DependencyInjection`. In DI scenarios, the logger is wired automatically by `Polymorph.Extensions.DependencyInjection` from the DI container and the single-parameter constructor is used. Both constructors invoke the configuration delegate immediately and synchronously. Neither constructor is thread-safe; concurrent invocation of the configuration delegate produces undefined behavior.

`PolymorphOptions` is the configuration surface passed to the delegate. Its members are fully specified in §5.6.

`IPolymorphLogger` is defined in `Polymorph.Abstractions` and carries no external dependencies, so non-DI consumers who want logging may implement it without taking a dependency on the full implementation package. The formal logging requirements are defined in §4.14. Consumers using `Microsoft.Extensions.Logging` should use `Polymorph.Extensions.DependencyInjection`, which wires up `ILoggerFactory` to `IPolymorphLogger` automatically. Non-DI consumers who want logging may implement `IPolymorphLogger` against any logging library.

```csharp
// Without logging
var configuration = new MappingConfiguration(options =>
{
    options.AddProfile<OrderMappingProfile>();
    options.AddProfile<CustomerMappingProfile>();

    // Pre-constructed instance - for profiles requiring constructor arguments.
    // Falls outside the statically analyzable subset; expression tree path only.
    options.AddProfile(new RegionalMappingProfile(regionCode));
});

// With logging - non-DI consumers supply their own IPolymorphLogger implementation
var configuration = new MappingConfiguration(options =>
{
    options.AddProfile<OrderMappingProfile>();
    options.AddProfile<CustomerMappingProfile>();
}, logger); // logger implements IPolymorphLogger

IObjectMapper mapper = configuration.Build();
```

### 5.5 Enumeration Types

The following enumeration types are part of the public API surface. All are defined in the `Polymorph` namespace. `NullHandling` is shipped in `Polymorph.Abstractions` because it is referenced by `IMappingExpression.WithNullHandling()`, which is defined there. `CompilationMode` is shipped in `Polymorph` because it is only referenced by `PolymorphOptions`, which also lives in `Polymorph`.

**`CompilationMode`**

Controls when map compilation occurs relative to `Build()`.

| Value | Description |
|---|---|
| `Eager` | All registered maps are compiled immediately when `Build()` is called. Any compilation errors surface at that moment as a `MappingConfigurationException`. This is the default and strongly recommended for production use. |
| `Lazy` | Each map is compiled on its first use. Compilation errors for a given map surface the first time that type pair is mapped, as a `MappingConfigurationException`. Defers compilation of each map beyond `Build()` to the first time that map is invoked at runtime. Accepted trade-off: startup is faster but map errors are deferred to runtime. |

**`NullHandling`**

Controls the behavior of the mapping engine when the source object is null. May be set globally via `PolymorphOptions.NullHandling` or overridden per map via `WithNullHandling()` on the mapping expression.

| Value | Description |
|---|---|
| `ReturnNull` | Returns null as the destination when the source is null. This is the default. |
| `Throw` | Throws a `MappingExecutionException` when the source is null. Use when null source objects represent a programming error that should not be silently propagated. |

### 5.6 `PolymorphOptions` Members

`PolymorphOptions` is a concrete class that carries the full configuration surface for a Polymorph mapper instance. It exposes profile registration methods and configuration properties, and is the type consumers interact with directly in `AddPolymorph` lambdas and `IPostConfigureOptions` implementations.

`PolymorphOptions<TMapper>` inherits from `PolymorphOptions` and adds no additional members. It exists solely as a typed registration vehicle for the DI options infrastructure, allowing each mapper type to have its own independently configurable options instance. Consumers working with a single default mapper never need to reference `PolymorphOptions<TMapper>` directly - they interact only with `PolymorphOptions` through the `AddPolymorph` lambda.

`AddPolymorph` registers `PolymorphOptions<ObjectMapper>` with the options infrastructure internally. `AddPolymorph<TMapper>` registers `PolymorphOptions<TMapper>`. In both cases, the lambda passed to `AddPolymorph` is applied as the initial configuration via `services.Configure<PolymorphOptions<TMapper>>(lambda)`. Consumers may then register `IPostConfigureOptions<PolymorphOptions<TMapper>>` implementations to apply deferred or late-stage configuration. Post-configuration runs after the `AddPolymorph` lambda, after the `IServiceProvider` is fully constructed, and before the `IObjectMapper` factory resolves the options instance to call `Build()`. This is the standard behavior of the options infrastructure.

Profiles registered with any mapper instance - whether via `AddPolymorph` or `AddPolymorph<TMapper>` - are defined identically. Since `PolymorphOptions<TMapper>` inherits from `PolymorphOptions`, the same `MappingProfile` subclasses work with any mapper registration without modification. Profiles receive no reference to `PolymorphOptions` and make no decisions based on which mapper they are registered with. If a profile requires external data it receives it via constructor injection, using `AddProfile(MappingProfile profile)`.

All methods on `PolymorphOptions` return `PolymorphOptions` to support fluent chaining.

**Registration methods:**

| Member | Returns | Description |
|---|---|---|
| `AddProfile<TProfile>()` | `PolymorphOptions` | Constructs a `TProfile` instance via its public parameterless constructor and registers all maps defined in its constructor. This is the recommended registration path and the only one that is AOT-safe. |
| `AddProfile(MappingProfile profile)` | `PolymorphOptions` | Registers all maps from a pre-constructed profile instance. Use when the profile requires constructor arguments. Not AOT-safe; falls outside the statically analyzable subset. |
| `AddMap<TSource, TDestination>()` | `PolymorphOptions` | Registers a convention-only map for the type pair with no member-level configuration. Not AOT-safe; not subject to source generator diagnostics. Consumers who need `ForMember` or other fluent configuration must use a named profile instead. |
| `UseLogger(IPolymorphLogger logger)` | `PolymorphOptions` | Registers a logger instance. Equivalent to supplying a logger via the two-parameter `MappingConfiguration` constructor. If both are provided, the constructor parameter takes precedence. |
| `AddValueTransformer<TSource, TDestination>(Func<TSource, TDestination> transformer)` | `PolymorphOptions` | Registers a global value transformer that applies to all member assignments where the source member type is exactly `TSource` and the destination member type is exactly `TDestination`. Last registration wins for duplicate type pairs. See §3.19. |
| `OnDepthExceeded(DepthExceededBehavior behavior)` | `PolymorphOptions` | Registers the global behavior invoked when the maximum object graph depth is reached. Defaults to `DepthExceededBehavior.ReturnNull`. See §3.23.1 and §5.9. |

**Configuration options:**

| Option | Type | Default | Description |
|---|---|---|---|
| `CompilationMode` | `CompilationMode` | `Eager` | Controls when maps are compiled relative to `Build()`. `Eager` compiles all registered maps immediately when `Build()` is called, so all errors surface at that moment. `Lazy` defers compilation of each map beyond `Build()` to the first time that map is invoked at runtime, so errors for a given map surface on first use. `Build()` itself may be called at any time in either mode - the distinction is purely about when compilation occurs relative to that call. Has no effect on the source generator path, where mapping code is emitted at compile time. |
| `ValidateMaps` | `bool` | `true` | When `true`, all registered maps are validated during `MappingConfiguration` initialization. On the expression tree path, this verifies that maps are compilable and complete. On the source generator path, this verifies that all expected maps have been registered and are routable - the generated code has already been emitted at compile time, but the runtime configuration must still be consistent with it. |
| `EnforceStrictMode` | `bool` | `true` | Controls runtime strict mode enforcement on both paths. When `true`, unmatched destination properties cause a `MappingConfigurationException` at startup. On the source generator path, this applies only to maps outside the statically analyzable subset. Independent of the MSBuild `StrictMode` attribute, which controls build-time diagnostic emission. Disabled by default when using `Polymorph.Compatibility.AutoMapper`. |
| `NullHandling` | `NullHandling` | `ReturnNull` | Global default behavior when the source object is null. `ReturnNull` returns null. `Throw` throws a `MappingExecutionException`. May be overridden per map using `WithNullHandling()` on the mapping expression. |
| `MaxDepth` | `int` | `32` | Global maximum object graph nesting depth. Depth is measured as the number of distinct object instances currently being mapped in the active call chain, not the number of call stack frames. When reached, the active `DepthExceededBehavior` is invoked. May be overridden per map via `MaxDepth(int)` on the mapping expression. See §3.23.1. |

### 5.7 Implementable vs. Consumable Interfaces

Polymorph distinguishes between interfaces intended to be consumed (called by the consumer) and interfaces intended to be implemented (subclassed or implemented by the consumer). This distinction has direct implications for versioning.

**Consumable interfaces** - consumers call these but do not implement them. Adding members is a minor version change.

- `IObjectMapper`
- `IMappingExpression<TSource, TDestination>`

**Implementable interfaces** - consumers must subclass the corresponding abstract base class rather than implementing the interface directly. The base classes, shipped in `Polymorph.Abstractions`, provide default implementations for all members, allowing new members to be added in minor versions without breaking consumers. Consumers who implement the interface directly accept the risk of compile errors on minor version upgrades.

| Interface | Abstract Base Class |
|---|---|
| `ITypeConverter<TSource, TDestination>` | `TypeConverterBase<TSource, TDestination>` |
| `IValueConverter<TSourceMember, TDestinationMember>` | `ValueConverterBase<TSourceMember, TDestinationMember>` |
| `IMemberValueResolver<TSource, TDestination, TMember>` | `MemberValueResolverBase<TSource, TDestination, TMember>` |

The Polymorph analyzer must emit a warning (see §4.10) when direct interface implementation is detected. Consumers with a legitimate reason to implement an interface directly - such as test doubles - may suppress this diagnostic via `#pragma warning disable` or `[SuppressMessage]`.

### 5.7.1 `IMappingExpression<TSource, TDestination>` Members

`IMappingExpression<TSource, TDestination>` is the fluent configuration surface returned by `CreateMap<TSource, TDestination>()`. It is a consumable interface - consumers call its methods but do not implement it. The following table enumerates its public members.

| Member | Returns | Description |
|---|---|---|
| `UseConverter<TConverter>()` | `void` | Registers an `ITypeConverter<TSource, TDestination>` implementation that replaces the entire mapping for this type pair. When a type converter is registered, convention-based property mapping does not run - the converter is responsible for producing the complete destination object. `TConverter` must have a public parameterless constructor. This method is within the statically analyzable subset when `TConverter` is a compile-time-known type. Returns `void` because no further fluent configuration is meaningful after declaring a whole-type converter. Calling `UseConverter<TConverter>()` after any other fluent configuration method on the same mapping expression - including `ForMember`, `ConstructUsing`, `BeforeMap`, or `AfterMap` - throws a `MappingConfigurationException`. The analyzer emits `POLYMORPH021` to catch this pattern at compile time. |
| `ConstructUsing(Func<TSource, TDestination> factory)` | `IMappingExpression<TSource, TDestination>` | Specifies a factory delegate for constructing the destination object when no parameterless constructor exists and the destination has multiple public constructors. Required for destination types with multiple public constructors - omitting it for such types produces a `MappingConfigurationException` at initialization. Not required for records with a single primary constructor, which are handled automatically via convention. See §3.14. |
| `ForMember(dest => dest.Property, opt => ...)` | `IMappingExpression<TSource, TDestination>` | Configures mapping for a specific destination member. Works for both properties and constructor parameters on record types - no separate `ForCtorParam` is needed. The options expression supports `MapFrom`, `Ignore`, `UseResolver`, and `UseConverter<TConverter>`. |
| `Ignore(dest => dest.Property)` | `IMappingExpression<TSource, TDestination>` | Syntactic sugar for `ForMember(dest => dest.Property, opt => opt.Ignore())`. Explicitly excludes a destination member from mapping. Satisfies strict mode for that member. |
| `IgnoreAllExcept(dest => dest.Prop1, ...)` | `IMappingExpression<TSource, TDestination>` | Suppresses all destination members subject to strict mode that are not named in the call, as if `Ignore()` had been called on each individually. Additive with explicit `ForMember` configurations - named members retain any existing configuration and unnamed members with existing `ForMember` configurations also retain them. Resolved during convention resolution when the full destination member list is known. See §3.13. |
| `WithNullHandling(NullHandling)` | `IMappingExpression<TSource, TDestination>` | Overrides the global null handling behavior for this type pair. Takes precedence over the global `NullHandling` configuration option. |
| `MaxDepth(int depth)` | `IMappingExpression<TSource, TDestination>` | Sets a per-map depth limit relative to the current depth at the point the engine enters this map. The per-map limit is added to `CurrentDepth` at entry to produce an absolute ceiling for this map's subgraph. Governs this type pair exclusively regardless of the global `MaxDepth`. See §3.23.1. |
| `WithDepthExceededBehavior(DepthExceededBehavior behavior)` | `IMappingExpression<TSource, TDestination>` | Overrides the global `DepthExceededBehavior` for this type pair. Takes precedence over the global behavior registered via `PolymorphOptions.OnDepthExceeded`. See §5.9. |
| `ReverseMap()` | `IMappingExpression<TDestination, TSource>` | Registers the reverse map and returns an `IMappingExpression` for the reverse direction. Custom member configurations on the forward map do not apply in reverse and must be configured explicitly on the returned expression. |
| `AfterMap(Action<TSource, TDestination> action)` | `IMappingExpression<TSource, TDestination>` | Registers a post-mapping hook invoked after all member mapping has completed. Multiple `AfterMap` calls are permitted and invoked in registration order. The action does not receive a `MappingContext`. Maps using `AfterMap` fall outside the statically analyzable subset and execute on the expression tree path. See §3.8. |
| `BeforeMap(Action<TSource, TDestination> action)` | `IMappingExpression<TSource, TDestination>` | Registers a pre-mapping hook invoked after the destination object is constructed but before any member mapping occurs. Fires after construction regardless of whether the destination was constructed via parameterless constructor, single-constructor convention, or `ConstructUsing`. Multiple `BeforeMap` calls are permitted and invoked in registration order. The action does not receive a `MappingContext`. Maps using `BeforeMap` fall outside the statically analyzable subset and execute on the expression tree path. See §3.7. |
| `Include<TDerivedSource, TDerivedDestination>()` | `IMappingExpression<TSource, TDestination>` | Specifies that the derived type pair `TDerivedSource → TDerivedDestination` should inherit explicit member configurations from this map. Both the derived source and destination types must be assignable from `TSource` and `TDestination` respectively. The derived type pair must be independently registered with `CreateMap`. See §3.5. |
| `IncludeBase<TBaseSource, TBaseDestination>()` | `IMappingExpression<TSource, TDestination>` | Specifies that this map should inherit explicit member configurations from the base type pair `TBaseSource → TBaseDestination`. The base type pair must already be registered. See §3.5. |
| `IgnoreStaticWarnings()` | `IMappingExpression<TSource, TDestination>` | Suppresses `POLYMORPH020` diagnostics for this mapping expression. Use when a `ForMember` configuration intentionally references a static property on either side and the analyzer warning is not desired. This method affects only build-time diagnostics and has no effect at runtime. |

The member options expression passed to `ForMember` supports the following options:

| Option | Description |
|---|---|
| `opt.MapFrom(src => ...)` | Maps the destination member from a lambda expression evaluated against the source object. |
| `opt.Condition(src => ...)` | Suppresses the member assignment when the predicate returns `false`. The predicate receives the full source object. When the condition is false, no conversion or resolution logic is evaluated for that member. May be combined with any other `ForMember` option; the condition is always evaluated first. |
| `opt.NullSubstitute(TMember value)` | Specifies a substitute value to use when the source member evaluates to null. Typed as `TMember` - the destination member type - providing compile-time type safety. The substitute is applied after the condition (if any) passes and before the value is assigned to the destination member. See §3.18. |
| `opt.UseConverter<TConverter>()` | Maps the destination member using an `IValueConverter<TSourceMember, TDestinationMember>` implementation. Used for lightweight single-value transformations. |
| `opt.UseResolver<TResolver>()` | Maps the destination member using an `IMemberValueResolver<TSource, TDestination, TMember>` implementation. Used when the resolver needs access to both the full source and destination objects. |
| `opt.Ignore()` | Explicitly excludes the destination member from mapping. Satisfies strict mode for that member. |

### 5.8 Exception Hierarchy

All exceptions thrown by Polymorph must derive from `PolymorphException`, and all exception types must live in the `Polymorph` namespace and be shipped in `Polymorph.Abstractions`. These are formal requirements defined in §4.8. The exception hierarchy is intentionally minimal - four types, each with a distinct and well-defined role. Consumers should catch the most specific exception type applicable to their scenario.

The four types are `PolymorphException` (abstract base), `MissingMapException`, `MappingConfigurationException`, and `MappingExecutionException`. All exception types expose structured properties in addition to the message string, so consumers can inspect exception details programmatically without parsing messages.

#### `PolymorphException`

Abstract base class for all Polymorph exceptions. Exposes no additional structured properties beyond those inherited from `Exception`.

#### `MissingMapException`

Thrown when a mapping operation is requested for a source and destination type pair for which no map has been registered. This includes nested object mapping scenarios where a nested type pair lacks a registered map.

| Property | Type | Description |
|---|---|---|
| `SourceType` | `Type` | The source type for which no map was found. |
| `DestinationType` | `Type` | The destination type for which no map was found. |
| `ParentSourceType` | `Type?` | The source type of the containing map, when the missing map was encountered during nested object mapping. Null for top-level missing map errors. |
| `ParentMemberName` | `string?` | The name of the property on `ParentSourceType` that triggered the nested map lookup. Null for top-level missing map errors. |

#### `MappingConfigurationException`

Thrown in any of the following scenarios:

- Map validation fails during `MappingConfiguration` initialization - a registered map is invalid or inconsistent.
- Strict mode is enabled and one or more destination properties are unmatched on the expression tree path.
- An attempt is made to register a profile, add a map, or modify configuration options after `Build()` has been called. `MappingConfiguration` is immutable after `Build()`.
- A profile constructor throws an exception during registration.
- `UseConverter<TConverter>()` is called on a mapping expression that already has `ForMember`, `ConstructUsing`, `BeforeMap`, or `AfterMap` configurations. A whole-type converter replaces the entire mapping and is incompatible with member-level configuration.
- `ForMember` is configured for an init-only property or constructor parameter on a destination type that uses `ConstructUsing`. These members can only be set during construction and are unreachable by member mapping.

| Property | Type | Description |
|---|---|---|
| `SourceType` | `Type?` | The source type of the map that failed validation. Null when the error is not specific to a single type pair (e.g. post-`Build()` mutation). |
| `DestinationType` | `Type?` | The destination type of the map that failed validation. Null under the same conditions as `SourceType`. |
| `UnmatchedMembers` | `IReadOnlyList<UnmatchedMemberInfo>?` | The destination members that failed strict mode validation. Null when the exception was not caused by strict mode violations. Each `UnmatchedMemberInfo` exposes `DeclaringType` (`Type`) and `MemberName` (`string`). |

#### `MappingExecutionException`

Thrown in any of the following scenarios:

- A valid, registered map fails at runtime due to an unexpected error during property mapping or type conversion.
- The source object is null and `NullHandling` is configured to `Throw`, either globally or for the specific type pair.
- The maximum object graph nesting depth is reached and the active `DepthExceededBehavior` is `Throw` or `InvokeAndThrow` (§3.23.1).
- A circular reference is detected during mapping - the same object instance appears more than once in the active call chain (§3.23.2).
- Mutually recursive type converters are detected - an `ITypeConverter<A, B>` invokes the mapper in a way that produces a circular call chain (§3.23.2).

| Property | Type | Description |
|---|---|---|
| `SourceType` | `Type` | The source type being mapped when the exception occurred. |
| `DestinationType` | `Type` | The destination type being mapped when the exception occurred. |
| `MemberName` | `string?` | The name of the destination member being assigned when the exception occurred. Null when the failure is not member-specific (e.g. depth exceeded, circular reference). |
| `CurrentDepth` | `int?` | The object graph depth at the point of failure. Populated when the exception was thrown due to the depth limit being reached - that is, when the active `DepthExceededBehavior` is `Throw` or `InvokeAndThrow`. Null when the depth limit was reached but the behavior was `ReturnNull` or `Invoke` (no exception is thrown in those cases), and null for all other failure scenarios. |

### 5.9 `DepthExceededBehavior`

`DepthExceededBehavior` is a sealed class hierarchy in `Polymorph.Abstractions` that controls what happens when the configured maximum object graph depth is reached. It provides four variants covering the common scenarios without requiring consumers to subclass or implement anything.

```csharp
public abstract class DepthExceededBehavior
{
    public static readonly DepthExceededBehavior ReturnNull;
    public static readonly DepthExceededBehavior Throw;
    public static DepthExceededBehavior Invoke(Action<DepthExceededContext> action);
    public static DepthExceededBehavior InvokeAndThrow(Action<DepthExceededContext> action);
}
```

| Variant | Behavior |
|---|---|
| `ReturnNull` | Silently returns null for the member that would have exceeded the limit. The mapping operation completes with truncated data. This is the default. |
| `Throw` | Throws a `MappingExecutionException` immediately. |
| `Invoke(action)` | Executes the action as a side effect, then returns null. Use for logging, metrics, or alerting without interrupting the mapping operation. |
| `InvokeAndThrow(action)` | Executes the action as a side effect, then throws a `MappingExecutionException`. Use when the consumer wants both an alert and a hard failure. |

`ReturnNull` and `Throw` are static readonly singleton instances. `Invoke` and `InvokeAndThrow` are factory methods that return a new instance capturing the provided action delegate.

#### `DepthExceededContext`

The context object passed to the action in `Invoke` and `InvokeAndThrow` variants. Carries all information needed to produce a useful diagnostic.

| Property | Type | Description |
|---|---|---|
| `MapKey` | `MapKey` | The source and destination types of the map at which the depth limit was reached. |
| `CurrentDepth` | `int` | The depth at which mapping was halted. Equal to the configured limit. |
| `IsPerMapLimit` | `bool` | `true` when the limit that was reached was a per-map limit set via `MaxDepth(int)` on the mapping expression. `false` when the global `MaxDepth` was reached. |
| `Logger` | `IPolymorphLogger` | The mapper's configured logger instance. Allows the action to log without requiring a separate logger reference to be closed over. |
| `CancellationToken` | `CancellationToken?` | The cancellation token associated with the current mapping operation, if any. Null when no cancellation token was provided. |

`DepthExceededBehavior` and `DepthExceededContext` live in `Polymorph.Abstractions` and are listed in the §6.2 public types table.

---

## 6. Package Structure

Polymorph is distributed as four NuGet packages. This section defines each package's responsibilities, dependencies, and target frameworks. Package boundaries are a public contract - once a type is published in a given package, moving it to another package is a breaking change. These boundaries must therefore be deliberate and stable.

### 6.1 Governing Principles

#### 6.1.1 Packages Are Contracts

Every package boundary is a promise to consuming developers about what they will and will not need to reference. A package that does too much forces consumers to take on dependencies they don't need. A package that does too little forces consumers to reference multiple packages to accomplish basic tasks. Both are failures of design. Once a type is published in a given package, moving it to another package is a breaking change. Package boundaries must therefore be deliberate and stable from the outset.

#### 6.1.2 Three Tiers

Polymorph's packages fall into three distinct tiers, each with a different purpose and a different audience:

- **Foundation** - `Polymorph.Abstractions` and `Polymorph`. These are the core of the library. Every other Polymorph package depends on one or both of these. They must be lean, stable, and suitable for use in any .NET environment.
- **Integration** - `Polymorph.Extensions.DependencyInjection`. This package adds optional DI container integration. Consumers who do not use `Microsoft.Extensions.DependencyInjection` must not be required to reference it.
- **Compatibility** - `Polymorph.Compatibility.AutoMapper`. This package exists solely to ease migration from AutoMapper. It is not intended as a permanent dependency and carries no new functionality beyond migration shims.

#### 6.1.3 Dependency Graph Integrity

Dependencies must only flow downward through the tiers. Integration and compatibility packages may depend on foundation packages. Foundation packages must not depend on any integration or compatibility package. This ensures the foundation remains suitable for consumption in environments where the integration or compatibility packages are not present or not desired.

#### 6.1.4 Independent Installability

Every package must be independently installable. A consuming application must be able to reference any single Polymorph package without being forced to reference others beyond its declared dependencies. Transitive dependencies are resolved automatically by NuGet and are acceptable, but no package may require a consuming application to manually add a sibling Polymorph package to compile or run correctly.

### 6.2 `Polymorph.Abstractions`

Contains all public contracts, including `IPolymorphLogger` so that non-DI consumers can implement logging without referencing the implementation package. This package also ships the Polymorph analyzer as a `developmentDependency` analyzer asset, ensuring that any project referencing this package receives build-time diagnostics automatically. This package has no runtime dependencies beyond the .NET base class libraries. All types in this package live in the `Polymorph` namespace. Domain and application layer projects should reference this package where the mapping abstraction is consumed but the implementation is not needed. Libraries that integrate with Polymorph - including test infrastructure and third-party extension packages - should also depend on this package rather than `Polymorph` to keep their own dependency footprint minimal.

**Public types:**

| Type | Kind | Description |
|---|---|---|
| `IObjectMapper` | Interface | Primary mapping abstraction. Consumers depend on this interface. |
| `ITypeConverter<TSource, TDestination>` | Interface | Formal contract for custom whole-type conversion logic. |
| `TypeConverterBase<TSource, TDestination>` | Abstract class | Recommended base class for custom type converter implementations. Provides default implementations to absorb future interface additions. |
| `IValueConverter<TSourceMember, TDestinationMember>` | Interface | Formal contract for lightweight per-member value conversion logic. Used with `opt.UseConverter<TConverter>()` in member configuration expressions. |
| `ValueConverterBase<TSourceMember, TDestinationMember>` | Abstract class | Recommended base class for custom value converter implementations. Provides default implementations to absorb future interface additions. |
| `IMemberValueResolver<TSource, TDestination, TMember>` | Interface | Formal contract for custom member-level resolution logic requiring access to both source and destination objects. |
| `MemberValueResolverBase<TSource, TDestination, TMember>` | Abstract class | Recommended base class for custom member value resolver implementations. Provides default implementations to absorb future interface additions. |
| `IMappingExpression<TSource, TDestination>` | Interface | Fluent API for configuring individual maps. Returned by `CreateMap<TSource, TDestination>()`. |
| `NullHandling` | Enum | Controls behavior when the source object is null. Referenced by `IMappingExpression.WithNullHandling()` and `PolymorphOptions.NullHandling`. |
| `IPolymorphLogger` | Interface | Logging abstraction used by Polymorph internally. Mirrors the `ILogger` interface from MEL with a single `Log<TState>` method. Implement this interface to integrate Polymorph logging with any logging library. Convenience extension methods (`LogTrace`, `LogDebug`, etc.) are provided in `Polymorph.Abstractions`. |
| `PolymorphLogLevel` | Enum | Mirrors `Microsoft.Extensions.Logging.LogLevel` exactly, with identical member names and integer values. Used in `IPolymorphLogger.Log<TState>`. |
| `PolymorphEventId` | Struct | Mirrors `Microsoft.Extensions.Logging.EventId` exactly, with `int Id` and `string? Name` properties. Used in `IPolymorphLogger.Log<TState>`. |
| `NullPolymorphLogger` | Class | Discards all log calls. Available as a singleton via `NullPolymorphLogger.Instance`. Used as the internal default when no logger is provided. |
| `UnmatchedMemberInfo` | Record | Exposes `DeclaringType` (`Type`) and `MemberName` (`string`) for a destination member that failed strict mode validation. Surfaced via `MappingConfigurationException.UnmatchedMembers`. |
| `PolymorphException` | Abstract class | Base class for all Polymorph exceptions. |
| `MissingMapException` | Class | Thrown when no map is registered for a type pair. |
| `MappingConfigurationException` | Class | Thrown when map configuration is invalid. |
| `MappingExecutionException` | Class | Thrown when a valid map fails at runtime. |
| `ThreadSafeForMappingAttribute` | Attribute class | Applied to an instance field or auto-property on an extension point implementation to suppress `POLYMORPH011`. Declares that the annotated member is safe for concurrent access across cached mapping invocations. Has no runtime effect. |
| `DepthExceededBehavior` | Abstract class | Sealed class hierarchy controlling behavior when the configured maximum object graph depth is reached. Provides `ReturnNull`, `Throw`, `Invoke`, and `InvokeAndThrow` variants. See §5.9. |
| `DepthExceededContext` | Class | Context object passed to the action in `DepthExceededBehavior.Invoke` and `InvokeAndThrow`. Carries `MapKey`, `CurrentDepth`, `IsPerMapLimit`, `Logger`, and `CancellationToken?`. See §5.9. |

### 6.3 `Polymorph`

Contains the core implementation, including the expression tree-based mapping engine, the Roslyn source generator, and the `MappingProfile` base class. References `Polymorph.Abstractions`, and therefore also includes the Polymorph analyzer transitively. All types in this package live in the `Polymorph` namespace. This package should be referenced at the composition root - the single location in an application where dependencies are wired together. Domain and application layer projects should reference `Polymorph.Abstractions` instead.

**Public types:**

| Type | Kind | Description |
|---|---|---|
| `MappingProfile` | Abstract class | Base class for consumer-defined mapping profiles. |
| `CompilationMode` | Enum | Controls when map compilation occurs relative to `Build()`. Referenced by `PolymorphOptions.CompilationMode`. |
| `MappingConfiguration` | Class | Aggregates profiles, validates maps, and produces an `IObjectMapper` via `Build()`. Provides two constructors: one accepting only a configuration delegate, and one additionally accepting an `IPolymorphLogger` for non-DI logging scenarios. Immutable after `Build()` is called. |
| `PolymorphOptions` | Class | The configuration surface for a Polymorph mapper instance. Consumers interact with it directly in `AddPolymorph` lambdas and `IPostConfigureOptions` implementations. Provides `AddProfile`, `AddMap`, `UseLogger`, and all configuration option properties. See §5.6. |
| `PolymorphOptions<TMapper>` | Class | Inherits from `PolymorphOptions`. Typed registration vehicle for the DI options infrastructure. Allows each mapper type to have its own independently configurable options instance. Consumers working with a single default mapper never reference this type directly. |
| `ObjectMapper` | Class | Concrete implementation of `IObjectMapper`. |

### 6.4 `Polymorph.Extensions.DependencyInjection`

Contains `IServiceCollection` and `IApplicationBuilder` extension methods for registering Polymorph with Microsoft's dependency injection container. References `Polymorph`. Consumers who do not use `Microsoft.Extensions.DependencyInjection` should not reference this package, as it carries a transitive dependency on `Microsoft.Extensions.DependencyInjection.Abstractions`.

This package provides two registration approaches. Profile-based registration is the recommended approach and supports the full feature set including AOT. The `AddMap<TSource, TDestination>()` convenience method is provided for simple cases but falls outside the statically analyzable subset - maps registered this way will not benefit from source generator diagnostics or AOT emission.

When registering via `AddPolymorph`, the `ILoggerFactory` registered in the DI container is automatically resolved and wrapped in an `IPolymorphLogger` implementation that is passed to `MappingConfiguration`. Consumers do not need to pass it explicitly - if `ILoggerFactory` is registered, Polymorph will use it. If it is not registered, Polymorph functions without logging.

#### AddPolymorph

`AddPolymorph` is an extension method on `IServiceCollection`. It accepts a lambda that receives a `PolymorphOptions` instance - the same configuration surface described in §5.6 - and registers `IObjectMapper` as a singleton in the DI container. The lambda is applied as the initial configuration via `services.Configure<PolymorphOptions<ObjectMapper>>(lambda)`. The `IObjectMapper` singleton is registered with a factory delegate; `MappingConfiguration.Build()` is called inside the factory at first resolution time, after the `IServiceProvider` is fully constructed and all options configurations have been applied.

`AddPolymorph` accepts a lambda that receives `PolymorphOptions` directly. `AddProfile` and `AddMap` are methods on this options object. This is the same pattern used by ASP.NET Core for authentication and authorization registration. Consumers who expect `AddMap` to be an extension method on `IServiceCollection` and call it directly will bypass `MappingConfiguration` entirely and produce a runtime error.

`AddPolymorph` uses an internal generic marker type to detect duplicate registrations. If `AddPolymorph` has already been called for a given mapper type, subsequent calls are no-ops - the first registration wins and no exception is thrown. This matches the convention established by ASP.NET Core's own registration methods such as `AddAuthentication` and `AddLogging`, and allows consumers to call `AddPolymorph` defensively or from multiple composition points without risk. The marker is keyed on the mapper type - `AddPolymorph` and `AddPolymorph<TMapper>` use independent markers per type, so registering `OrderMapper` and `ReportingMapper` independently is always permitted. Calling both `AddPolymorph` and `AddPolymorph<ObjectMapper>` is treated as a duplicate since both register the same mapper type, and the second call is a no-op.

The `IObjectMapper` singleton factory resolves `IOptions<PolymorphOptions<ObjectMapper>>` from the `IServiceProvider` at first resolution time. This ensures all `IPostConfigureOptions<PolymorphOptions<ObjectMapper>>` implementations registered in the container have run before `Build()` is called. The resolved `PolymorphOptions` instance is passed to `MappingConfiguration`, which uses it to drive profile registration and compilation. The same pattern applies to `AddPolymorph<TMapper>`, which resolves `IOptions<PolymorphOptions<TMapper>>` instead.

```csharp
// Profile-based registration - recommended, full AOT support
services.AddPolymorph(options =>
{
    options.AddProfile<OrderMappingProfile>();
    options.AddProfile<CustomerMappingProfile>();
});

// Convenience registration - expression tree path only, no AOT support
services.AddPolymorph(options =>
{
    options.AddMap<Order, OrderDto>();
});
```

#### UsePolymorph

`UsePolymorph` is an extension method on `IApplicationBuilder`. It eagerly resolves `IObjectMapper` from the DI container, forcing `MappingConfiguration.Build()` to run immediately. Any `MappingConfigurationException` thrown during map validation will surface at this point, before the application begins serving requests.

Calling `UsePolymorph` is optional. Consumers who do not call it will have `MappingConfiguration.Build()` deferred until the first time `IObjectMapper` is resolved from the container. In that case, map validation errors surface on first resolution rather than at startup. Consumers who require startup-time map validation must call `UsePolymorph` before `app.Run()`.

`UsePolymorph` resolves `IObjectMapper` using `GetRequiredService<IObjectMapper>()`. If `IObjectMapper` has not been registered - for example, because `AddPolymorph` was not called - a standard `InvalidOperationException` is thrown by the DI container, not a Polymorph exception. This is consistent with the standard behavior of `GetRequiredService` throughout ASP.NET Core. Calling `UsePolymorph` more than once is safe - `IObjectMapper` is registered as a singleton, so subsequent resolutions return the already-initialized instance from the DI container. No special guard logic is required and `Build()` is never called more than once.

When `CompilationMode` is `Lazy`, `UsePolymorph` still provides value - configuration errors such as strict mode violations, duplicate registrations, and invalid inheritance references surface at startup during the validation step of `Build()`. However, `LambdaExpression.Compile()` is not called for any map at startup - those costs are deferred to first use as the consumer intended. Compilation errors that are only detectable during `LambdaExpression.Compile()` will therefore still surface on first use rather than at startup. Consumers who require all errors - including compilation errors - to surface at startup must use `CompilationMode.Eager`, which is the default.

```csharp
var app = builder.Build();

app.UsePolymorph(); // optional - forces eager map validation before serving requests

app.Run();
```

#### Assembly Scanning (Compatibility)

`Polymorph.Compatibility.AutoMapper` provides an assembly-scanning overload of `AddPolymorph` as a migration aid for consumers moving from AutoMapper's `services.AddAutoMapper(assembly[])` registration pattern. This overload is not part of `Polymorph.Extensions.DependencyInjection` and must not be added to the core package.

The overload accepts `params Assembly[]` and scans each assembly for concrete, non-abstract types that subclass `MappingProfile`. Each discovered type is registered via `AddProfile<TProfile>()` on the underlying `PolymorphOptions`. The scan uses reflection and is not AOT-safe. The overload must be marked `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]` for the same reasons as `Map<TDestination>(object source)`.

The following boundary conditions apply:

- **Profiles with constructor arguments** - only profiles with a public parameterless constructor can be discovered and instantiated by the scanner. Profiles requiring constructor arguments are silently skipped. Consumers who need those profiles must register them explicitly via `AddProfile(MappingProfile profile)` alongside the scanning call.
- **Duplicate profiles** - if a scanned profile type duplicates a map already registered by an earlier `AddProfile` call or by a prior scan, this is treated as a duplicate map registration and will throw a `MappingConfigurationException` at `Build()` time, consistent with the existing duplicate key behavior.
- **Multiple assemblies** - the `params Assembly[]` signature allows multiple assemblies to be scanned in a single call. Each is scanned independently and all discovered profiles are registered in the order they are encountered.

The XML documentation on this overload must direct consumers toward explicit `AddProfile<TProfile>()` registration as the permanent solution and describe assembly scanning as a temporary migration aid. See §A.3 for the full compatibility package description.

```csharp
// Compatibility package only - not AOT-safe, migration aid only
services.AddPolymorph(typeof(MyProfile).Assembly, typeof(OtherProfile).Assembly);
```



#### Multiple Mapper Instances

Multiple independent mapper instances may be registered in the same DI container by subclassing `ObjectMapper` and registering each subclass as its own type. Consuming code injects the specific subclass rather than `IObjectMapper`. This pattern is useful for applications with distinct mapping domains that require hard isolation between their mapping configurations.

`Polymorph.Extensions.DependencyInjection` must provide a generic overload of `AddPolymorph<TMapper>` that registers a specific `ObjectMapper` subclass rather than the default `ObjectMapper`. This allows multiple independently configured mapper instances to coexist in the same DI container without conflict.

```csharp
public class OrderMapper : ObjectMapper { }
public class ReportingMapper : ObjectMapper { }

services.AddPolymorph<OrderMapper>(options =>
{
    options.AddProfile<OrderMappingProfile>();
});

services.AddPolymorph<ReportingMapper>(options =>
{
    options.AddProfile<ReportingMappingProfile>();
});
```

Consuming code then injects the specific subclass directly. Note that this is a deliberate exception to the interfaces-first principle in §5.1.1 - the subclass type serves as the discriminator, and injecting `IObjectMapper` would not allow the DI container to distinguish between the two registered mappers. Consumers who want to preserve the interfaces-first pattern may wrap each subclass behind their own named interface.

```csharp
public class OrderService
{
    private readonly OrderMapper _mapper;

    public OrderService(OrderMapper mapper)
    {
        _mapper = mapper;
    }
}
```

### 6.5 `Polymorph.Compatibility.AutoMapper`

`Polymorph.Compatibility.AutoMapper` is a transitional package for consumers migrating from AutoMapper. It depends on `Polymorph` and `Polymorph.Extensions.DependencyInjection` and provides no runtime value beyond those packages once migration is complete.

| Attribute | Value |
|---|---|
| **Package ID** | `Polymorph.Compatibility.AutoMapper` |
| **Dependencies** | `Polymorph`, `Polymorph.Extensions.DependencyInjection` |
| **AOT safe** | No. Assembly scanning overload is marked `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]`. |
| **Development dependency** | No, in NuGet's technical sense - the package is not marked `<developmentDependency>true</developmentDependency>` and ships as a normal runtime reference. Conceptually it is transitional; see §6.1.2 for the expectation that consumers remove this reference once migration is complete. |

The full behavioral specification for this package is in Appendix A.3.

### 6.6 Package Metadata Requirements

All Polymorph packages must include the following NuGet package metadata:

- **Package ID** - follows the `Polymorph.*` naming convention. The `Polymorph` prefix must be reserved on NuGet.org to prevent third-party packages from using the same prefix and causing consumer confusion.
- **Description** - a clear, concise description of the package's purpose and its relationship to other Polymorph packages.
- **License** - dual MIT / Apache 2.0, expressed as `MIT OR Apache-2.0` in the license expression field, consistent with §4.1.
- **Repository URL** - a link to the public source repository.
- **Documentation URL** - a link to the hosted documentation site.
- **README** - a package-level README describing the package's purpose, its place in the Polymorph ecosystem, and a link to getting-started documentation. The `readme` property in the project file must point to this file so it renders correctly on NuGet.org.
- **Tags** - at minimum: `mapping`, `object-mapping`, `automapper`, and `polymorph`. The `automapper` tag is deliberate - it aids discoverability for developers searching for AutoMapper alternatives.
- **Version** - follows Semantic Versioning 2.0.0 [R10], consistent with §4.3.
- **Symbol package** - a `.snupkg` symbol package must be published alongside each release to enable source-level debugging in consuming applications.
- **SourceLink** - all packages must be built with SourceLink enabled, embedding source control metadata in the symbol package. This allows consuming developers to step through Polymorph source code in their debugger without cloning the repository. SourceLink must be configured to point to the public source repository.

---

## References

The following sources substantiate specific claims made in this document.

### AutoMapper Licensing

- **[R1]** AutoMapper Commercial Edition Launch - Jimmy Bogard's announcement of the commercial transition, detailing the dual-license model, tier structure, license key requirement, and log warning behavior for unlicensed usage.
  https://www.jimmybogard.com/automapper-and-mediatr-commercial-editions-launch-today/

- **[R2]** AutoMapper Licensing and Pricing - The official AutoMapper pricing page, documenting community tier eligibility criteria and commercial tier requirements.
  https://automapper.io

- **[R3]** AutoMapper License Configuration Documentation - The official documentation describing how to configure a license key in both DI and non-DI scenarios.
  https://docs.automapper.io/en/stable/License-configuration.html

### AutoMapper Security Vulnerability

- **[R4]** GHSA-rvv3-g6hj-g44x - GitHub Security Advisory documenting the Denial of Service vulnerability in AutoMapper via uncontrolled recursion. Affects all versions prior to 15.1.1 and 16.1.1. Describes the attack vector, severity score (7.5 / High), and recommended remediation.
  https://github.com/advisories/GHSA-rvv3-g6hj-g44x

- **[R5]** CVE-2026-32933 - The AutoMapper maintainer's own security advisory, confirming the vulnerability in the core mapping engine and providing the official fix guidance.
  https://github.com/LuckyPennySoftware/AutoMapper/security/advisories/GHSA-rvv3-g6hj-g44x

- **[R6]** LuckyPennySoftware/AutoMapper Issue #4618 - The maintainer's confirmation that no security patch will be released for version 14.x or earlier. The fix is available only in the commercial versions.
  https://github.com/LuckyPennySoftware/AutoMapper/issues/4618

### .NET Platform

- **[R7]** .NET Native AOT Documentation - Microsoft's official documentation on Native AOT deployment, covering platform requirements, limitations, and trimming behavior.
  https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/

- **[R8]** Roslyn Source Generators - Microsoft's official overview of the Roslyn source generator API.
  https://learn.microsoft.com/en-us/dotnet/csharp/roslyn-sdk/source-generators-overview

- **[R9]** Supporting Multiple SDK Versions in Source Generators - Andrew Lock's detailed guide on multi-targeting source generators across Roslyn API versions, including the relationship between Roslyn API versions and .NET SDK versions.
  https://andrewlock.net/supporting-multiple-sdk-versions-in-analyzers-and-source-generators/

### Standards and Licenses

- **[R10]** Semantic Versioning 2.0.0 - The SemVer specification referenced by Polymorph's versioning policy.
  https://semver.org

- **[R11]** MIT License
  https://opensource.org/licenses/MIT

- **[R12]** Apache License 2.0
  https://www.apache.org/licenses/LICENSE-2.0

---

## Appendix A: Compatibility and Migration

### A.1 AutoMapper Compatibility

Polymorph is not a fork of AutoMapper and does not guarantee API compatibility with it. However, it is designed to cover the same core use cases and to feel familiar to developers with AutoMapper experience. The following table maps AutoMapper concepts to their Polymorph equivalents.

| AutoMapper | Polymorph | Notes |
|---|---|---|
| `IMapper` | `IObjectMapper` | Renamed to avoid IP conflict. |
| `Profile` | `MappingProfile` | Renamed for clarity. Shipped in `Polymorph`, not `Polymorph.Abstractions`. |
| `ITypeConverter<TSrc, TDest>` | `ITypeConverter<TSource, TDestination>` | Equivalent. |
| `IMemberValueResolver` | `IMemberValueResolver<TSource, TDestination, TMember>` | Equivalent. |
| `CreateMap<TSrc, TDest>()` | `CreateMap<TSource, TDestination>()` | Identical. |
| `ReverseMap()` | `ReverseMap()` | Identical. |
| `.AfterMap((src, dest) => ...)` | `.AfterMap((src, dest) => ...)` | Identical. Multiple calls permitted and invoked in registration order. `MappingContext` is not passed to the action, consistent with AutoMapper's behavior. |
| `.BeforeMap((src, dest) => ...)` | `.BeforeMap((src, dest) => ...)` | Identical. Fires after destination construction, before member mapping. Multiple calls permitted and invoked in registration order. `MappingContext` is not passed to the action. |
| `ForMember()` | `ForMember()` | Identical. In Polymorph, `ForMember` also works for constructor parameters on record types - no `ForCtorParam` is needed. |
| `.ForCtorParam("paramName", opt => ...)` | `.ForMember(dest => dest.Property, opt => ...)` | In Polymorph, use `ForMember` instead. `ForCtorParam` is available in `Polymorph.Compatibility.AutoMapper` as a deprecated shim that maps to the equivalent `ForMember` call, emitting a compiler warning directing consumers to migrate. |
| `.ConvertUsing<TConverter>()` | `.UseConverter<TConverter>()` | In Polymorph, use `UseConverter<TConverter>()` directly on the mapping expression for whole-type conversion. `ConvertUsing<TConverter>()` is available in `Polymorph.Compatibility.AutoMapper` as a deprecated shim. |
| `opt.MapFrom<TConverter>()` | `opt.UseConverter<TConverter>()` | In Polymorph, use `UseConverter<TConverter>()` inside a `ForMember` options expression for per-member value conversion. `MapFrom<TConverter>()` is available in `Polymorph.Compatibility.AutoMapper` as a deprecated shim. |
| `Ignore()` | `Ignore()` | Identical. |
| `.Include<TDerivedSrc, TDerivedDest>()` | `.Include<TDerivedSource, TDerivedDestination>()` | Equivalent. |
| `.IncludeBase<TBaseSrc, TBaseDest>()` | `.IncludeBase<TBaseSource, TBaseDestination>()` | Equivalent. |
| `MapperConfiguration` | `MappingConfiguration` | Renamed. |
| `MapperConfiguration(cfg, loggerFactory)` | `MappingConfiguration(options, logger)` | `ILoggerFactory` is replaced by `IPolymorphLogger`, Polymorph's own logging abstraction with no MEL dependency. `Polymorph.Extensions.DependencyInjection` wires `ILoggerFactory` to `IPolymorphLogger` automatically. `Polymorph.Compatibility.AutoMapper` provides a convenience shim for consumers migrating from AutoMapper 15.0. In Polymorph, the logger is always optional. |
| `MapperConfiguration.CreateMapper()` | `MappingConfiguration.Build()` | Renamed. `CreateMapper()` is available as an alias in `Polymorph.Compatibility.AutoMapper`. |
| `AssertConfigurationIsValid()` | Automatic during initialization | Validation is automatic by default in Polymorph. |
| Static `Mapper.Map<T>()` | Not provided | Consumers may build their own static facade over `IObjectMapper`. |

### A.2 Migration Guidance

Consumers migrating from AutoMapper should follow these steps:

1. Install `Polymorph` and `Polymorph.Abstractions`.
2. Replace references to `IMapper` with `IObjectMapper`.
3. Change `Profile` subclasses to subclass `MappingProfile` instead.
4. `CreateMap<TSource, TDestination>()` calls require no changes.
5. Replace `MapperConfiguration` with `MappingConfiguration`, and replace `.CreateMapper()` with `.Build()`. If using `Polymorph.Compatibility.AutoMapper`, `.CreateMapper()` is available as an alias and requires no change.
6. If migrating from AutoMapper 15.0 or later, replace the required `ILoggerFactory` parameter with an `IPolymorphLogger` implementation. `Polymorph.Compatibility.AutoMapper` provides a convenience shim that wraps `ILoggerFactory` behind `IPolymorphLogger`. When using `Polymorph.Extensions.DependencyInjection`, this wiring is automatic.
7. Remove any calls to `AssertConfigurationIsValid()` - validation is automatic.
8. Install `Polymorph.Extensions.DependencyInjection` if using Microsoft DI, and replace `services.AddAutoMapper()` with `services.AddPolymorph()`. The `ILoggerFactory` is resolved automatically from the DI container when using this package.

### A.3 Compatibility Package

The `Polymorph.Compatibility.AutoMapper` package provides additional shims for consumers with large existing AutoMapper configurations. It includes `CreateMapper()` as an alias for `MappingConfiguration.Build()`, disables strict mode by default to match AutoMapper's permissive behavior, and provides an `ILoggerFactory`-backed implementation of `IPolymorphLogger` for consumers migrating from AutoMapper 15.0 where `ILoggerFactory` was a required constructor parameter. It also provides the following deprecated shims on `IMappingExpression`, all marked `[Obsolete]` and emitting compiler warnings directing consumers to migrate: `ForCtorParam` mapping to `ForMember`; `ConvertUsing<TConverter>()` mapping to `UseConverter<TConverter>()` for whole-type conversion; `MapFrom<TConverter>()` mapping to `UseConverter<TConverter>()` for per-member value conversion; and `IgnoreAllPropertiesWithAnException` mapping to `IgnoreAllExcept`. It also provides an assembly-scanning overload of `AddPolymorph` that accepts `params Assembly[]` and registers all discoverable `MappingProfile` subclasses - this overload is not AOT-safe and is intended as a migration aid only; see §6.4 for the full behavioral contract. It is intended as a transitional dependency and should not be treated as a permanent dependency. It is a v1 deliverable.

Consumers are encouraged to enable strict mode explicitly once their migration is complete, to take full advantage of Polymorph's default safety guarantees.

---

## Appendix B: Technical Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Source generator API changes between Roslyn versions cause behavioral differences across SDK versions | Medium | High | Pin to a minimum Roslyn API version. Document the minimum required SDK version. Use multi-targeting for the generator binary where feasible. |
| Expression tree compilation behavior differs across .NET versions, producing subtle mapping inconsistencies | Low | Medium | Maintain a comprehensive integration test suite that runs across all supported target frameworks in CI. |
| Changes to generated source generator output produce unexpected diffs for consumers who commit generated files to source control | Medium | Low | Document that generated output may change between minor versions. Note all such changes explicitly in release notes. |
| Strong naming creates transitive dependency conflicts when consumers mix strong-named and non-strong-named assemblies | Low | High | Document the strong-naming requirement and its implications clearly. Ensure all Polymorph packages and their dependencies are strong-named. |
| Future .NET runtime versions further restrict expression tree compilation, breaking the non-AOT engine | Low | High | Monitor .NET roadmap. The source generator path is designed to be the long-term primary engine; the expression tree path is a compatibility bridge. |
| The mapping context object must be propagated through every internal mapping call, including through type converter invocations. Failure to propagate it correctly in any code path - including generated code on the source generator path - will silently break circular reference detection and depth tracking. | Medium | High | Enforce context propagation through automated tests that specifically exercise circular references and deep graphs across both the expression tree and source generator paths. |
| BenchmarkDotNet results vary across machines and environments, making it difficult to enforce regression thresholds reliably in CI without false positives. | Medium | Low | Run benchmarks on a dedicated, consistent benchmark machine. Use relative comparisons against a committed baseline rather than absolute thresholds. Document the benchmark environment in the repository. |
| Sole maintainer availability affects release cadence and issue response times | Medium | Medium | Maintain clear contribution guidelines. Keep the codebase and architecture simple enough that external contributors can participate without deep onboarding. |

---

## Appendix C: Competitive Landscape

### C.1 AutoMapper

AutoMapper is the library Polymorph most directly replaces. It is mature, well-documented, and widely understood. Its profile-based configuration model is the direct inspiration for Polymorph's own design.

Beginning with version 15.0, AutoMapper is licensed under a commercial dual-license model (RPL-1.5 and a commercial license). Previous versions remain available under their original open source licenses but will not receive updates. For organizations that cannot accept a commercial dependency or require ongoing updates, AutoMapper is no longer a viable option.

AutoMapper does not provide Native AOT support and has no stated plans to do so.

### C.2 Mapster

Mapster is an actively maintained open source library licensed under the MIT License. It is fast, has a large user base, and supports both convention-based and explicit mapping. It provides IQueryable projection support and a DI integration package.

Mapster's default mode is reflection-based and is not AOT-safe. A separate Mapster.Tool provides code generation, but it is not a Roslyn source generator and does not integrate with the build pipeline in the same way. Mapster does not provide strong-named packages and does not make formal SemVer compatibility commitments.

### C.3 Mapperly

Mapperly is an open source Roslyn source generator licensed under the Apache License 2.0. It generates mapping code at compile time, is fully AOT-safe, and produces readable, debuggable output. It is the closest technical equivalent to Polymorph's source generator path.

Mapperly's API surface is meaningfully different from AutoMapper's. Mappers are defined as partial classes decorated with `[Mapper]` rather than as profile subclasses. Mapperly does not implement a standard `IMapper`-style interface and does not integrate with DI containers by default, requiring consumers to write their own wrapper. Mapperly does not provide an abstractions package, strong-named packages, or formal SemVer compatibility commitments.

### C.4 Manual Mapping

Writing mapping code by hand eliminates all external dependencies and produces maximally transparent, debuggable code. It is a legitimate choice for small projects or teams with strong opinions about external dependencies.

At scale, manual mapping is expensive to maintain. As types evolve, mapping code must be updated manually, and the risk of silent mapping omissions increases. For organizations managing many type pairs across many projects, a dedicated mapping library provides meaningful value.

### C.5 Differentiation Summary

| Capability | AutoMapper | Mapster | Mapperly | Polymorph |
|---|---|---|---|---|
| Open source | ⚠️ v14 and earlier only | ✅ MIT | ✅ Apache 2.0 | ✅ MIT + Apache 2.0 |
| Native AOT support | ❌ | ❌ default mode | ✅ | ✅ |
| Profile-based configuration | ✅ | ⚠️ different model | ❌ | ✅ |
| Standard `IMapper` interface | ✅ | ✅ | ❌ | ✅ `IObjectMapper` |
| First-class DI integration | ✅ | ✅ | ⚠️ manual wrapper | ✅ separate package |
| Abstractions package | ❌ | ❌ | ❌ | ✅ |
| Strong-named packages | ✅ | ❌ | ❌ | ✅ |
| Formal SemVer commitments | ❌ | ❌ | ❌ | ✅ |

---

## Appendix D: Framework and Engine Matrix

| Target Framework | Default Engine | Optional Engine | Native AOT Safe | Notes |
|---|---|---|---|---|
| .NET Standard 2.0 | Expression trees | - | ❌ | AOT is a runtime feature unavailable on .NET Standard. |
| .NET Standard 2.1 | Expression trees | - | ❌ | AOT is a runtime feature unavailable on .NET Standard. |
| .NET 6.0 - .NET 10.0 | Expression trees | Source generator | ❌ | Source generator available via `UseSourceGenerator="true"` in the `<Polymorph>` MSBuild item. |
| .NET 6.0 - .NET 10.0 (AOT) | Source generator | - | ✅ | Source generator is mandatory. Expression trees are not permitted. |

The source generator requires the .NET 6 SDK or later. Consumers targeting .NET Standard 2.0 or 2.1 may use Polymorph with the expression tree engine but cannot use Native AOT regardless of the SDK version used to build. On non-AOT .NET 6+ targets, consumers may opt into the source generator path by setting `UseSourceGenerator="true"` in the `<Polymorph>` MSBuild item to gain compile-time mapping validation and readable generated code.

The untyped `Map<TDestination>(object source)` overload is not AOT-safe on any target framework. It is marked with `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]` and must not be used in Native AOT applications. It is not available on .NET Standard 2.0.