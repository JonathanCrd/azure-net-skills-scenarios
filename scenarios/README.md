# Scenarios

Each subdirectory here is a self-contained test scenario. It contains a `prompt.md` (the plain-language request to give an LLM) and two output directories for recording the results.

## Directory Layout

```
scenarios/
├── _template/                         # Copy this directory to create a new scenario
│   ├── prompt.md
│   ├── validation.md
│   ├── baseline/
│   └── with-skills/
├── blob-storage-manager/
│   ├── prompt.md                      # The prompt — paste this into the LLM
│   ├── validation.md                  # Evaluation criteria for this scenario
│   ├── baseline/                      # Save LLM output here (no skills)
│   │   ├── sync/
│   │   └── async/
│   └── with-skills/                   # Save LLM output here (with skills)
│       ├── sync/
│       └── async/
├── keyvault-secret-config/
├── cosmos-todo-repository/
├── servicebus-order-processor/
├── appconfig-feature-flags/
├── storage-keyvault-encrypted-uploader/
├── identity-credential-chain/
└── blob-event-notifier/
```

## How to Use

1. Open a scenario's `prompt.md` and copy the full text.
2. Paste it into your LLM / coding agent (Copilot Chat, Claude, etc.).
3. Save the output into `baseline/` or `with-skills/` depending on the run (the LLM will create `sync/` and `async/` subdirectories).
4. Use the scenario's `validation.md` to evaluate the generated code.

See [docs/evaluation-guide.md](../docs/evaluation-guide.md) for the full step-by-step evaluation process.

## Creating a New Scenario

```bash
cp -r _template my-new-scenario
```

Then replace `my-new-scenario/prompt.md` with your plain-language prompt and `my-new-scenario/validation.md` with scenario-specific evaluation criteria.

### Writing Good Prompts

- **Self-contained** — no external context needed; the prompt is everything the LLM sees.
- **Specific** — name the classes, methods, and services. Don't say "do some blob stuff".
- **Realistic** — a task a real .NET developer might ask a coding agent to do.
- **Small project scope** — target 2–4 C# classes, 1–2 Azure services, .NET 8 console app. This is complex enough to be meaningful but small enough to evaluate by reading the output.
- **No evaluation criteria in the prompt** — the prompt is what the LLM sees. Evaluation criteria live in the scenario's `validation.md` and in [docs/evaluation-guide.md](../docs/evaluation-guide.md).

### Cross-Service Scenarios

Cross-service scenarios (e.g., Key Vault + Storage, Identity + multiple services) are especially valuable because they test whether the LLM can correctly combine multiple Azure SDKs, share credentials, and handle different exception types.
