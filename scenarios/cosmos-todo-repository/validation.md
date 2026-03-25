# Validation: cosmos-todo-repository

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Microsoft.Azure.Cosmos` NuGet package
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `Microsoft.Azure.DocumentDB` or `Microsoft.Azure.DocumentDB.Core` (old SDK)
- [ ] Does NOT contain `Microsoft.Azure.DocumentDB.ChangeFeedProcessor`
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Microsoft.Azure.Cosmos` namespace (not `Microsoft.Azure.Documents`)
- [ ] Uses `Azure.Identity` namespace
- [ ] No using directives from `Microsoft.Azure.Documents`

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential
- [ ] No hardcoded master keys or connection strings
- [ ] Reads Cosmos DB endpoint from environment variable

### Anti-Pattern Checks
- [ ] No use of `DocumentClient` (old v2 API)
- [ ] No use of `DocumentClientException`
- [ ] No connection string containing `AccountKey=...`

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `CosmosClient` constructor with endpoint and `TokenCredential`
- [ ] Pattern: `new CosmosClient(endpoint, credential)` or `CosmosClientBuilder` with credential
- [ ] Uses `CosmosClientOptions` for configuration (e.g., `SerializerOptions`)

### Partition Key Handling
- [ ] Uses `PartitionKey` correctly with the `category` field
- [ ] All point operations (read, update, delete) include the partition key — not just the id
- [ ] Container creation specifies `/category` as the partition key path

### Optimistic Concurrency / ETags (scenario-specific)
- [ ] Read operation captures the ETag from `ItemResponse<T>.ETag`
- [ ] Update operation uses `ItemRequestOptions` with `IfMatchEtag` set
- [ ] Catches `CosmosException` with `HttpStatusCode.PreconditionFailed` (412) as a specific error case

### Pagination (scenario-specific)
- [ ] Sync query uses `GetItemQueryIterator<T>` and iterates `FeedResponse<T>` pages
- [ ] Has configurable page size via `QueryRequestOptions.MaxItemCount`
- [ ] Logs or prints the continuation token per page
- [ ] Logs item count per page
- [ ] Does NOT call `.ToList()` or similar to flatten all results into memory

### Async Pagination (scenario-specific)
- [ ] Async query uses `GetItemQueryIterator<T>` with `await` and `HasMoreResults`
- [ ] Caller can process pages individually as they arrive via `IAsyncEnumerable<T>` or page iteration

### Parameterized Query (scenario-specific)
- [ ] Uses `QueryDefinition` with `WithParameter()` for query parameters
- [ ] Does NOT build the query with string interpolation or concatenation

### TTL Configuration (scenario-specific)
- [ ] Sets default TTL on the container (90 days = 7776000 seconds)
- [ ] Uses `ContainerProperties.DefaultTimeToLive` property

### Indexing Policy (scenario-specific)
- [ ] Configures a custom indexing policy
- [ ] Excludes `/description` path from indexing
- [ ] Uses `ContainerProperties.IndexingPolicy.ExcludedPaths`

### RU Cost Logging (scenario-specific)
- [ ] Extracts request charge from response (e.g., `response.RequestCharge`)
- [ ] Logs/prints RU cost for each operation

### Error Handling
- [ ] Catches `CosmosException` (not just `Exception`)
- [ ] Checks `CosmosException.StatusCode` for specific error handling (404, 409, 412)
- [ ] Handles 412 (precondition failed) separately for ETag conflicts

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `*Async()` method variants with `await`
- [ ] Does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code
- [ ] Pagination uses `await` in the page loop

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDK (`Microsoft.Azure.Cosmos`) | | | Old `DocumentDB` = major failure |
| `DefaultAzureCredential` (no master key) | | | |
| `CosmosClient` with credential pattern | | | |
| Correct partition key usage | | | Missing partition key = runtime errors |
| ETag-based optimistic concurrency | | | Missing = did not address requirement |
| Parameterized query | | | String concat = potential injection risk |
| Pagination with page-size control | | | Flattened results = did not address requirement |
| TTL configured (90 days) | | | Missing = did not address requirement |
| Indexing policy excludes `description` | | | Missing = did not address requirement |
| RU cost logged per operation | | | |
| `CosmosException` with status codes | | | Generic `Exception` = weaker |
| Async uses `await` (no `.Result`) | | | `.Result` = deadlock risk |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
