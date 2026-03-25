# Validation: [Scenario Name]

<!-- Replace everything in [brackets] with scenario-specific content. -->

## 1. Automated Checks

Run these against each generated project (baseline and with-skills):

| # | Check | Command / What to Look For |
|---|-------|---------------------------|
| 1 | **Correct dependencies** | `.csproj` includes the correct `Azure.*` NuGet packages |
| 2 | **Correct using directives** | All Azure using directives start with `Azure.` (not deprecated `Microsoft.WindowsAzure.*` or `WindowsAzure.*`) |
| 3 | **Authentication** | Uses `DefaultAzureCredential` or appropriate credential from `Azure.Identity` |
| 4 | **No banned patterns** | No connection-string auth, no hardcoded keys, no `new DefaultAzureCredential()` with explicit options where not needed |
| 5 | **Compiles** | `dotnet build` succeeds (or at minimum: no obvious syntax errors) |

## 2. SDK Usage Quality

Check each item; mark Y (present), N (missing), or P (partial):

| # | Criterion | What to Look For |
|---|-----------|-----------------|
| 1 | [Criterion name] | [What it means for this scenario] |
| 2 | [Criterion name] | [What it means for this scenario] |
| 3 | [Criterion name] | [What it means for this scenario] |
| 4 | [Criterion name] | [What it means for this scenario] |

## 3. Async Implementation Quality

| # | Check | What to Look For |
|---|-------|-----------------|
| 1 | **Separate subdirectory** | `sync/` and `async/` exist as separate directories |
| 2 | **Async methods used** | Async variant uses `*Async()` methods with `await` |
| 3 | **No blocking calls** | Async code does not call `.Result`, `.Wait()`, or `.GetAwaiter().GetResult()` |
| 4 | **Error handling** | Catches `RequestFailedException` (not just `Exception`) |

## 4. Comparison Table

Fill in after evaluating both outputs:

| Criterion | Baseline | With Skills | Notes |
|-----------|----------|-------------|-------|
| Automated checks pass | | | |
| [Criterion 1] | | | |
| [Criterion 2] | | | |
| [Criterion 3] | | | |
| Async quality | | | |
| **Overall** | | | |
