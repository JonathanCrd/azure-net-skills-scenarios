> **IMPORTANT:**
> - DO NOT look at any files in this repository in the process of following this prompt, except for any skill files located in the `/skills` directory. In particular, do NOT read `validation.md` in this scenario directory.
> - Write all output to the appropriate subfolder of this scenario's directory. If you have Azure SDK skills installed, write to `with-skills/`. If you do not, write to `baseline/`. If you are unsure which applies, ask the user before proceeding.
> - Provide **two complete, separate implementations** — one synchronous and one asynchronous — in separate subdirectories (`sync/` and `async/`) under the output directory. Both implementations should cover the same functionality.

Create a small .NET 8 console application that processes Azure Blob Storage lifecycle events delivered via Event Grid.

The project needs:

- An **event receiver class** (both sync and async versions) that accepts a JSON payload (as if received from an Event Grid webhook endpoint) and deserializes it into structured event objects. It should support both Event Grid schema and CloudEvents 1.0 schema (since Event Grid supports both and the configured schema may vary). It should route events by type — blob-created events get processed one way, blob-deleted another, and unrecognized types are logged as warnings.

- A **blob event handler class** that processes individual blob events. For blob-created events, it should parse the blob's container and name from the event subject, download the blob, and print a summary (name, size, content type, and the blob's access tier). For blob-deleted events, it should just log the deletion. It should handle race conditions gracefully — the blob may have already been deleted or moved to a different tier by the time we try to read it.

- An **event publisher class** (both sync and async versions) that can publish custom events to an Event Grid topic. Given a topic endpoint and a list of custom event objects, it should send them to Event Grid. This would be used for downstream notifications (e.g., "document processed" events). It should support setting a subject hierarchy for event filtering (e.g., "/documents/invoices/processed").

- A **configuration class** that connects to Azure Blob Storage and Event Grid securely. Authentication should use managed identity — no access keys or SAS tokens.

- A **Program.cs** that demos both implementations: constructs a sample Event Grid JSON payload (with both CloudEvents and EventGrid-schema examples) containing mock blob-created and blob-deleted events with realistic structure, feeds them through the receiver and handler, and publishes a custom downstream event. Run the full demo with the sync implementation first, then repeat with the async implementation.

Include a complete `.csproj` with the necessary Azure SDK NuGet dependencies.
