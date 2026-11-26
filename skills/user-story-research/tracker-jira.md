# Tracker access: Jira and Confluence

Jira and Confluence are read through **Telecontext**, an HTTP MCP server registered once in `~/.copilot/mcp-config.json`. Every call is read-only.

## Connection

Setup guide: [Telecontext with the Copilot CLI](https://docs.devops.telekom.de/docs/AI/Telecontext/setup/github-copilot#copilot-cli).

The agent can do the first two steps; the user does the rest, because they need the user's browser and personal API tokens. The agent hands the user steps 3 and 4 as a checklist and waits.

1. **Agent**: done when `Telecontext-search_tools` answers one call; then skip to step 5.
2. **Agent**: when the server is missing, add it under `mcpServers` in `~/.copilot/mcp-config.json`, keeping existing entries and holding no secret. Use `https://telecontext.telekom.net/mcp` from the internet, or `https://telecontext.telekom.de/mcp` on the internal DTAG network (Nucleus/DevClient):
   ```json
   "Telecontext": {
     "type": "http",
     "tools": ["*"],
     "url": "https://telecontext.telekom.net/mcp"
   }
   ```
   Every Telecontext tool loads on demand, so no tool filter is needed. A fixed tool set is optional: the user generates a filtered URL on the Teleconsole [MCP Tools](https://teleconsole.telecontext.telekom.net/mcp-tools) page.
3. **User**: restarts Copilot and authorises Telecontext in the browser (`Authorise Telecontext`, then `Allow Access`).
4. **User**: connects Jira and Confluence on the Teleconsole [Authorization Services](https://teleconsole.telecontext.telekom.net/authorization-services) page with personal API tokens. Without this connection the Jira and Confluence tools return no data.
5. **Agent**: reads one story of the Project and one Confluence page of the knowledge base. Done when both return content.

## Reading

Tools are reached through the meta tools, so the agent never guesses a tool name.

1. Find a tool with `Telecontext-search_tools`: `jira_get_issue`, `jira_search`, `confluence_search`, `confluence_get_page`.
2. Call it with `Telecontext-call_readonly_tool`.
3. Fetch the story with `jira_get_issue`: `fields: "*all"`, `expand: "renderedFields,changelog"`, `comment_limit: 100`, `update_history: false`.

Read from the issue: title, status, assignee, priority, story points, epic or parent, description, acceptance criteria (custom field `Acceptance Criteria`), decisions and open questions from comments and changelog, linked issues, subtasks, and every issue key mentioned in the text.

## Keys

Accept `123` and `PROJ-123` and normalise both to `<Project key>-123`.

## Bounds

- Fetch linked and mentioned stories in parallel, at most five. Use one bounded JQL search only when the links look incomplete.
- Search Confluence in this order and stop once the governing specification is found: URLs referenced by Jira, the story key, the exact story title, the story's main business objects. Fetch at most three pages.
