# Validation: blob-storage-manager

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

These can be verified by grep, compilation, or script.

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Storage.Blobs` NuGet package (not `WindowsAzure.Storage` or `Microsoft.WindowsAzure.Storage`)
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `WindowsAzure.Storage` package reference
- [ ] Does NOT contain `Microsoft.WindowsAzure.Storage` package reference
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Storage.Blobs` namespace (not `Microsoft.WindowsAzure.Storage`)
- [ ] Uses `Azure.Identity` namespace
- [ ] No using directives from `Microsoft.WindowsAzure.Storage`

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential — not connection strings
- [ ] No hardcoded account keys, connection strings, or SAS tokens in source code
- [ ] Reads storage endpoint from environment variable

### Anti-Pattern Checks
- [ ] No use of `CloudStorageAccount` (deprecated v11 API)
- [ ] No use of `CloudBlobClient` or `CloudBlobContainer`
- [ ] No use of `StorageCredentials` with an account key

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `BlobServiceClient` constructor with `Uri` and `TokenCredential` (not connection string overload)
- [ ] Client is constructed using `new BlobServiceClient(new Uri(endpoint), credential)` pattern
- [ ] Async implementation uses `async/await` with `*Async()` methods (not wrapping sync in `Task.Run`)

### Retry & HTTP Pipeline (scenario-specific)
- [ ] Configures a custom retry policy via `BlobClientOptions.Retry`
- [ ] Sets `MaxRetries`, `Delay`, and `Mode = RetryMode.Exponential`
- [ ] Sets a per-request or per-operation timeout (via `BlobClientOptions` or `CancellationToken`)
- [ ] Enables HTTP logging (e.g., via `BlobClientOptions` diagnostics or `Azure.Core.Pipeline`)

### Blob Lease (scenario-specific)
- [ ] Implements blob lease acquisition before overwrite using `BlobLeaseClient`
- [ ] Uses `AcquireLease()` / `AcquireLeaseAsync()` before the upload/overwrite operation
- [ ] Releases the lease after the operation completes

### Parallel Upload (scenario-specific)
- [ ] Implements parallel/block upload for large files
- [ ] Uses `StorageTransferOptions` with `MaximumConcurrency` and block size settings
- [ ] Does NOT load entire file into a `MemoryStream` — streams from disk

### Blob Index Tags (scenario-specific)
- [ ] Sets blob index tags on upload (not just metadata)
- [ ] Tags are a `Dictionary<string, string>` passed via `BlobUploadOptions.Tags`

### Error Handling
- [ ] Catches `RequestFailedException` (not just `Exception`)
- [ ] Handles or logs the HTTP status code from storage errors (`ex.Status`)

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `*Async()` method variants (e.g., `UploadAsync`, `DownloadAsync`, `DeleteAsync`)
- [ ] Uses `await` — does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code
- [ ] Uses `AsyncPageable<BlobItem>` for listing blobs asynchronously
- [ ] `CancellationToken` is accepted and forwarded to SDK calls

## Comparison: Baseline vs With-Skills

When both outputs exist, compare:

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDK (`Azure.Storage.Blobs`) | | | `WindowsAzure.Storage` = major failure |
| `DefaultAzureCredential` used | | | Connection strings = failure |
| `BlobServiceClient(uri, credential)` pattern | | | |
| `RequestFailedException` handling | | | Generic `Exception` = weaker |
| Retry policy configured | | | Missing = acceptable but weaker |
| HTTP logging configured | | | Missing = acceptable but weaker |
| Parallel upload with `StorageTransferOptions` | | | Missing = did not address requirement |
| Blob lease implemented | | | Missing = did not address requirement |
| Blob index tags (not just metadata) | | | Metadata only = partial credit |
| Async uses `await` (no `.Result`) | | | `.Result` = deadlock risk |
| Correct NuGet packages | | | Wrong package = compilation failure |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
