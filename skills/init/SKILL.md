---
name: init
description: Install my skill packs, including my own agent-skills plugin, into GitHub Copilot.
disable-model-invocation: true
---

# init

Install every pack in the table as a Copilot plugin. Idempotent: skip what is already installed.

## Packs

| Plugin | Marketplace repo | Marketplace name |
|--------|------------------|------------------|
| `agent-skills` | `L4nzknecht/AgentSkills` (or the path of a local checkout, which loads live) | `agent-skills` |
| `mattpocock-skills` | `mattpocock/skills` | `mattpocock` |
| `ponytail` | `DietrichGebert/ponytail` | `ponytail` |

<!-- Add new packs to this table; the steps read it. -->

## Steps

1. Run `copilot plugin list` to find which table plugins are missing.
2. For each missing pack:
   ```
   copilot plugin marketplace add <marketplace repo>
   copilot plugin install <plugin>@<marketplace name>
   ```
   If the marketplace name is rejected, take the name from `copilot plugin marketplace list`.
3. Run `copilot plugin list` again. Done when every table plugin appears; retry or report each one that doesn't.
4. Report each pack as installed, already present, or failed. Tell the user to restart Copilot so the new skills load, and to run `repo-init` in each repository.
