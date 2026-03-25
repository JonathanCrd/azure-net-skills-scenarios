> **IMPORTANT:**
> - DO NOT look at any files in this repository in the process of following this prompt, except for any skill files located in the `/skills` directory. In particular, do NOT read `validation.md` in this scenario directory.
> - Write all output to the appropriate subfolder of this scenario's directory. If you have Azure SDK skills installed, write to `with-skills/`. If you do not, write to `baseline/`. If you are unsure which applies, ask the user before proceeding.
> - Provide **two complete, separate implementations** — one synchronous and one asynchronous — in separate subdirectories (`sync/` and `async/`) under the output directory. Both implementations should cover the same functionality.

Create a small .NET 8 console application that implements an application configuration provider backed by Azure Key Vault.

The project needs:

- A **secret provider class** (both sync and async versions) that retrieves secrets from Key Vault by name, with graceful handling when a secret doesn't exist (return a default value instead of crashing). It should also be able to retrieve a specific version of a secret (not just the latest), and inspect a secret's expiry date so the caller can tell if a secret is about to expire.

- A **caching layer** on top of the provider that stores secret values in memory after first retrieval. It should support bulk-loading a predefined set of required config keys at startup, on-demand refresh of individual keys, and automatic re-fetch of any secret whose expiry date is within a configurable warning window (e.g., 7 days out).

- A **configuration/factory class** that connects securely to the Key Vault using the vault URL from an environment variable. The application runs in Azure and should authenticate using managed identity — no client secrets or certificates in code.

- A **secret rotation helper** that safely rotates a secret: delete the old secret, ensure the deletion is fully complete, then create the new secret with an updated value and expiry date. The rotation must be safe — don't assume deletion is instantaneous, since Key Vault's soft-delete feature means the secret may not be immediately gone.

- A **Program.cs** that demos both implementations: loading several config keys at startup, reading them from cache, refreshing one, printing a warning if any secret is near expiry, and performing a secret rotation (delete old, wait for completion, create new). Run the full demo with the sync implementation first, then repeat with the async implementation.

Include a complete `.csproj` with the necessary Azure SDK NuGet dependencies.
