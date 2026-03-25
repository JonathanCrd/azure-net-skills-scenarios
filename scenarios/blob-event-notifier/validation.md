# Validation: blob-event-notifier

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Messaging.EventGrid` NuGet package
- [ ] Contains `Azure.Storage.Blobs` NuGet package
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `WindowsAzure.Storage` or `Microsoft.WindowsAzure.Storage`
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Messaging.EventGrid` namespace
- [ ] Uses `Azure.Storage.Blobs` namespace
- [ ] Uses `Azure.Identity` namespace
- [ ] No using directives from `Microsoft.WindowsAzure.Storage`
- [ ] No fabricated Event Grid namespaces

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential
- [ ] No hardcoded access keys, connection strings, or SAS tokens
- [ ] Reads endpoints from environment variables

### Anti-Pattern Checks
- [ ] No use of `CloudStorageAccount` or `CloudBlobClient`
- [ ] No fabricated Event Grid classes that don't exist in the SDK

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `BlobServiceClient` constructor with `Uri` and `TokenCredential`
- [ ] Uses `EventGridPublisherClient` constructor with `Uri` and `AzureKeyCredential` or `TokenCredential`

### Event Grid Schema Support (scenario-specific — critical)
- [ ] Handles Event Grid native schema (`EventGridEvent`)
- [ ] Handles CloudEvents 1.0 schema (`CloudEvent`)
- [ ] Uses `EventGridEvent.ParseMany()` or `EventGridEvent.Parse()` for deserialization
- [ ] Uses `CloudEvent.ParseMany()` or `CloudEvent.Parse()` for CloudEvents deserialization
- [ ] Does NOT manually deserialize the JSON without the SDK's deserialization helpers

### Event Routing (scenario-specific)
- [ ] Routes events based on event type string
- [ ] Handles `Microsoft.Storage.BlobCreated` events
- [ ] Handles `Microsoft.Storage.BlobDeleted` events
- [ ] Logs a warning for unrecognized event types (not silently ignoring)

### Blob Subject Parsing (scenario-specific)
- [ ] Parses container name and blob name from the event subject
- [ ] Subject pattern: `/blobServices/default/containers/{container}/blobs/{blob}`
- [ ] Parsing is robust (handles nested blob paths with `/` in the name)

### Event Publishing (scenario-specific)
- [ ] Uses `EventGridPublisherClient` to publish custom events
- [ ] Sets a subject hierarchy for filtering (e.g., "/documents/invoices/processed")
- [ ] Creates properly structured custom events (`EventGridEvent` or `CloudEvent`) with required fields

### Blob Access Tier (scenario-specific)
- [ ] Retrieves and prints the blob's access tier for created events
- [ ] Uses blob properties (`BlobProperties.AccessTier`) to get the access tier

### Race Condition Handling (scenario-specific)
- [ ] Handles the case where the blob no longer exists (already deleted)
- [ ] Catches `RequestFailedException` with status 404
- [ ] Does not crash on the race condition — logs a warning or handles gracefully

### Error Handling
- [ ] Catches `RequestFailedException` for blob errors
- [ ] Catches appropriate exceptions for Event Grid publishing errors
- [ ] Does not use bare `Exception` catches

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `*Async()` method variants with `await` for blob and Event Grid operations
- [ ] Does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDKs (eventgrid + blob + identity) | | | |
| `DefaultAzureCredential` used | | | |
| Event Grid schema deserialization | | | Manual JSON parse = weaker |
| CloudEvents 1.0 support | | | Missing = did not address requirement |
| Both `EventGridEvent` and `CloudEvent` | | | Only one = partial |
| Event routing by type | | | |
| Subject parsing for container/blob | | | |
| Event publishing with subject hierarchy | | | Missing = did not address requirement |
| Blob access tier reported | | | Missing = did not address requirement |
| Race condition (404) handled | | | Crash on missing blob = failure |
| Async implementation present | | | |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
