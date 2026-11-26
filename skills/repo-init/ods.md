# ODS Suite MCP

The Suite MCP carries the usage guidance itself: server instructions, prompt templates, playbooks, and `get_skill`. The repo only needs the server, a version pin, and one line in `AGENTS.md`. Setup reference: <https://ods.trap.ng.telekom.net/?path=/docs/ods-suite-mcp-%F0%9F%94%A5-new-setup--docs> (Telekom SSO).

1. **Registry access (user)**: the user sets `NPM_TOKEN` and the project root has an `.npmrc` for the `@telekom-ods` scope (Artifactory `one-design-system-npm`), as the "Get started as a developer" guide describes. The agent checks the `.npmrc` and asks the user to set the token when `npm install` fails to authenticate.
2. **Install** `@telekom-ods/suite-mcp` as a pinned `devDependency` and run `npm install`.
3. **Server entry**, committed in `.vscode/mcp.json` (Copilot) next to any `.mcp.json` (Claude Code) the repo already has:
   ```json
   {
     "servers": {
       "ods-suite": {
         "type": "stdio",
         "command": "node",
         "args": ["./node_modules/@telekom-ods/suite-mcp/bin/suite-mcp.mjs"]
       }
     }
   }
   ```
   Name it `ods-suite`; `ods` is the legacy `@telekom-ods/cli` server.
4. **Pin** the SDK version in a committed `ods.json` at the repo root: `{ "platforms": { "react": "<installed version>" }, "default": "react" }`.
5. **`AGENTS.md`** gets one line: UI work starts from the `ods-suite` prompt templates (`/react-implement-screen`, `/react-implement-component`, `/react-rework-component`, `/react-migrate-deprecated-component`, or `list_prompt_templates`), never from component search alone.
6. **Verify**: ask the agent to call `get_context`. Done when it names the platform, the version and the source of that version; run `check_project_setup` when components render unstyled.
