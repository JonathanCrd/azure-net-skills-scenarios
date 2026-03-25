# Validation: keyvault-secret-config

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Security.KeyVault.Secrets` NuGet package
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `Microsoft.Azure.KeyVault` (old SDK)
- [ ] Does NOT contain `Microsoft.Azure.Services.AppAuthentication`
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Security.KeyVault.Secrets` namespace (not `Microsoft.Azure.KeyVault`)
- [ ] Uses `Azure.Identity` namespace
- [ ] No using directives from `Microsoft.Azure.KeyVault`

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential
- [ ] No hardcoded client secrets, certificates, or tenant IDs in source code
- [ ] Reads Key Vault URL from environment variable

### Anti-Pattern Checks
- [ ] No use of `KeyVaultClient` (old v3 API class)
- [ ] No use of `ServiceClientCredentials`
- [ ] No use of `AzureServiceTokenProvider`

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `SecretClient` constructor with `Uri` and `TokenCredential`
- [ ] Pattern: `new SecretClient(new Uri(vaultUrl), credential)`

### Secret Versioning (scenario-specific)
- [ ] Retrieves a specific version of a secret (not just latest)
- [ ] Uses `GetSecret(name, version)` or `GetSecretAsync(name, version)` overload
- [ ] Does NOT construct a URL manually to retrieve a versioned secret

### Secret Expiry Inspection (scenario-specific)
- [ ] Accesses the secret's `Properties` to get expiry date
- [ ] Uses `secret.Properties.ExpiresOn` or equivalent
- [ ] Implements a configurable warning window for near-expiry detection

### Caching Layer (scenario-specific)
- [ ] Implements in-memory caching (e.g., `ConcurrentDictionary` or similar)
- [ ] Supports bulk-loading keys at startup
- [ ] Supports single-key refresh
- [ ] Integrates expiry checking with cache refresh

### Secret Rotation / LRO (scenario-specific)
- [ ] Implements secret deletion as a long-running operation
- [ ] Uses `StartDeleteSecret()` / `StartDeleteSecretAsync()` and polls for completion
- [ ] Uses `DeletedSecret.WaitForCompletion()` or `await DeletedSecret.WaitForCompletionAsync()` to wait
- [ ] Creates new secret after delete completes (not before or concurrently)
- [ ] Does NOT call `DeleteSecret()` directly (fire-and-forget, not safe with soft-delete)

### Error Handling
- [ ] Catches `RequestFailedException` with status 404 for missing secrets
- [ ] Does NOT let a missing secret crash the application
- [ ] Returns a default value when secret is not found

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `SecretClient` with `*Async()` method variants
- [ ] Uses `await` — does NOT call `.Result` or `.GetAwaiter().GetResult()`
- [ ] LRO uses `await WaitForCompletionAsync()` (async) — not `WaitForCompletion()` in async code

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDK (`Azure.Security.KeyVault.Secrets`) | | | Old `Microsoft.Azure.KeyVault` = major failure |
| `DefaultAzureCredential` used | | | |
| `SecretClient(uri, credential)` pattern | | | |
| Secret version retrieval | | | Missing = did not address requirement |
| Secret expiry inspection | | | Missing = did not address requirement |
| Cache with bulk-load + refresh | | | |
| LRO: `StartDeleteSecret` + wait | | | Instant `DeleteSecret` = wrong with soft-delete |
| `RequestFailedException` (404) handling | | | Generic `Exception` = weaker |
| Async uses `await` (no `.Result`) | | | `.Result` = deadlock risk |
| Correct NuGet packages | | | |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
