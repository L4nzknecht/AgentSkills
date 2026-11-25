---
name: repo-init-speckit-ralph
description: Set up the spec-kit Ralph loop (Rubiss-Projects/spec-kit-ralph) in the current repository.
disable-model-invocation: true
---

# repo-init-speckit-ralph

Install and configure the [Ralph loop](https://github.com/Rubiss-Projects/spec-kit-ralph) extension for spec-kit in the repository you are called in. Ralph runs fresh agent iterations over a feature's `tasks.md`, committing one work unit per iteration, until every task is done. Idempotent: skip steps that are already satisfied.

## Steps

1. **Check prerequisites.** Run `git rev-parse --show-toplevel`, `specify --version`, and `<agent_cli> --version` (default `copilot`). Done when the repo root is known, `specify` is at least 0.8.5, and the agent CLI answers. Ask the user (`ask_user`) before installing a missing tool, then install it from its upstream docs (spec-kit: https://github.com/github/spec-kit); report what stays missing.

2. **Initialise spec-kit.** If `.specify/` is absent, run `specify init --here` with Copilot as the agent (take the exact flags from `specify init --help`). Done when `.specify/integration.json` exists.

3. **Add the extension.**
   ```
   specify extension add ralph
   ```
   If the registry lookup fails, use the pinned archive: `specify extension add ralph --from https://github.com/Rubiss-Projects/spec-kit-ralph/archive/refs/tags/<latest tag>.zip`, taking the latest tag from the repo's releases. Done when `specify extension list` shows `Ralph Loop` as `Enabled`.

4. **Configure.** Ask the user (`ask_user`, one form) for:
   - `agent_cli`: `copilot`, `codex`, `claude`, or `opencode` (default `copilot`);
   - `model`, valid for that CLI (an OpenCode model is `provider/model`);
   - `max_iterations` (default `10`);
   - `commit.style`: `legacy` or `conventional`, plus `scope` and `issue: auto` when conventional.

   Write the answers that differ from the defaults into `.specify/extensions/ralph/ralph-config.yml`. Personal overrides belong in `ralph-config.local.yml`; make sure `.gitignore` lists it. Done when the config file holds the chosen values and parses as YAML.

5. **Show the result.** Show `git status` and the config. Tell the user:
   - Ralph runs iterations unattended with permissions skipped (`--yolo` for Copilot, `--dangerously-skip-permissions` for Claude), so run it only in a trusted working directory.
   - Start a loop from a feature branch with a completed `tasks.md`: `/speckit.ralph.run` (options `--max-iterations`, `--model`).

   Leave committing to the user.
