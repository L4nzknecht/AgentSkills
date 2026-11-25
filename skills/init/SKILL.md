---
name: init
description: Install my standard skill packs into GitHub Copilot as plugins.
disable-model-invocation: true
---

# init

Install every pack in the table as a Copilot plugin. Idempotent: skip packs already installed.

## Packs

| Plugin | Marketplace repo | Marketplace name |
|--------|------------------|------------------|
| `mattpocock-skills` | `mattpocock/skills` | `mattpocock` |
| `ponytail` | `DietrichGebert/ponytail` | `ponytail` |

Add new packs here; the steps read this table.

## Own skills

Every folder under `skills/` of the `L4nzknecht/AgentSkills` repo is a general skill, installed to `~/.copilot/skills/` (currently `init`, `repo-init`, `user-story-research`).

## Steps

1. Run `copilot plugin list` to find which table plugins are missing.
2. For each missing pack:
   ```
   copilot plugin marketplace add <marketplace repo>
   copilot plugin install <plugin>@<marketplace name>
   ```
   If the marketplace name is rejected, take the name from `copilot plugin marketplace list`.
3. Run `copilot plugin list` again. Done when every table plugin appears; retry or report each one that doesn't.
4. Install own skills: use this checkout if it is the AgentSkills repo, otherwise `gh repo clone L4nzknecht/AgentSkills` to a temp folder. Copy each `skills/<name>` folder to `~/.copilot/skills/<name>`, overwriting the general copy. Done when every folder has a `SKILL.md` at the target.
5. Report each pack and own skill as installed, already present, or failed. Tell the user to restart Copilot so the new skills load, and to run `repo-init` in each repository.
