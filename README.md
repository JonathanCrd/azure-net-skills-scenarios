# azure-net-skills-scenarios

Private repo for measuring whether [microsoft/skills](https://github.com/microsoft/skills) .NET SDK skills improve LLM-generated code quality.

## What is this?

The `microsoft/skills` repo contains auto-generated "skills" — structured knowledge files that ground coding agents (Copilot, Claude, etc.) with Azure SDK best practices. This repo stores **test scenarios** that let us measure whether those skills actually move the needle.

## The Experiment

```
┌─────────────────────┐    ┌──────────────────────┐
│  1. BASELINE        │    │  2. WITH SKILLS       │
│                     │    │                       │
│  Give LLM a prompt  │    │  Install azure-sdk-   │
│  (no skills)        │    │  dotnet skill, then   │
│  → Record output    │    │  give same prompt     │
│                     │    │  → Record output      │
└────────┬────────────┘    └────────┬──────────────┘
         │                          │
         └──────────┬───────────────┘
                    ▼
           3. COMPARE & EVALUATE
           Did the skill help?
```

## Repo Structure

```
scenarios/
├── _template/                         # Copy this to create a new scenario
├── blob-storage-manager/              # Azure Blob Storage CRUD utility
│   ├── prompt.md                      # The prompt to give the LLM
│   ├── validation.md                  # Post-generation evaluation criteria
│   ├── baseline/                      # LLM output WITHOUT skills
│   │   ├── sync/
│   │   └── async/
│   └── with-skills/                   # LLM output WITH skills
│       ├── sync/
│       └── async/
├── keyvault-secret-config/
├── cosmos-todo-repository/
├── servicebus-order-processor/
├── appconfig-feature-flags/
├── storage-keyvault-encrypted-uploader/
├── identity-credential-chain/
└── blob-event-notifier/

docs/
└── evaluation-guide.md
```

Each scenario is self-contained: `prompt.md` is the input, `validation.md` has the evaluation criteria, and `baseline/` and `with-skills/` hold the LLM outputs (each split into `sync/` and `async/` subdirectories).

## Scenarios

| Scenario | Azure Services | Prompt | Validation |
|----------|---------------|--------|------------|
| blob-storage-manager | Blob Storage | [prompt](scenarios/blob-storage-manager/prompt.md) | [validation](scenarios/blob-storage-manager/validation.md) |
| keyvault-secret-config | Key Vault, Identity | [prompt](scenarios/keyvault-secret-config/prompt.md) | [validation](scenarios/keyvault-secret-config/validation.md) |
| cosmos-todo-repository | Cosmos DB | [prompt](scenarios/cosmos-todo-repository/prompt.md) | [validation](scenarios/cosmos-todo-repository/validation.md) |
| servicebus-order-processor | Service Bus | [prompt](scenarios/servicebus-order-processor/prompt.md) | [validation](scenarios/servicebus-order-processor/validation.md) |
| appconfig-feature-flags | App Configuration | [prompt](scenarios/appconfig-feature-flags/prompt.md) | [validation](scenarios/appconfig-feature-flags/validation.md) |
| storage-keyvault-encrypted-uploader | Blob Storage, Key Vault | [prompt](scenarios/storage-keyvault-encrypted-uploader/prompt.md) | [validation](scenarios/storage-keyvault-encrypted-uploader/validation.md) |
| identity-credential-chain | Identity | [prompt](scenarios/identity-credential-chain/prompt.md) | [validation](scenarios/identity-credential-chain/validation.md) |
| blob-event-notifier | Blob Storage, Event Grid | [prompt](scenarios/blob-event-notifier/prompt.md) | [validation](scenarios/blob-event-notifier/validation.md) |

## Quick Start

1. **Pick a scenario** from the table above (or create one using the [template](scenarios/_template/prompt.md))
2. **Run baseline** — give the prompt to Copilot/LLM without any skills installed. Save output into that scenario's `baseline/` directory.
3. **Install skills** — `npx skills add microsoft/skills --skill azure-sdk-dotnet`
4. **Run with skills** — give the same prompt in a fresh session. Save output into `with-skills/`.
5. **Validate** — use the scenario's `validation.md` to evaluate both outputs.
6. **Compare** — follow the full process in [docs/evaluation-guide.md](docs/evaluation-guide.md) to produce a scored comparison.

## Adding Scenarios

See [CONTRIBUTING.md](CONTRIBUTING.md) and [scenarios/README.md](scenarios/README.md).

## Related Repos

- [microsoft/skills](https://github.com/microsoft/skills) — The skills repo (source of truth for skill content)
- [Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) — Azure SDK for .NET
- [samvaity/azure-java-skills-scenarios](https://github.com/samvaity/azure-java-skills-scenarios) — The Java equivalent of this repo
