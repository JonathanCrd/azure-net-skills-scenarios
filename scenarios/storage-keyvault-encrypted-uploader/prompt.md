> **IMPORTANT:**
> - DO NOT look at any files in this repository in the process of following this prompt, except for any skill files located in the `/skills` directory. In particular, do NOT read `validation.md` in this scenario directory.
> - Write all output to the appropriate subfolder of this scenario's directory. If you have Azure SDK skills installed, write to `with-skills/`. If you do not, write to `baseline/`. If you are unsure which applies, ask the user before proceeding.
> - Provide **two complete, separate implementations** — one synchronous and one asynchronous — in separate subdirectories (`sync/` and `async/`) under the output directory. Both implementations should cover the same functionality.

Create a small .NET 8 console application that uploads files to Azure Blob Storage with client-side encryption, where the encryption key material is managed in Azure Key Vault.

The project needs:

- A **key management class** (both sync and async versions) that interacts with Azure Key Vault's Keys service (not Secrets) to perform cryptographic operations. It should implement envelope encryption: generate a data encryption key locally, use Key Vault to protect (wrap) it, and store the protected key alongside the encrypted blob. For decryption, have Key Vault recover (unwrap) the data key, then decrypt locally. The raw data key should never be persisted anywhere, and the vault's key material should never leave Key Vault.

- A **blob uploader/downloader class** (both sync and async versions) that handles the actual encryption and storage. For upload: generate a data key, encrypt the data locally, protect the data key via Key Vault, then upload the ciphertext to Blob Storage with the protected key and any necessary cryptographic parameters stored as blob metadata. For download: read the blob and its metadata, recover the data key via Key Vault, and decrypt. Should handle errors from both services (e.g., the vault key may have been disabled, or the blob may not exist).

- A **configuration class** that builds the necessary Azure connections for both Blob Storage and Key Vault. It should read endpoints from environment variables and authenticate with managed identity. All connections should share a single credential instance.

- A **Program.cs** that demos both implementations: runs the full encrypt-upload-download-decrypt round-trip using the sync implementation first, then repeats with the async implementation. Print the vault key ID used, the wrapped DEK (base64), and the decrypted output.

Include a complete `.csproj` with the necessary Azure SDK NuGet dependencies.
