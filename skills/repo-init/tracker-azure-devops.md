# Tracker access: Azure DevOps

Use the local `@azure-devops/mcp` server, registered once per organization in the user config `~/.copilot/mcp-config.json` so every repository shares it. The remote `https://mcp.dev.azure.com/<org>` endpoint accepts Entra OAuth only.

1. When `~/.copilot/mcp-config.json` already has an `@azure-devops/mcp` server for `<org>`, go to step 4.
2. Verify `node --version` is 20 or newer.
3. Add the server under `mcpServers`, keeping existing entries:
   ```json
   "<ORG>-azure-devops": {
     "tools": ["*"],
     "type": "local",
     "command": "npx",
     "args": ["-y", "@azure-devops/mcp", "<org>", "--authentication", "interactive"]
   }
   ```
   `interactive` signs the user in through the browser on first use; the config holds no secret.
4. The user restarts Copilot and completes the browser sign-in, then a story of `<project>` and a page of the wiki are read to confirm.

**PAT fallback**, when browser sign-in is unavailable (headless or CI): the user creates a PAT at `https://dev.azure.com/<org>/_usersSettings/tokens` with **Work Items: Read** and **Wiki: Read**, sets it as the user environment variable `ADO_MCP_AUTH_TOKEN` themselves, and the args end in `--authentication envvar` instead. The token lives only in that variable.
