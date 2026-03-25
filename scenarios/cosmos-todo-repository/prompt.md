> **IMPORTANT:**
> - DO NOT look at any files in this repository in the process of following this prompt, except for any skill files located in the `/skills` directory. In particular, do NOT read `validation.md` in this scenario directory.
> - Write all output to the appropriate subfolder of this scenario's directory. If you have Azure SDK skills installed, write to `with-skills/`. If you do not, write to `baseline/`. If you are unsure which applies, ask the user before proceeding.
> - Provide **two complete, separate implementations** of the repository — one synchronous and one asynchronous — in separate subdirectories (`sync/` and `async/`) under the output directory.

Create a small .NET 8 console application that implements a ToDo item CRUD repository backed by Azure Cosmos DB (NoSQL API).

The project needs:

- A **model class** (shared by both implementations) for a ToDo item with properties for id, title, description, completed status, created timestamp, and category (where category is the partition key).

- A **synchronous repository class** that performs CRUD operations against Cosmos DB. It should support create, read, update, delete, and a query-by-category method. Each operation should log the request charge (RU cost consumed). The update operation should prevent lost updates — if another process modified the item since it was last read, the update should fail with a clear conflict error rather than silently overwriting the other process's changes. The query method should use safe, parameterized queries and must handle large result sets properly — paginate through results rather than loading everything into memory at once, and log progress as each page is retrieved.

- An **asynchronous repository class** that provides the same CRUD operations. The query method should return results as an async stream of pages, and the caller should be able to process each page as it arrives.

- A **configuration/factory class** that connects to the Cosmos DB account using its endpoint from an environment variable. Authentication must use managed identity (no master keys). It should create the database and container if they don't already exist, setting a default TTL (time-to-live) of 90 days on the container and configuring the indexing policy to exclude the `description` field from indexing (since it's never queried on).

- A **Program.cs** that demos both implementations: runs the full CRUD cycle using the sync repository first (including paginated query output showing page-by-page results), then runs the same operations using the async repository. Print RU costs and results to the console.

Include a complete `.csproj` with the necessary Azure SDK NuGet dependencies.
