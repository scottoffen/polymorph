# Implementation Roadmap
## C# Class Library: Polymorph

## Item Numbering

Numbered items within each phase are stable identifiers, not an execution order. Items within a single phase have no implied ordering relative to each other - implementers may sequence them as they prefer. The numbers exist so that items can be referenced unambiguously in commit messages, pull requests, and external discussion.

## Definition of Done

A phase is complete when all of the following hold for every item in the phase:

1. All **Acceptance** criteria listed for items in the phase are met.
2. `dotnet build` succeeds with zero warnings (`TreatWarningsAsErrors` is enabled globally, so this is enforced by the build).
3. The new public API surface, if any, matches what `REQUIREMENTS.md` specifies for that item.
4. No drift has been introduced between the code and `DESIGN.md`. If drift is found, surface it to the user. Do not modify `DESIGN.md` unilaterally.

## Execution Constraints

When the work is being performed by an agentic AI:

- Work on one item at a time.
- Complete that item - including all required tests, passing build, and the Definition of Done checks - before picking up another item.
- Items within a phase may be done in any order, and items in parallel phases (per §Parallelism) may be selected freely, but only one is in flight at a time.

## Acceptance and Tests

Each item below includes an **Acceptance:** line stating the criteria for considering the item complete. Most items also include a **Tests:** line listing the test scenarios that must be covered; for those items the Acceptance line points at the Tests line. Where an item has acceptance criteria that are not captured by tests, those criteria appear directly on the Acceptance line, optionally alongside a Tests reference. Where an item has no tests (Phase 12), the Acceptance line stands alone.

Tests lists are the minimum coverage required, not an exhaustive enumeration. Implementers should add tests beyond the listed scenarios where judgment indicates additional coverage is warranted - edge cases discovered during implementation, regression tests for bugs found in review, scenarios specific to a target framework, and so on.

---

## Phase 1 - Value Types and Data Carriers

No behavioral dependencies. Fully testable as pure data.

### 1. `MapKey`
Value type identifying a map by source and destination `Type`. Implements `Equals` and `GetHashCode` via reference equality delegation to `Type`.

**Acceptance:** all Tests below pass.

**Tests:** equality, hash collisions, `MapKey(A, B)` and `MapKey(B, A)` are distinct, `GetHashCode` stable across repeated calls.

### 2. `MappingOptions`
Immutable value type carrying the snapshotted configuration values: `CompilationMode`, `ValidateMaps`, `EnforceStrictMode`, `NullHandling`, `MaxDepth`, `DepthExceededBehavior`, and `ValueTransformers`.

**Acceptance:** all Tests below pass.

**Tests:** all configuration values assigned from constructor, no public mutators.

### 3. `DepthExceededBehavior` and `DepthExceededContext`
Sealed class hierarchy controlling behavior when the configured maximum object graph depth is reached. Four variants: `ReturnNull` and `Throw` are static readonly singletons; `Invoke(action)` and `InvokeAndThrow(action)` are factory methods. `DepthExceededContext` carries `MapKey`, `CurrentDepth`, `IsPerMapLimit`, `Logger`, and `CancellationToken?`. Both types live in `Polymorph.Abstractions`.

**Acceptance:** all Tests below pass.

**Tests:** each variant constructs correctly, `ReturnNull` and `Throw` are stable singleton references across calls, factory methods produce distinct instances when called repeatedly, factory methods capture action, `DepthExceededContext` field access including nullable `CancellationToken`.

### 4. `MemberDescriptor`
Data carrier representing the resolved configuration for a single destination member. Supports all eight kinds: `Convention`, `ConstructorParameter`, `Expression`, `ValueConverter`, `Resolver`, `Ignore`, `NestedMap`, and `MutableCollectionPopulation`. Carries optional condition (`Func<object, bool>?`) and null substitute (`object?`). Member resolution is by name via reflection at compile time - not by cached `PropertyInfo` - to preserve `ForPath` extensibility.

**Acceptance:** all Tests below pass.

**Tests:** construction per kind, field access, condition and null substitute fields, optional field independence.

### 5. `MapDescriptor`
Mutable builder accumulating all configuration for a single type pair during profile construction. Includes `IsSealed` gate, `UseConverter` conflict detection, `IgnoreAllExceptMembers` set, `PerMapMaxDepth`, and `PerMapDepthExceededBehavior`.

