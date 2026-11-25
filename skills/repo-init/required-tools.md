# Required tools

An instruction file can demand a tool by name, often one it says to invoke first. If that tool is missing, every agent in the repo is blocked on any request the rule covers. The fix is to add the MCP server that provides the tool. Leave the rule as it is.

1. **Find the provider.** Look the tool up in the table. Otherwise search for its name together with "MCP server" and confirm the provider in that server's own documentation.

   | Rule or tool | Provider | Command |
   |---|---|---|
   | `@azure Rule` lines, `azmcp_*` (for example `azmcp_bestpractices_get`) | Azure MCP Server, written by the GitHub Copilot for Azure extension | `npx -y @azure/mcp@latest server start`, sign-in through `az login` |

2. **Register the provider** under `mcpServers` in `~/.copilot/mcp-config.json`. Keep the existing entries, and put no secrets in the file:
   ```json
   "azure-mcp": { "type": "local", "command": "npx", "args": ["-y", "@azure/mcp@latest", "server", "start"], "tools": ["*"] }
   ```
3. **Confirm.** The user restarts Copilot and completes the provider's sign-in, then the tool is called once. The session prefixes tool names with the server name (for example `azure-mcp-bestpractices`). The tool that matches the rule's purpose satisfies it.

The step is done when every required tool has made one successful call. If the user declines a provider, report the rule that stays unsatisfied.
