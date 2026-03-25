# Validation: identity-credential-chain

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `Microsoft.Azure.Services.AppAuthentication` (old approach)
- [ ] Does NOT contain other Azure service SDKs (this scenario is identity-only)
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Identity` namespace
- [ ] Uses `Azure.Core` (for `TokenCredential`, `TokenRequestContext`, `AccessToken`)
- [ ] No using directives from `Microsoft.Azure.Services.AppAuthentication`
- [ ] No using directives from `Azure.Identity.Implementation` (internal packages)

### Auth Pattern
- [ ] No hardcoded client secrets, certificates, or tenant IDs in source code
- [ ] User-assigned managed identity client ID comes from environment variable (not hardcoded)

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Credential Chain Construction
- [ ] Uses `ChainedTokenCredential` to compose multiple credentials
- [ ] Credentials are added in the constructor in order — order matters

### Local Development Chain (scenario-specific)
- [ ] Chains credentials suitable for developer machines
- [ ] Includes `AzureCliCredential` (most common dev credential)
- [ ] May include `VisualStudioCredential`, `VisualStudioCodeCredential`, or `AzurePowerShellCredential`
- [ ] Order makes sense (most-likely-available first)

### CI Pipeline Chain (scenario-specific)
- [ ] Uses `EnvironmentCredential` or `AzurePipelinesCredential` for CI
- [ ] `AzurePipelinesCredential` is the correct choice for Azure Pipelines service connections
- [ ] Does NOT use `DefaultAzureCredential` as the CI credential (too broad, defeats the purpose)

### Production Chain (scenario-specific)
- [ ] Uses `ManagedIdentityCredential` as the primary production credential
- [ ] Supports user-assigned managed identity (passes client ID from `AZURE_CLIENT_ID` env var)
- [ ] Uses `new ManagedIdentityCredential(clientId)` for user-assigned identity
- [ ] Falls back to `WorkloadIdentityCredential` for Kubernetes scenarios
- [ ] Credential ordering: managed identity first, workload identity second

### Continuous Access Evaluation / CAE (scenario-specific)
- [ ] Enables CAE on token requests
- [ ] Uses `TokenRequestContext` with `isCaeEnabled: true` parameter or `IsCaeEnabled` property
- [ ] This is a recent/advanced feature — baseline may miss it entirely

### Environment Detection (scenario-specific)
- [ ] Detects CI environment (checks for `CI`, `TF_BUILD`, `SYSTEM_TEAMFOUNDATIONCOLLECTIONURI`, or similar)
- [ ] Detects production/managed identity (checks for `IDENTITY_ENDPOINT`, `MSI_ENDPOINT`, `IMDS_ENDPOINT`, or similar)
- [ ] Falls back to dev if neither is detected
- [ ] Logic is reasonable and documented

### Token Request & Connectivity Testing (scenario-specific)
- [ ] Creates a `TokenRequestContext` with the correct scope (e.g., `https://management.azure.com/.default`)
- [ ] Calls `GetToken()` (sync) or `GetTokenAsync()` (async)
- [ ] Prints token expiry time from `AccessToken.ExpiresOn`
- [ ] Handles authentication failure with specific exception info (not generic catch)
- [ ] Reports the credential that failed and why

### Error Handling
- [ ] Catches `CredentialUnavailableException` for missing credentials
- [ ] Catches `AuthenticationFailedException` where appropriate
- [ ] Provides actionable error messages (e.g., "Azure CLI not logged in" rather than "auth failed")

## Async Implementation Quality
- [ ] Async connectivity tester exists in a separate subdirectory
- [ ] Token request uses `GetTokenAsync()` with `await`
- [ ] Does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code
- [ ] Properly handles async errors with `try/catch`

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDK (`Azure.Identity`) | | | |
| `ChainedTokenCredential` used | | | Just `DefaultAzureCredential` everywhere = lazy |
| Dev chain has `AzureCliCredential` | | | |
| CI chain has `AzurePipelinesCredential` | | | Just `EnvironmentCredential` = partial |
| Prod chain: managed identity + workload identity | | | |
| User-assigned identity with client ID | | | System-only = partial |
| CAE enabled | | | Missing = likely without skills |
| Environment detection logic | | | |
| `CredentialUnavailableException` handling | | | Generic `Exception` = weaker |
| Token expiry printed | | | |
| Async token request (`GetTokenAsync`) | | | |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
