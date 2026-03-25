# Validation: storage-keyvault-encrypted-uploader

Use this file to evaluate the generated code **after** the code generation step is complete.

## Automated Checks

### Dependency Checks (.csproj)
- [ ] Contains `Azure.Storage.Blobs` NuGet package
- [ ] Contains `Azure.Security.KeyVault.Keys` (Keys, not Secrets — the prompt asks for key wrap/unwrap)
- [ ] Contains `Azure.Identity` NuGet package
- [ ] Does NOT contain `WindowsAzure.Storage` or `Microsoft.WindowsAzure.Storage`
- [ ] Targets `net8.0` or later

### Using Directive Checks
- [ ] Uses `Azure.Storage.Blobs` namespace
- [ ] Uses `Azure.Security.KeyVault.Keys` or `Azure.Security.KeyVault.Keys.Cryptography` namespace
- [ ] Uses `Azure.Identity` namespace
- [ ] Uses `System.Security.Cryptography` for local AES-GCM encryption
- [ ] No using directives from `Microsoft.WindowsAzure.Storage`

### Auth Pattern
- [ ] Uses `DefaultAzureCredential` or another `Azure.Identity` credential
- [ ] Shares a single credential instance between Blob Storage and Key Vault clients
- [ ] No hardcoded keys, connection strings, or SAS tokens
- [ ] Reads endpoints from environment variables

### Anti-Pattern Checks
- [ ] No use of `CloudStorageAccount` or `CloudBlobClient`
- [ ] No use of old Key Vault SDK classes (`KeyVaultClient`)
- [ ] No storing raw encryption key material in plaintext (the DEK should only exist wrapped)

### Compilation
- [ ] `dotnet build` succeeds for the sync implementation
- [ ] `dotnet build` succeeds for the async implementation

## SDK Usage Quality

### Client Construction
- [ ] Uses `BlobServiceClient` constructor with `Uri` and `TokenCredential`
- [ ] Uses `CryptographyClient` from `Azure.Security.KeyVault.Keys.Cryptography` for wrap/unwrap operations (NOT `SecretClient`)
- [ ] Both builders use credential from the shared `DefaultAzureCredential` instance

### Key Vault Keys vs Secrets (scenario-specific — critical)
- [ ] Uses the Key Vault **Keys** service (`Azure.Security.KeyVault.Keys`), NOT Secrets
- [ ] Uses `CryptographyClient` for wrap/unwrap operations
- [ ] Uses `WrapKey()` / `WrapKeyAsync()` and `UnwrapKey()` / `UnwrapKeyAsync()` APIs
- [ ] Specifies an RSA key wrap algorithm (e.g., `KeyWrapAlgorithm.RsaOaep` or `RsaOaep256`)
- [ ] The RSA key material never leaves Key Vault (wrap/unwrap happens server-side)

### Envelope Encryption Pattern (scenario-specific — critical)
- [ ] Generates a random AES-256 DEK locally (32 bytes via `RandomNumberGenerator`)
- [ ] Encrypts data with AES-GCM locally using the DEK
- [ ] Wraps the DEK via Key Vault (`WrapKey`)
- [ ] Stores the wrapped (encrypted) DEK as blob metadata
- [ ] For decryption: retrieves wrapped DEK from blob metadata, unwraps via Key Vault, decrypts locally
- [ ] Stores the IV (initialization vector) alongside the blob (in metadata)
- [ ] Stores the vault key identifier in blob metadata (so you know which key to unwrap with)

### AES-GCM Encryption (scenario-specific)
- [ ] Uses `AesGcm` class from `System.Security.Cryptography` (not AES-CBC, AES-ECB)
- [ ] Generates a random nonce/IV for each encryption operation
- [ ] Nonce length is appropriate (12 bytes for GCM)
- [ ] Stores the authentication tag alongside the ciphertext

### Error Handling
- [ ] Catches `RequestFailedException` for blob storage errors
- [ ] Catches `RequestFailedException` for Key Vault errors (e.g., key disabled, key not found)
- [ ] Catches specific exceptions rather than generic `Exception`

## Async Implementation Quality
- [ ] Async implementation exists in a separate subdirectory
- [ ] Uses `*Async()` method variants with `await` for blob and Key Vault operations
- [ ] Does NOT call `.Result` or `.GetAwaiter().GetResult()` inside async code

## Comparison: Baseline vs With-Skills

| Criteria | Baseline | With Skills | Notes |
|----------|----------|-------------|-------|
| Correct SDKs (blob + keyvault-keys + identity) | | | Using keyvault-secrets instead of keys = wrong |
| `DefaultAzureCredential` shared | | | Separate credentials = works but wasteful |
| Uses Key Vault Keys (not Secrets) | | | Secrets = fundamentally wrong approach |
| `CryptographyClient` for wrap/unwrap | | | Manual RSA = reinventing the wheel |
| Envelope encryption (DEK/KEK) pattern | | | Encrypting with vault key directly = wrong |
| AES-GCM with random nonce | | | AES-CBC/ECB = weaker |
| Wrapped DEK stored as blob metadata | | | Stored in plaintext = security failure |
| Key identifier in blob metadata | | | Missing = can't decrypt later |
| Handles blob + vault exceptions | | | |
| Async implementation present | | | |
| Code compiles | | | |
| Overall quality | Low/Med/High | Low/Med/High | |
