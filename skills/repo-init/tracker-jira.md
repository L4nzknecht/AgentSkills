# Tracker access: Jira and Confluence

1. Guide the user to the Atlassian MCP server (https://github.com/atlassian/atlassian-mcp-server) and its sign-in; the user completes the sign-in themselves.
2. When `~/.copilot/mcp-config.json` has no Atlassian server yet, add one under `mcpServers` as its documentation shows, keeping existing entries and holding no secret.
3. The user restarts Copilot, then a story of the project and a page of the Confluence space are read to confirm.

This path is unverified; report each step that differs from the documentation.