**Acceptance:** all Tests below pass.

**Tests:** field accumulation, sealing behavior, duplicate `ForMember` last-write-wins, `UseConverter` conflict throws `MappingConfigurationException` (POLYMORPH208), `IgnoreAllExceptMembers` set populated correctly, per-map depth and behavior fields.

### 6. Exception Types
`PolymorphException`, `MissingMapException`, `MappingConfigurationException`, and `MappingExecutionException` with all structured properties defined in requirements §5.8. `MappingExecutionException.CurrentDepth` populated only when thrown by `DepthExceededBehavior.Throw` or `InvokeAndThrow`. Message templates per design Appendix A.

**Acceptance:** all Tests below pass.

**Tests:** construction, property access (including `MapKey` and `Depth` on `MappingExecutionException` per requirements §5.8), inheritance chain, `CurrentDepth` null for non-depth-exceeded scenarios, message template substitution per design Appendix A.

---

## Phase 2 - Core Infrastructure

Depends on Phase 1.

### 7. `MappingContext`
Per-operation context enforcing circular reference detection and depth tracking. Maintains `ActiveInstances` (`HashSet<object>`), `ActiveCeiling` (`int`), `CeilingStack` (`Stack<int>`), `ActiveBehavior` (`DepthExceededBehavior`), and `BehaviorStack` (`Stack<DepthExceededBehavior>`). `Enter` performs circular reference check first, then depth check; returns `false` when depth limit is reached and `ActiveBehavior` is `ReturnNull` or `Invoke`; throws when `ActiveBehavior` is `Throw` or `InvokeAndThrow`. Per-map ceilings and behaviors are pushed on entry and popped on `Exit`. `Exit` always called in a `finally` block. Value types excluded from tracking.

**Acceptance:** all Tests below pass.

**Tests:** enter/exit lifecycle, circular reference detection throws, depth limit with each `DepthExceededBehavior` variant (`ReturnNull` returns false, `Throw` throws, `Invoke` fires action then returns false, `InvokeAndThrow` fires action then throws), per-map ceiling pushed and restored correctly, per-map behavior pushed and restored correctly, value type pass-through, cleanup on throw, all three collections empty after top-level call returns.

### 8. `MapRegistry`
Eager mode uses an immutable `Dictionary` wrapped in a read-only view after population. Lazy mode uses `ConcurrentDictionary` with idempotent compilation factory.

**Acceptance:** all Tests below pass.

**Tests:** registration, lookup, missing key throws `MissingMapException`, lazy mode compilation factory invoked exactly once per key under concurrent contention.

### 9. `MappingExtensionCache`
`ConcurrentDictionary<Type, object>` caching one converter or resolver instance per extension point type. `GetOrAdd` guarantees at-most-one construction under concurrent access.

**Acceptance:** all Tests below pass.

**Tests:** first-use construction, cached retrieval, `GetOrAdd` factory invoked exactly once per type under concurrent contention.

---

## Phase 3 - Reflection Utilities

Depends on Phase 1. No engine logic - pure property and type discovery.

### 10. `Polymorph.Reflection` Utilities
Public instance property discovery including inherited properties, type assignability checks, collection type inspection (mutable vs read-only interface, element type extraction), and constructor discovery. Member resolution by name returns `PropertyInfo` at compile time but is not cached on descriptors.

**Acceptance:** all Tests below pass.

**Tests:** name matching, type compatibility, static exclusion, getter-only categorization (scalar vs mutable collection vs read-only collection), `init`-only property categorization, single-constructor detection, inherited property visibility.

---

## Phase 4 - Convention Resolution and Validation

Depends on Phases 1-3.

### 11. `ConventionResolver`
Applies convention matching rules from requirements §3.3 in fixed order. As the first step of convention matching, applies `IgnoreAllExceptMembers` if set - adding `Ignore` descriptors to all non-excepted members with no existing descriptor before convention matching runs for the remaining members. Produces `Convention` and `ConstructorParameter` descriptors. Caches reflection results per type.

**Acceptance:** all Tests below pass.

**Tests:** name match, type assignability, static exclusion, getter-only exclusion, single-constructor parameter matching, inherited properties, unmatched members recorded correctly, `IgnoreAllExcept` adds `Ignore` descriptors before convention matching, `IgnoreAllExcept` does not overwrite existing `ForMember` descriptors.

