---
name: repo-init-speckit-ralph
description: Set up the spec-kit Ralph loop (Rubiss-Projects/spec-kit-ralph) in the current repository.
disable-model-invocation: true
---

# repo-init-speckit-ralph

Install and configure the [Ralph loop](https://github.com/Rubiss-Projects/spec-kit-ralph) spec-kit extension in the repository you are called in. Ralph runs fresh agent iterations over a feature's `tasks.md`, committing one work unit per iteration, until every task is done. Idempotent: skip steps already satisfied. The extension's README is the source of truth for versions, flags, and config keys.

Ask every question as a **multiple choice**, as the `## Questions` section of [`../user-story-research/SKILL.md`](../user-story-research/SKILL.md) defines.

## Steps

1. **Check prerequisites.** Run `git rev-parse --show-toplevel`, `specify --version`, and `copilot --version`. Done when the repo root is known and both tools meet the README's prerequisites. Ask the user before installing a missing tool from its upstream docs; report what stays missing.
2. **Initialise spec-kit.** When `.specify/` is absent, run `specify init --here` with Copilot as the agent, taking the flags from `specify init --help`. Done when `.specify/integration.json` exists.
3. **Add the extension**: `specify extension add ralph`. When the registry lookup fails, install the latest release archive with `--from` as the README shows. Done when `specify extension list` shows Ralph Loop as enabled.
4. **Configure.** Read `.specify/extensions/ralph/ralph-config.yml` and ask the user in one form about each setting it holds, recommending its current value unless the README advises otherwise. Write the changed values back; personal overrides go to `ralph-config.local.yml`, which `git check-ignore` must confirm as ignored. Done when the config holds the chosen values and parses as YAML.
5. **Show** `git status` and the config, then tell the user:
   - Ralph runs every iteration unattended with permission prompts skipped (`--yolo` for Copilot), so run it only in a trusted working directory.
   - Start a loop with `/speckit.ralph.run` on a feature branch with a completed `tasks.md`.

   Leave committing to the user.
