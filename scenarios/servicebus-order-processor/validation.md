# Validation: servicebus-order-processor

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Messaging.ServiceBus` NuGet package
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `Microsoft.Azure.ServiceBus` (old SDK)
- [ ] Does NOT contain `WindowsAzure.ServiceBus`
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Messaging.ServiceBus` namespace (not `Microsoft.Azure.ServiceBus`)
- [ ] Uses `Azure.Identity` namespace
- [ ] No using directives from `Microsoft.Azure.ServiceBus`
- [ ] No using directives from `Microsoft.ServiceBus`

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential
- [ ] Uses fully-qualified namespace (not connection string) with the credential
- [ ] No hardcoded connection strings or SAS tokens
- [ ] Reads namespace from environment variable

### Anti-Pattern Checks
- [ ] No use of `QueueClient` (old SDK class)
- [ ] No use of `IMessageHandler` or `MessageHandlerOptions`
- [ ] No use of `ConnectionStringBuilder` from old SDK

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `ServiceBusClient` as the entry point with namespace and credential
- [ ] Sender: uses `client.CreateSender(queueName)` to create `ServiceBusSender`
- [ ] Processor: uses `client.CreateProcessor(queueName, options)` to create `ServiceBusProcessor`
- [ ] Pattern: `new ServiceBusClient(fullyQualifiedNamespace, credential)`

### Batch Sending (scenario-specific)
- [ ] Creates a `ServiceBusMessageBatch` via `sender.CreateMessageBatchAsync()`
- [ ] Checks `TryAddMessage()` return value before adding each message
- [ ] Handles the case where a message doesn't fit in the batch

### Scheduled Delivery (scenario-specific)
- [ ] Implements scheduled delivery for high-priority orders
- [ ] Uses `ScheduledEnqueueTime` property on `ServiceBusMessage` or `ScheduleMessageAsync()`
- [ ] Delay is approximately 30 seconds as specified

### Correlation Properties (scenario-specific)
- [ ] Sets order ID as the correlation ID on the message
- [ ] Uses `ServiceBusMessage.CorrelationId` property

### Dead-Letter Queue (scenario-specific)
- [ ] Explicitly dead-letters failed messages (not just abandoning)
- [ ] Uses `args.CompleteMessageAsync()` / `args.DeadLetterMessageAsync()` with a reason string
- [ ] Implements reading from the dead-letter sub-queue
- [ ] Uses `ServiceBusReceiver` with `SubQueue.DeadLetter` option

### Session-Aware Receiving (scenario-specific)
- [ ] Implements session-aware message processing
- [ ] Uses `ServiceBusSessionProcessor` or session-enabled `ServiceBusSessionReceiver`
- [ ] Session ID is keyed by customer name as specified

### Error Handling
- [ ] Catches `ServiceBusException` (not just `Exception`)
- [ ] Error handler in processor logs entity path and error reason
- [ ] Distinguishes transient vs non-transient errors (via `IsTransient` property)

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `*Async()` method variants with `await`
- [ ] Does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code
- [ ] `ServiceBusProcessor` event handlers are properly async

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDK (`Azure.Messaging.ServiceBus`) | | | Old `Microsoft.Azure.ServiceBus` = major failure |
| `DefaultAzureCredential` with namespace (not conn string) | | | |
| `ServiceBusClient` pattern | | | |
| Batch sending with `TryAddMessage` check | | | Missing check = messages silently dropped |
| Scheduled delivery (~30s delay) | | | Missing = did not address requirement |
| Dead-letter with reason string | | | Abandon = weaker; missing = failure |
| Dead-letter queue reading | | | Missing = did not address requirement |
| Session-aware receiving | | | Missing = did not address requirement |
| `ServiceBusException` handling | | | Generic `Exception` = weaker |
| Async implementation present | | | |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
