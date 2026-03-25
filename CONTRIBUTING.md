# Contributing to azure-net-skills-scenarios

## How to Add a Scenario

1. Copy the `scenarios/_template/` directory:
   ```bash
   cp -r scenarios/_template scenarios/my-new-scenario
   ```
2. Give it a descriptive kebab-case directory name (e.g., `cosmos-todo-repository`)
3. Replace `prompt.md` with your plain-language prompt (see guidelines below)
4. Replace `validation.md` with scenario-specific evaluation criteria
5. Submit a PR

## Naming Conventions

### Scenario Directories

Use kebab-case names that describe the task:
```
scenarios/blob-storage-manager/
scenarios/keyvault-secret-config/
scenarios/cosmos-todo-repository/
```

Cross-service scenarios should name both services:
```
scenarios/storage-keyvault-encrypted-uploader/
```

### Result Files

Save LLM outputs inside each scenario's `baseline/` or `with-skills/` directory. The LLM creates `sync/` and `async/` subdirectories within each:
```
scenarios/blob-storage-manager/baseline/sync/...
scenarios/blob-storage-manager/baseline/async/...
scenarios/blob-storage-manager/with-skills/sync/...
scenarios/blob-storage-manager/with-skills/async/...
```

## Prompt Guidelines

Each `prompt.md` should be a plain natural-language request — the kind of thing you'd type into Copilot Chat or Claude. It should:

- Be **self-contained** — the LLM should need no external context
- Ask for a **.NET 8 console application** with 2–4 C# classes
- Target **1–2 Azure services**
- Specify class names, methods, and behavior explicitly
- Mention authentication approach (`DefaultAzureCredential`)
- Specify .NET version and project type
- **Not** include evaluation criteria (those live in [docs/evaluation-guide.md](docs/evaluation-guide.md))

## Workflow

1. **Write the scenario** — create the directory with `prompt.md` and `validation.md`
2. **Run baseline** (no skills) — save output in the scenario's `baseline/` directory
3. **Run with skills** — save output in the scenario's `with-skills/` directory
4. **Validate** — use the scenario's `validation.md` to evaluate each output
5. **Compare** — follow [docs/evaluation-guide.md](docs/evaluation-guide.md) for the full scoring process

## Scenario Quality Checklist

Before submitting a scenario, ensure:

- [ ] The prompt is clear and self-contained (someone unfamiliar can run it)
- [ ] The prompt asks for something non-trivial (multiple classes, realistic task)
- [ ] The scenario tests SDK patterns where skills should make a measurable difference
- [ ] Cross-service scenarios name all services involved
- [ ] The `validation.md` includes automated checks and SDK quality criteria

## Questions?

Reach out to Jonathan in the team channel.
