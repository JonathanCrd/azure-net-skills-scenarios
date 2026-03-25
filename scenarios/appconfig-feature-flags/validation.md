# Validation: appconfig-feature-flags

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Data.AppConfiguration` NuGet package
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain fabricated/non-existent package references
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Data.AppConfiguration` namespace
- [ ] Uses `Azure.Identity` namespace
- [ ] No using directives from fabricated namespaces

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential
- [ ] No hardcoded connection strings or access keys
- [ ] Reads App Configuration endpoint from environment variable

### Anti-Pattern Checks
- [ ] No use of connection string-based authentication
- [ ] No fabricated/hallucinated class names that don't exist in the SDK

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `ConfigurationClient` constructor with `Uri` and `TokenCredential`
- [ ] Pattern: `new ConfigurationClient(new Uri(endpoint), credential)`

### Label-Based Configuration (scenario-specific)
- [ ] Retrieves settings with a specific label parameter
- [ ] Uses `SettingSelector` with `LabelFilter` for label-based filtering
- [ ] Demonstrates different labels for different environments (e.g., "production", "staging")

### Prefix Listing (scenario-specific)
- [ ] Lists settings filtered by key prefix
- [ ] Uses `SettingSelector` with `KeyFilter` using a wildcard (e.g., `"app/*"`)
- [ ] Returns results as a dictionary

### Conditional Reads (scenario-specific)
- [ ] Implements conditional reads to avoid re-downloading unchanged settings
- [ ] Uses `MatchConditions` with the setting's `ETag`
- [ ] Handles `RequestFailedException` with status 304 (Not Modified) as "unchanged"
- [ ] Returns the cached value when unchanged

### Feature Flag Key Prefix (scenario-specific)
- [ ] Uses `.appconfig.featureflag/` prefix for feature flag keys
- [ ] Parses the JSON payload stored in the feature flag setting value
- [ ] Checks the `enabled` field in the JSON

### Percentage Rollout (scenario-specific)
- [ ] Implements percentage-based evaluation for feature flags
- [ ] Uses a deterministic/consistent hash of the user ID (same user → same result every time)
- [ ] Does NOT use `Random` or other non-deterministic approaches

### Sentinel Key Watching (scenario-specific)
- [ ] Implements a polling loop that watches sentinel keys
- [ ] Detects when a sentinel key's value changes (via ETag comparison or value comparison)
- [ ] Triggers a full config refresh when sentinel changes
- [ ] Has a configurable polling interval

### Error Handling
- [ ] Handles missing settings gracefully (returns null, or default value)
- [ ] Catches `RequestFailedException` or appropriate exceptions

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `*Async()` method variants with `await`
- [ ] Does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code
- [ ] Uses `AsyncPageable<ConfigurationSetting>` for listing

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDK (`Azure.Data.AppConfiguration`) | | | |
| `DefaultAzureCredential` (not connection string) | | | |
| `ConfigurationClient(uri, credential)` pattern | | | |
| Label-based setting retrieval | | | Missing = did not address requirement |
| Conditional reads with ETag/304 | | | Missing = did not address requirement |
| `.appconfig.featureflag/` prefix | | | Wrong prefix = flags won't be found |
| Percentage rollout (deterministic) | | | `Random` = non-deterministic = wrong |
| Sentinel key watching pattern | | | Missing = did not address requirement |
| Async uses `await` (no `.Result`) | | | `.Result` = deadlock risk |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
