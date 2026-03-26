---
description: "Migrate a Delphi (Object Pascal) unit or class to modern .NET 8+ C# with clean architecture patterns"
agent: "agent"
tools: [read, search, editFiles, createFiles]
name: "Migrate Delphi to .NET"
argument-hint: "Provide the Delphi source code (.pas) or file path to migrate"
---
Migrate the provided Delphi (Object Pascal) code to idiomatic .NET 8+ C#.

## Migration Steps

1. **Identify the unit type** — procedure, class, data module, form, or utility
2. **Map Delphi types to C#**:
   - `Integer` → `int`, `String` → `string`, `Boolean` → `bool`
   - `Currency` → `decimal`, `TDateTime` → `DateTime`
   - `TStringList` → `List<string>`, `TList<T>` → `List<T>`
   - `TDictionary<K,V>` → `Dictionary<TKey,TValue>`
3. **Convert the code** following these patterns:
   - Unit `interface` section → public members; `implementation` → private methods
   - `constructor Create` → constructor injection via DI
   - `try/finally obj.Free` → `using` statement or DI-managed lifetime
   - `raise Exception.Create(...)` → `throw new SpecificException(...)`
   - `Assigned(obj)` → `obj is not null`
   - `property ... read ... write ...` → C# auto-property
   - `with obj do` → explicit variable reference (no C# equivalent)
4. **Apply .NET conventions**:
   - File-scoped namespaces
   - `async/await` for all I/O (Delphi is synchronous)
   - Dependency injection instead of `Create`/`Free`
   - `ILogger<T>` instead of `ShowMessage` or `WriteLn`
5. **Generate xUnit tests** for the converted logic

## Constraints

- Never instantiate services directly — use DI
- Replace global variables with scoped DI services
- Replace `initialization`/`finalization` sections with DI registration or `IHostedService`
- Delphi strings are 1-based; C# is 0-based — adjust all index operations
- Replace `TThread` with `Task` / `async-await`
- Use `record` types for DTOs, `class` for entities

## Output

For each Delphi unit, produce:
1. C# file(s) following clean architecture (`src/Domain/`, `src/Application/`, `src/Infrastructure/`, `src/Api/`)
2. A before/after summary table showing what each Delphi construct became
3. DI registration lines for `Program.cs`
