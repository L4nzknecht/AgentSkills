---
name: init
description: Install my skill packs and own skills into GitHub Copilot.
disable-model-invocation: true
---

# init

Install every pack in the table as a Copilot plugin, and every own skill into `~/.copilot/skills/`. Idempotent: skip what is already installed.

## Packs

| Plugin | Marketplace repo | Marketplace name |
|--------|------------------|------------------|
| `mattpocock-skills` | `mattpocock/skills` | `mattpocock` |
| `ponytail` | `DietrichGebert/ponytail` | `ponytail` |

Add new packs here; the steps read this table.

## Steps

1. Run `copilot plugin list` to find which table plugins are missing.
2. For each missing pack:
   ```
   copilot plugin marketplace add <marketplace repo>
   copilot plugin install <plugin>@<marketplace name>
   ```
   If the marketplace name is rejected, take the name from `copilot plugin marketplace list`.
3. Run `copilot plugin list` again. Done when every table plugin appears; retry or report each one that doesn't.
4. **Own skills** are the folders under `skills/` of the `L4nzknecht/AgentSkills` repo: use this checkout if it is that repo, otherwise `gh repo clone L4nzknecht/AgentSkills` to a temp folder. Copy each folder with all its files to `~/.copilot/skills/<name>`, replacing the installed copy. Done when every folder's `SKILL.md` exists at the target and its frontmatter parses as YAML.
5. Report each pack and own skill as installed, already present, or failed. Tell the user to restart Copilot so the new skills load, and to run `repo-init` in each repository.
