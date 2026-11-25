---
name: init
description: Install my standard agent skill packs (Matt Pocock skills, Ponytail) into GitHub Copilot as plugins. Use for "init", "set up my skills", "install my skills" on a new machine.
---

# init

Install every pack in the list below as a Copilot plugin. Idempotent: skip what is already installed.

## Packs

| Plugin | Marketplace repo | Marketplace name |
|--------|------------------|------------------|
| `mattpocock-skills` | `mattpocock/skills` | `mattpocock` |
| `ponytail` | `DietrichGebert/ponytail` | `ponytail` |

Add new packs here; the steps below read this table.

## Steps

1. Run `copilot plugin list`. Note which plugins are already installed.
2. For each missing pack:
   ```
   copilot plugin marketplace add <marketplace repo>
   copilot plugin install <plugin>@<marketplace name>
   ```
   If the marketplace name is rejected, run `copilot plugin marketplace list` and use the name shown.
3. Run `copilot plugin list` again and confirm every plugin in the table is present.
4. Report installed / already present / failed. Tell the user to restart the Copilot session so new skills load.
5. Suggest `repo-init` for the current repository.

Don't modify anything else on the machine.