### 12. `MapValidator`
Enforces strict mode, resolves `Include`/`IncludeBase` inheritance via depth-first merge at correct priority, detects duplicate map keys and missing referenced maps.

**Acceptance:** all Tests below pass.

**Tests:** unmatched members throw with full enumeration (no short-circuit), `ValidateMaps=false` skips enforcement, inheritance merge priority (derived explicit > inherited explicit > convention), missing base map (POLYMORPH206), missing derived map (POLYMORPH207), multi-level inheritance chains, diamond inheritance (one map included by two distinct maps), inheritance cycle detection (A includes B which includes A).

---

## Phase 5 - Expression Tree Compiler

Depends on Phases 1-4.

### 13. `ExpressionMapCompiler`
Compiles a validated `MapDescriptor` into a `Func<TSource, MappingContext, TDestination>` delegate. Handles all three construction paths, all eight descriptor kinds, null handling baked in at compile time, `if (!context.Enter(source)) return null` wrapping with per-map ceiling and behavior push/pop, `NestedMap` promotion, `MutableCollectionPopulation` `Add()`-based emission, and `BeforeMap`/`AfterMap` hook invocation via type-erased delegates. Member-level evaluation order: condition first, null substitute second, value transformer third. Value transformers checked against `MappingOptions.ValueTransformers` by exact type match with nullability stripping; suppressed by `UseConverter`, `UseResolver`, and `Ignore`.

**Acceptance:** all Tests below pass.

**Tests:** each construction path (parameterless, single-constructor, `ConstructUsing`), each descriptor kind, null source behavior per `NullHandling` setting, nested map recursive dispatch, collection population with null-instance guard, hook ordering, per-map `NullHandling` override vs global default, `opt.Condition` suppresses assignment when false, `opt.NullSubstitute` applied when source member null, value transformer applied after null substitute, transformer suppressed by `UseConverter`/`UseResolver`/`Ignore`, `IgnoreAllExcept` members mapped normally while excluded members are ignored, per-map `MaxDepth` ceiling pushed and restored, `DepthExceededBehavior.ReturnNull` produces null member in output, `DepthExceededBehavior.Throw` propagates exception.

---

## Phase 6 - MappingProfile and MappingConfiguration

Depends on Phases 1-5.

### 14. `MappingProfile`
Exposes `CreateMap<TSource, TDestination>()` to subclasses. Accumulates `MapDescriptor` instances. Seals on registration with `MappingConfiguration`.

**Acceptance:** all Tests below pass.

**Tests:** map accumulation, post-registration `CreateMap` throws, `ReverseMap` allocates linked reverse descriptor, duplicate `MapKey` within a single profile detected at registration time.

### 15. `IMappingExpression` Implementation
All fluent members from requirements §5.7.1. `UseConverter` conflict check fires at call time. `IgnoreAllExcept` populates `IgnoreAllExceptMembers` on the descriptor. `opt.Condition` stores type-erased predicate. `opt.NullSubstitute(TMember value)` stores substitute after type erasure, typed at call site for compile-time safety. `MaxDepth(int)` sets `PerMapMaxDepth`. `WithDepthExceededBehavior` sets `PerMapDepthExceededBehavior`.

**Acceptance:** all Tests below pass.

**Tests:** each fluent method mutates the descriptor correctly, `UseConverter` after `ForMember` throws immediately, `Ignore` satisfies strict mode, `IgnoreAllExcept` sets correct member set, `opt.NullSubstitute` type safety enforced at call site, `MaxDepth` and `WithDepthExceededBehavior` set correct descriptor fields.

### 16. `PolymorphOptions`
Registration methods (`AddProfile<T>`, `AddProfile(instance)`, `AddMap`, `AddValueTransformer`, `OnDepthExceeded`) and five configuration properties. All methods return `PolymorphOptions` for fluent chaining. Duplicate key detection fires immediately on registration, before `Build()`.

**Acceptance:** all Tests below pass.

**Tests:** `AddProfile<T>` constructs and seals, pre-constructed instance accepted and sealed, `AddMap` produces convention-only descriptor, duplicate key throws immediately, `AddValueTransformer` last-write-wins for duplicate type pairs, `OnDepthExceeded` sets global behavior, fluent chaining.

