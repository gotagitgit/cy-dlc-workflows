# .NET and C# Coding Standards

These are team defaults for C# changes. Follow the repository's target framework, language version, `.editorconfig`, analyzers, and established project conventions. Put rules that tools can enforce in those settings and CI; keep this document for guidance that needs explanation.

## .NET Build and Dependency Standards

For .NET repositories using this workflow, use MSBuild centralized configuration (`Directory.Build.props`) and NuGet Central Package Management (`Directory.Packages.props`). Follow these rules when creating or modifying projects.

### Package management (CPM)

- Never specify `Version` attributes on `<PackageReference>` elements inside individual `.csproj` files.
- Declare package versions centrally in the root `Directory.Packages.props` file using `<PackageVersion Include="Package.Name" Version="x.y.z" />`.
- When adding a NuGet package through the CLI or file edits, update `Directory.Packages.props` first. The `.csproj` should contain only `<PackageReference Include="Package.Name" />` for that dependency.
- Avoid `VersionOverride` in `.csproj` files unless explicitly instructed to use a documented compatibility exception.
- Keep package entries in `Directory.Packages.props` sorted alphabetically by package ID.

### Shared build properties (`Directory.Build.props`)

- Do not repeat global MSBuild properties in individual `.csproj` files when they are already defined in `Directory.Build.props`. Examples include `<TargetFramework>`, `<Nullable>`, `<ImplicitUsings>`, `<TreatWarningsAsErrors>`, and `<LangVersion>`.
- Keep `.csproj` files minimal. They should contain project-specific settings (such as `<OutputType>` or a non-standard `<RootNamespace>`), `<ProjectReference>` items, and versionless `<PackageReference>` items.
- Add a property to `Directory.Build.props` only when it applies globally across all projects in its scope.

### New project creation

When scaffolding a new `.csproj` in a repository governed by these standards:

1. Remove redundant `<TargetFramework>`, `<ImplicitUsings>`, and `<Nullable>` properties inherited from `Directory.Build.props`.
2. Remove version numbers from any generated `<PackageReference>` items and add the versions to `Directory.Packages.props`.

## Language and project configuration

- Use the target framework and C# language version declared by the solution and project files. Do not upgrade them as part of unrelated work.
- Enable nullable reference types for new projects and respect the existing nullable context in established projects. Avoid null-forgiving operators unless a checked invariant makes the value non-null and the reason is clear.
- Use implicit global usings only when already enabled by the project. Keep package and framework dependencies minimal; explain new dependencies and verify they are maintained and compatible with the target framework.
- Follow `.editorconfig` and configured analyzers. Do not suppress diagnostics broadly; use a narrow suppression with a reason when a justified exception is needed.

## Naming and API design

- Use PascalCase for types, methods, properties, enum values, and public constants. Use camelCase for local variables and parameters. All interfaces must use the `I` prefix, such as `IAppConfig` or `ISearchService`.
- Use full, descriptive names for variables, properties, attributes, configuration, and repositories; do not shorten names to forms such as `vars`, `attrs`, `config`, or `repo`. Common industry abbreviations are allowed, including `id`, `dto`, `api`, `url`, `uri`, `sql`, `html`, `json`, and `xml`, using conventional C# casing (for example `userId`, `UserDto`, `ApiClient`, and `requestUrl`).
- Use verb-noun names for methods, such as `GetUser`, `CreateOrder`, and `ValidateInput`. Boolean-returning methods should start with `Is`, `Has`, `Can`, or `Should` (for example `IsValid` or `HasPermission`) unless an established framework/API convention requires another form.
- Use UPPER_SNAKE_CASE for private constants only when following an existing legacy convention; otherwise use the repository's standard C# constant naming and do not introduce a second style.
- Prefer names from the domain and use-case language. Avoid unexplained abbreviations and generic names that obscure intent.
- Keep public APIs small and stable. Make types and members no more visible than needed, and document non-obvious public behavior and contracts.
- Use immutable records or value types for value-like data when their semantics fit. Choose classes for identity, lifecycle, or mutable behavior.
- Validate inputs at appropriate boundaries and express invalid states clearly. Do not duplicate validation at every layer without a reason.

