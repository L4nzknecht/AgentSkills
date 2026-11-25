# AgentSkills

My personal base of agent skills for GitHub Copilot: which skill packs I use, how to install them, and my own skills for setup and user-story work.

## Skill packs

| Pack | Source | Plugin | Marketplace |
|------|--------|--------|-------------|
| Matt Pocock skills (`/grill-me`, TDD, specs/tickets, code review, domain modelling, ...) | https://github.com/mattpocock/skills | `mattpocock-skills` | `mattpocock/skills` |
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
| [`init`](skills/init/SKILL.md) | One-time machine setup: installs the plugins above and this repo's `agent-skills` plugin. |
| [`repo-init`](skills/repo-init/SKILL.md) | Per-repo setup: tailors the general skills to the current repository (conventions, test/build commands, docs layout), adds MCP servers for tools its instruction files require, and sets up tracker access through an MCP server (Azure DevOps with browser sign-in or a PAT, Jira/Confluence via the Atlassian server), registered once in `~/.copilot/mcp-config.json`. |
| [`user-story-research`](skills/user-story-research/SKILL.md) | General skill: researches a user story from the issue tracker (Azure DevOps, Jira, ...) plus its knowledge base (wiki, Confluence), repo docs and code, and saves a brief in a configurable language to `docs\US-<ID>.md`. `repo-init` copies it into the repo as the personal, git-ignored `user-story-research-<repo>` with its configuration (tracker, organization, project, knowledge base, specifications, language) filled in. |
| [`user-story-review`](skills/user-story-review/SKILL.md) | General skill: reviews the implementation against `docs\US-<ID>.md` from `user-story-research` (every criterion, tests, scope creep), runs `ponytail-review` for bloat, duplication and simplifications, and writes the result in a `## Review` section of that file. |
| [`repo-init-speckit-ralph`](skills/repo-init-speckit-ralph/SKILL.md) | Per-repo setup of the [spec-kit Ralph loop](https://github.com/Rubiss-Projects/spec-kit-ralph): checks `specify`, initialises spec-kit, adds the `ralph` extension and writes its config (agent CLI, model, iterations, commit style). |

This repo is a plugin marketplace (`.github/plugin/marketplace.json`) with one plugin, `agent-skills` (`plugin.json`). The skills load in every session, including general chats:

```bash
copilot plugin marketplace add L4nzknecht/AgentSkills   # or a local checkout path: loads live, edits apply on the next session
copilot plugin install agent-skills@agent-skills
```

Then run `/init` once per machine for the other packs, and `/repo-init` in each repository.
