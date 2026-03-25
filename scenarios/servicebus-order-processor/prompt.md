> **IMPORTANT:**
> - DO NOT look at any files in this repository in the process of following this prompt, except for any skill files located in the `/skills` directory. In particular, do NOT read `validation.md` in this scenario directory.
> - Write all output to the appropriate subfolder of this scenario's directory. If you have Azure SDK skills installed, write to `with-skills/`. If you do not, write to `baseline/`. If you are unsure which applies, ask the user before proceeding.
> - Provide **two complete, separate implementations** — one synchronous and one asynchronous — in separate subdirectories (`sync/` and `async/`) under the output directory. Both implementations should cover the same functionality.

Create a small .NET 8 console application that implements an order processing system using Azure Service Bus.

The project needs:

- A **model class** for an Order with properties for order ID, customer name, product, quantity, total price, and status (pending/processing/completed/failed). It should be serializable to and from JSON.

- A **sender class** (both sync and async versions) that publishes order messages to a Service Bus queue. It should support sending individual orders and sending a batch of orders efficiently (respecting the maximum batch size so messages aren't rejected). Each message should carry the order ID as a correlation ID property, and orders above a certain dollar threshold should be sent with high priority and a scheduled delivery delay of 30 seconds (to allow for fraud review before processing).

- A **processor class** (both sync and async versions) that receives and processes orders from the queue. It should handle messages as they arrive, deserialize them, and log the results. If processing fails (e.g., a deserialization error), the message should be sent to the dead-letter queue with a reason string rather than being silently abandoned. The processor should also be able to read from the dead-letter queue so failed messages can be inspected and reprocessed. It should guarantee that orders from the same customer are processed in sequence, not interleaved with other customers' orders.

- A **Program.cs** that demos both implementations: connects to the Service Bus namespace (from an environment variable) with managed identity, runs the full send/receive/dead-letter cycle using the sync implementation first, then repeats with the async implementation.

Include a complete `.csproj` with the necessary Azure SDK NuGet dependencies.
