---
name: repo-init
description: Set up the current repository with AGENTS.md, a glossary, and a repo-specific user-story-research skill with tracker access.
disable-model-invocation: true
---

# repo-init

Record the repository's facts once, in `AGENTS.md`, and specialise `user-story-research` for it. Every file holds only verified facts about this repository.

## Steps

1. **Inspect** the repo. Done when each of these is known or confirmed absent: languages, package manager, build/test/lint commands, folder layout, `AGENTS.md`/`CLAUDE.md`/`CONTRIBUTING.md`/`GLOSSARY.md`/`docs/adr`, specification docs, CI config, issue tracker. Run each command before recording it.
2. **Write `AGENTS.md`**: create it or extend the existing one. It holds what the environment cannot tell an agent: the build, test, and lint commands plus the smallest-test command, conventions and layout not obvious from the code, and where specs, ADRs, and the glossary live. Done when every command in it has run successfully and every path in it exists.
3. **Glossary**: when the code or docs use domain terms, run the `domain-modeling` skill to create or update `GLOSSARY.md`. Done when it exists, or the repo is confirmed to have no domain terms.
4. **Generate `user-story-research-<repo>`** when the repo tracks user stories in an issue tracker; `<repo>` is the repository folder name in kebab-case.
   1. Confirm `~/.copilot/skills/user-story-research/` exists; otherwise tell the user to run `init` and skip to step 6.
   2. Ask the user (`ask_user`, one form) for every Configuration setting of the general skill (tracker, organization or site, project, knowledge base, specifications location and name, language), with values detected from `git remote`, CI config, and docs as defaults.
   3. Copy the general skill folder to `.github/skills/user-story-research-<repo>/`, set the frontmatter `name` to the folder name, and fill the Configuration table. The copy differs from the general skill only in these two places; re-run this step after the general skill changes.

   Done when the copy's name matches its folder and no `<unset>` remains. Only this skill is specialised; the plugin skills stay general.
5. **Tracker access**: follow [`tracker-azure-devops.md`](tracker-azure-devops.md) or [`tracker-jira.md`](tracker-jira.md) for the chosen tracker. Done when one story and one knowledge-base page read successfully through an MCP server.
6. **Show the diff** and wait for the user's confirmation. Commit only when asked.