## Models and immutability

- Prefer constructor-initialized, get-only properties for domain models. Domain models should be immutable after construction unless a domain lifecycle requires controlled state changes.
- Use setters or init-only properties for transport, serialization, or framework-bound models only when required by that boundary; keep those types separate from domain models where practical.
- Example:

  ```csharp
  public class Person
  {
      public Person(Guid id, string name, string? email)
      {
          Id = id;
          Name = name;
          Email = email;
      }

      public Guid Id { get; }
      public string Name { get; }
      public string? Email { get; }
  }
  ```

## Magic values

- Do not embed semantic string values directly in code. Use named constants, enums, or configuration as appropriate. String literals for logging and exception messages are allowed.
- Do not embed numeric values with domain or business meaning directly in code. Use named constants or configuration as appropriate. Obvious values such as `0`, `1`, and `-1` are allowed in clear contexts such as loop initialization or sentinel checks.

## Code organization

- Keep one primary class per file, except for nested classes. Name the file to match its primary class.
- Order class members in this sequence:
  1. Constants and static fields
  2. Instance fields
  3. Constructors
  4. Properties
  5. Public methods
  6. Protected methods
  7. Private methods
- Keep related overloads together within their visibility group.
- Place using directives at the top of the file, outside the namespace. Order them with `System` namespaces first, then third-party namespaces, then project namespaces. Remove unused using directives.

## Methods, errors, and asynchronous work

- Keep methods focused and straightforward to follow. Extract helpers when they clarify a meaningful operation, not just to reduce line count.
- Use `async`/`await` for naturally asynchronous I/O. Avoid blocking asynchronous work with `.Result`, `.Wait()`, or sync-over-async patterns.
- Accept and pass `CancellationToken` through public asynchronous operations that perform cancellable work. Honor cancellation before starting expensive work when practical.
- Use `Task` or `Task<T>` for asynchronous methods; use `ValueTask` only when measurement or a well-understood API contract justifies it. Avoid `async void` except event handlers.
- Throw specific exceptions for exceptional failures. Do not use exceptions for expected control flow. Preserve the original exception when adding context, and translate errors at meaningful boundaries.
- Dispose owned `IDisposable` and `IAsyncDisposable` resources deterministically, preferably with `using` or `await using`.

## Dependency injection, logging, and security

- Make dependencies explicit through constructors or the project's established composition pattern. Keep object construction and service registration at composition roots.
- Use dependency injection where it improves replaceability or lifecycle management; avoid injecting broad service containers or resolving dependencies from a service locator.
- Use structured logging with stable event meaning. Do not log credentials, tokens, secrets, or sensitive personal data. Avoid logging the same failure at multiple layers without adding useful context.
- Keep secrets and environment-specific values out of source code. Use the repository's approved configuration and secret-management approach.
- Validate and encode untrusted input at system boundaries. Apply authorization at the boundary and enforce important domain invariants in the domain/application logic.

## Testing and maintainability

- Follow the repository's test framework and naming patterns. Test observable behavior and important edge cases, including failure and cancellation paths where relevant.
- Keep unit tests isolated from external services. Use integration tests for persistence, messaging, and other real infrastructure boundaries when those behaviors need verification.
- Prefer deterministic tests with explicit setup and assertions. Avoid sleeps, shared mutable state, and dependence on test execution order.
- Do not add tests that only restate implementation details. When fixing a bug, add coverage that would fail before the fix when practical.
- Refactor locally when needed to keep the change understandable, but avoid unrelated cleanup that obscures the feature or bug fix.

## Formatting and enforcement

Run the format, build, analyzer, and test commands documented by the repository when the task calls for validation. The solution's `.editorconfig`, analyzer configuration, `Directory.Build.props`/`targets`, and CI are the enforceable sources for mechanical standards; update them through normal review when a standard should become mandatory.