### 17. `MappingConfiguration`
Constructor invokes delegate immediately, snapshots all configuration property values defined on `MappingOptions`, accumulates descriptor list. `Build()` runs validation, compiles maps per `CompilationMode`, constructs `MappingOptions` from snapshotted values, constructs and returns `ObjectMapper`. Immutable after `Build()`.

**Acceptance:** all Tests below pass.

**Tests:** full pipeline integration, lazy vs eager compilation mode, `_built` gate on all mutating methods, logger resolution priority (constructor parameter takes precedence over `UseLogger()`), post-`Build()` mutation throws, all configuration values defined on `MappingOptions` are snapshotted from `PolymorphOptions` at `Build()` time and post-`Build()` mutation of `PolymorphOptions` does not affect the built mapper, eager mode surfaces `MappingConfigurationException` at `Build()`, lazy mode surfaces `MappingConfigurationException` on first invocation of an invalid map.

---

## Phase 7 - ObjectMapper and Public Dispatch

Depends on Phase 6.

### 18. `ObjectMapper`
Concrete implementation of `IObjectMapper`. All four overloads with correct `MappingContext` lifecycle per design §4.2. Collection dispatch with per-element fresh context via `yield return`. `MapAsync` as async iterator method directly on `ObjectMapper`. `MappingExtensionCache` integrated for extension point invocations. Not sealed; constructor accessible to derived classes.

**Acceptance:** all Tests below pass.

**Tests:** typed dispatch, untyped dispatch (runtime type resolution), collection lazy evaluation, async streaming, `MapAsync` cancellation token observed mid-stream, missing map throws `MissingMapException`, depth exceeded with each `DepthExceededBehavior` variant, circular reference throws `MappingExecutionException`, concurrent safety, subclassing for multi-mapper pattern.

---

## Phase 8 - DI Integration

Depends on Phase 7. Self-contained package; no changes to core.

### 19. `Polymorph.Extensions.DependencyInjection`
`AddPolymorph` and `AddPolymorph<TMapper>` extension methods on `IServiceCollection`. `UsePolymorph` on `IApplicationBuilder`. Duplicate `AddPolymorph` registrations are no-ops per the marker-type pattern. `ILoggerFactory` resolved automatically and bridged to `IPolymorphLogger`. Multi-mapper pattern via `ObjectMapper` subclasses.

**Acceptance:** all Tests below pass.

**Tests:** singleton registration, `Build()` deferred to first resolution, `UsePolymorph` forces eager resolution and surfaces `MappingConfigurationException` at startup, duplicate `AddPolymorph` is no-op, multi-mapper instances are independent, `ILoggerFactory` wired automatically when registered.

---

## Phase 9 - Source Generator

Depends on Phase 6. Can be developed in parallel with Phases 7 and 8.

### 20. `Polymorph.Generator`
Implements `IIncrementalGenerator`. Syntax provider identifies `MappingProfile` subclasses; options provider reads `<Polymorph>` MSBuild item attributes. Analyzes constructor bodies for the statically analyzable subset. Emits typed static map methods with `if (!context.Enter(source)) return null` wrapping, collision-resolved names, module initializer registration, `MappingContext` propagation. Emits `opt.Condition` as `if` guards, `opt.NullSubstitute` as null-coalescing expressions, value transformers inline at applicable member sites. `ReverseMap` and `Include`/`IncludeBase` resolved at analysis time. `GeneratedMapRegistry.Load()` is a no-op when no maps are registered.

**Acceptance:** all Tests below pass.

**Tests:** emitted code compiles and produces correct output, all descriptor kinds within the analyzable subset emitted correctly, `ReverseMap` and inheritance emit correctly, name collision resolution, `Load()` no-op when empty, context propagation verified against circular reference and depth limit scenarios, `DepthExceededBehavior` variants produce correct emitted behavior, condition and null substitute emitted correctly, value transformer emitted inline, the generator emits code that registers itself such that consumer startup paths remain identical whether or not the generator is active.

---

## Phase 10 - Analyzer

Depends only on Roslyn APIs and `Polymorph.Abstractions` public contracts. Can be developed in parallel with Phases 7, 8, and 9.

