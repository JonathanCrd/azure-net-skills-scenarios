> **IMPORTANT:**
> - DO NOT look at any files in this repository in the process of following this prompt, except for any skill files located in the `/skills` directory. In particular, do NOT read `validation.md` in this scenario directory.
> - Write all output to the appropriate subfolder of this scenario's directory. If you have Azure SDK skills installed, write to `with-skills/`. If you do not, write to `baseline/`. If you are unsure which applies, ask the user before proceeding.
> - Provide **two complete, separate implementations** of the connectivity tester — one synchronous and one asynchronous — in separate subdirectories (`sync/` and `async/`) under the output directory.

Create a small .NET 8 console application that demonstrates how to correctly build Azure credential chains tailored to different deployment environments — local development, CI/CD pipelines, and production.

The project needs:

- A **credential factory class** that builds the appropriate Azure credential for each environment. For local development, it should chain together credentials that work from developer tools (Azure CLI, Visual Studio, etc.). For CI pipelines, it should support credentials sourced from pipeline environment variables or Azure Pipelines service connections. For production, it should prefer managed identity (supporting both system-assigned and user-assigned, where the user-assigned identity's client ID comes from an environment variable), with workload identity as a fallback for Kubernetes scenarios. The factory should also support enabling Continuous Access Evaluation (CAE) on the token requests, which lets Azure revoke tokens mid-session for security events.

- An **environment detector class** that auto-detects which environment the app is running in by probing for well-known environment variables (e.g., CI pipeline workspace variables, managed identity endpoint availability). It should classify the environment as dev, CI, or production.

- A **connectivity tester class** (both sync and async versions) that verifies a credential works by requesting a token for a given Azure scope. It should print success/failure, the token's expiry time, and whether the token request was made with CAE enabled. It should handle and report the specific failure reason if authentication fails (expired credential, wrong tenant, no identity available, etc.) rather than just printing a generic error.

- A **Program.cs** that detects the current environment, builds the right credential, and runs the connectivity test against Azure Resource Manager using the sync tester first, then repeats with the async tester. Print the detected environment, the selected credential strategy, and the test results from both.

Include a complete `.csproj` with the necessary Azure SDK NuGet dependencies.
