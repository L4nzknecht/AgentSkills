---
name: repo-init
description: Set up the current repository: AGENTS.md, glossary, and a repo-specific user-story-research skill with tracker access.
disable-model-invocation: true
---

# repo-init

Record repo facts once, in `AGENTS.md`, and generate the repo-specific `user-story-research-<repo>` skill.

## Steps

1. **Check init**: if `~/.copilot/skills/user-story-research/SKILL.md` is missing, tell the user to run `init` first and stop.
2. **Inspect** the repo. Done when each of these is known or confirmed absent: languages, package manager, build/test/lint commands, folder layout, `AGENTS.md`/`CLAUDE.md`/`CONTRIBUTING.md`/`GLOSSARY.md`/`docs/adr`, CI config, issue tracker (GitHub, Azure DevOps, ...). Run each command before recording it.
3. **Write `AGENTS.md`**: create it, or extend the existing one by appending or editing sections. It holds only what the environment can't tell the agent:
   - build, test, lint commands, plus the smallest-test command
   - conventions and layout not obvious from the code
   - where specs, ADRs and the glossary live
4. **Create `GLOSSARY.md`** when the repo has real domain terms.
5. **Generate `user-story-research-<repo>`** when the repo uses an issue tracker with user stories. `<repo>` is the repository folder name in kebab-case. Ask the user (`ask_user`) for the tracker (Azure DevOps, Jira, ...), organization or site, project, and knowledge base (Azure DevOps wiki, Confluence space, ...); propose values detected from `git remote`, CI config, or docs as defaults. Copy the general skill to `.github/skills/user-story-research-<repo>/SKILL.md`, set its frontmatter `name` to `user-story-research-<repo>`, and fill its Configuration table with the answers. Done when the copy's name matches its folder and no `<unset>` remains; an existing copy is updated in place. Only this skill is specialised; the Matt Pocock and Ponytail skills stay general.
6. **Set up tracker access** for the chosen tracker. Done when a read of one story through an MCP server succeeds. Tokens live in the user's environment, never in a file, chat, or commit. If story and knowledge-base reads already work through an available MCP server, finish this step.
   - **Jira / Confluence**: guide the user to the Atlassian MCP server and its sign-in, then confirm with a story read.
   - **Azure DevOps**: the local server accepts a PAT (the remote `https://mcp.dev.azure.com/<org>` endpoint uses Entra OAuth only):
     1. Verify `node --version` is 20 or newer.
     2. The user creates a PAT at `https://dev.azure.com/<org>/_usersSettings/tokens`, scoped to the organization with **Work Items: Read** and **Wiki: Read**, and sets it as the user environment variable `ADO_MCP_AUTH_TOKEN` themselves.
     3. Add the server to the repo's `.mcp.json` without any secret (the process inherits the variable):
        ```json
        { "mcpServers": { "azure-devops": { "type": "stdio", "command": "npx", "args": ["-y", "@azure-devops/mcp", "<org>", "--authentication", "envvar"] } } }
        ```
     4. The user restarts Copilot, then reads a work item of `<project>` to confirm.
   - If the Copilot app can't run local servers, say so and point to VS Code's `.vscode/mcp.json` with the same command.
7. **Show the diff** and wait for the user's confirmation. Commit only when asked.

Every file stays short and holds verified facts, never copies of upstream skill text.