### 21. `Polymorph.Analyzer`
Implements `DiagnosticAnalyzer`. Five diagnostics at v1, each with static readonly descriptor fields. POLYMORPH000 severity configurable via `DiagnosticLevel` MSBuild attribute. POLYMORPH020 suppressible per-map via `IgnoreStaticWarnings()`. POLYMORPH011 suppressible per-member via `[ThreadSafeForMapping]`.

| Diagnostic | Trigger |
|---|---|
| `POLYMORPH000` | Unmatched destination property in statically analyzable map with `StrictMode=true` |
| `POLYMORPH010` | Direct implementation of `ITypeConverter`, `IValueConverter`, or `IMemberValueResolver` without subclassing the abstract base |
| `POLYMORPH011` | Non-readonly instance field or auto-property on an extension point implementation without `[ThreadSafeForMapping]` |
| `POLYMORPH020` | Static property referenced in a `ForMember` expression |
| `POLYMORPH021` | `UseConverter<TConverter>()` called after `ForMember`, `ConstructUsing`, `BeforeMap`, or `AfterMap` on the same mapping expression |

**Acceptance:** all Tests below pass.

**Tests:** each diagnostic fires on the correct trigger, does not fire when correctly suppressed, diagnostic location is correct (declaration site for POLYMORPH011, `CreateMap` invocation for POLYMORPH000, `UseConverter` invocation for POLYMORPH021), message template substitution correct, POLYMORPH000 severity changes per `DiagnosticLevel` MSBuild attribute, `IgnoreStaticWarnings()` suppression scope limited to the map it is applied to, `[ThreadSafeForMapping]` suppression scope respects member-level vs type-level application.

---

## Phase 11 - Compatibility Package

Depends on Phase 7. Self-contained.

### 22. `Polymorph.Compatibility.AutoMapper`
`CreateMapper()` alias for `MappingConfiguration.Build()`. Strict mode disabled by default. `ILoggerFactory`-backed `IPolymorphLogger` implementation. Assembly scanning overload of `AddPolymorph(params Assembly[])` - not AOT-safe, migration aid only, marked `[RequiresDynamicCode]` and `[RequiresUnreferencedCode]`. Deprecated shims all marked `[Obsolete]` with migration guidance: `ForCtorParam` → `ForMember`; `ConvertUsing<TConverter>()` → `UseConverter<TConverter>()`; `MapFrom<TConverter>()` → `opt.UseConverter<TConverter>()`; `IgnoreAllPropertiesWithAnException` → `IgnoreAllExcept`.

**Acceptance:** all Tests below pass.

**Tests:** shims delegate correctly to their Polymorph equivalents, `[Obsolete]` attributes present with correct messages, strict mode default is off, assembly scanning discovers profiles with parameterless constructors, profiles requiring constructor arguments silently skipped, duplicate profile throws `MappingConfigurationException` at `Build()` time, AOT diagnostics fire on the assembly scanning overload from AOT-publishable callers.

---

## Phase 12 - Benchmarks

Depends on Phase 7.

### 23. Benchmark Suite
BenchmarkDotNet suite covering all five required scenarios from requirements §4.13, running across all supported target frameworks, with baseline comparisons against AutoMapper 14.x.

| Scenario |
|---|
| Simple convention-based mapping between flat types |
| Nested object mapping with registered sub-maps |
| Collection mapping over small (10), medium (1,000), and large (100,000) element counts to measure per-element allocation impact, including `MappingContext` construction cost |
| Mapping with a custom type converter |
| Mapping with a custom member value resolver |

**Acceptance:** the suite executes end-to-end across all supported target frameworks, produces measurable timing and allocation results for every listed scenario on both the Polymorph and AutoMapper 14.x baseline paths, and the results are stable enough to be meaningful (acceptable variance, no setup-induced outliers). The collection scenario specifically must report per-element allocation counts so that the question of whether `MappingContext` pooling is needed in v1 can be answered from the data. Verifying that Polymorph's results meet the performance bounds in requirements §4.12 is a separate task and is not part of this phase.

---

## Parallelism

Once their individual prerequisites are met, the following phases are independent of each other and may proceed concurrently:

- **Phase 8** (DI Integration) - requires Phase 7
- **Phase 9** (Source Generator) - requires Phase 6
- **Phase 10** (Analyzer) - requires only `Polymorph.Abstractions` contracts
- **Phase 11** (Compatibility) - requires Phase 7
- **Phase 12** (Benchmarks) - requires Phase 7