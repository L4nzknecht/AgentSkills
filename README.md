# AgentSkills

My personal base of agent skills for GitHub Copilot: which skill packs I use, how to install them, and two skills of my own that automate setup.

## Skill packs

| Pack | Source | Plugin | Marketplace |
|------|--------|--------|-------------|
| Matt Pocock skills (grilling, TDD, specs/tickets, code review, domain modelling, ...) | https://github.com/mattpocock/skills | `mattpocock-skills` | `mattpocock/skills` |
| Ponytail (lazy senior dev mode: smallest change that solves the task) | https://github.com/DietrichGebert/ponytail | `ponytail` | `DietrichGebert/ponytail` |

Full, current skill lists live in each upstream repo (`skills/` folder).

## Install as plugins (GitHub Copilot app / CLI)

```bash
copilot plugin marketplace add mattpocock/skills
copilot plugin install mattpocock-skills@mattpocock

copilot plugin marketplace add DietrichGebert/ponytail
copilot plugin install ponytail@ponytail
```

Check with `copilot plugin list`. Update with `copilot plugin update <name>`.
Plugins are installed to `~/.copilot/installed-plugins/`. The app also lists them under **Customize > Installed**.

If a marketplace name differs, run `copilot plugin marketplace list` and use that name after the `@`.

## My own skills (this repo)

| Skill | Purpose |
|-------|---------|
| [`init`](skills/init/SKILL.md) | One-time machine setup: installs all the plugins above into GitHub Copilot. |
| [`repo-init`](skills/repo-init/SKILL.md) | Per-repo setup: tailors the general skills to the current repository (conventions, test/build commands, docs layout). |

Install them by copying into your personal skills folder:

```powershell
Copy-Item -Recurse skills\init, skills\repo-init $HOME\.copilot\skills\
```

Then in any session say "init" (machine setup) or "repo-init" (in a repo).
