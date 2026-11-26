---
name: repo-init
description: Set up the current repository with AGENTS.md, the MCP tools its rules require, a glossary, and a repo-specific user-story-research skill with tracker access.
disable-model-invocation: true
---

# repo-init

Record the repository's facts once, in `AGENTS.md`, and specialise `user-story-research` for it. Every file holds only verified facts about this repository.

Ask every question as a **multiple choice**, as [`../questions.md`](../questions.md) defines.

## Steps

1. **Inspect** the repo. Done when each of these is known or confirmed absent: languages, package manager, build/test/lint commands, folder layout, `AGENTS.md`/`CLAUDE.md`/`CONTRIBUTING.md`/`GLOSSARY.md`/`docs/adr`, specification docs, CI config, issue tracker. Read the commands from their **source**: the CI config, the build files (`package.json` scripts, `*.csproj`/`*.sln`, `Makefile`, `pyproject.toml`), and the README; each command is a file:line, not a run.
2. **Required tools**: list every tool that an instruction file (`AGENTS.md`, `CLAUDE.md`, `.github/copilot-instructions.md`, `.github/instructions/`) tells the agent to invoke. For each tool this session lacks, follow [`required-tools.md`](required-tools.md). Done when every listed tool has answered one call.
3. **Write `AGENTS.md`**: create it or extend the existing one. It holds what the environment cannot tell an agent: the build, test, and lint commands plus the smallest-test command, conventions and layout not obvious from the code, and where specs, ADRs, and the glossary live. Done when every command in it traces to its source file:line and every path in it exists. Then compare `AGENTS.md` with the other always-loaded instruction files (`CLAUDE.md`, `.github/copilot-instructions.md`, `.github/instructions/`). Record each **duplicate** (the same fact in two files) as file:line pairs for step 9, and leave those other files as they are.
4. **Glossary**: when the code or docs use domain terms, run the `domain-modeling` skill to create or update `GLOSSARY.md`. Done when it exists, or the repo is confirmed to have no domain terms.
5. **Generate `user-story-research-<repo>`** when the repo tracks user stories in an issue tracker; `<repo>` is the main checkout's folder name in kebab-case (`AgentSkills` becomes `agent-skills`), the parent folder of `git rev-parse --path-format=absolute --git-common-dir`, so every worktree resolves the same name.
   1. Confirm `copilot plugin list` shows `agent-skills`; otherwise tell the user to run `init` and skip to step 6. The general skill is the `skills/user-story-research/` folder of that plugin; its path is in `copilot plugin list` (live plugins) or under `~/.copilot/installed-plugins/`.
   2. Ask the user, in one form, for every Configuration setting of the general skill: tracker, organization or site, project, knowledge base, specifications location and name, and language. Take the options from `git remote`, the CI config and the docs.
   3. Copy the general skill folder to `~/.copilot/skills/user-story-research-<repo>/` (user level, so every worktree of the repo sees it), set the frontmatter `name` to the folder name, delete the `disable-model-invocation` line so agents and `user-story-deliver` can call the copy, set the `description` to a model-facing trigger naming the repository (the skill loads in every repo): `"Research a user story of <repo> from <tracker> into docs/US-<ID>.md. Use when the user asks to research or prepare a <repo> story, or a skill needs its brief."`, and fill the Configuration table. Copy [`../questions.md`](../questions.md) into the copy's folder and point its `## Questions` link at `questions.md`, since the copy has no plugin folder around it. The copy differs from the general skill only in these five places; re-run this step after the general skill changes.

   Done when the copy's name matches its folder, its description names the repo, and no `<unset>` remains. Only this skill is specialised; the plugin skills stay general.
6. **Telekom**: ask whether this is a Telekom project, with options pre-selected from `git remote` hosts and organisations (`telekom.de`, `devops.telekom.de`) and the registry in `.npmrc`. Done when answered. Steps 7 and 8 use the answer.
7. **Tracker access**: Azure DevOps follows [`tracker-azure-devops.md`](tracker-azure-devops.md). Jira follows [`../user-story-research/tracker-jira.md`](../user-story-research/tracker-jira.md) (Telecontext), a Telekom-only path: for a non-Telekom Jira repo, record that no access path exists and ask the user how to connect. Done when one story and one knowledge-base page read successfully through an MCP server, or step 5 found no issue tracker.
8. **Telekom ODS** (Telekom projects only): when `package.json` depends on `@telekom-ods/react-ui-kit`, follow [`ods.md`](ods.md). Done when `get_context` answers through the `ods-suite` server, or the project is confirmed not to use ODS.
9. **Show the diff** and the duplicates (or `none`), and wait for the user's confirmation. Commit only when asked.
