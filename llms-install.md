# Installing the svgapp.ai MCP server

These instructions are for AI agents (such as Cline) that set up this MCP server for a user.

svgapp.ai is a **remote** MCP server. There is nothing to clone, build or run locally, and no API key is needed. The user signs in with their svgapp.ai account through OAuth.

- **Endpoint:** `https://web.svgapp.ai/mcp`
- **Transport:** Streamable HTTP
- **Auth:** OAuth 2.1. An unauthenticated request returns 401 with `WWW-Authenticate: Bearer resource_metadata="https://web.svgapp.ai/.well-known/oauth-protected-resource/mcp"`, and the client runs the sign-in flow from there.

## Steps

1. Add the server to the MCP settings file. In Cline this is `cline_mcp_settings.json`. Merge into the existing `mcpServers` object and do not remove other servers:

   ```json
   {
     "mcpServers": {
       "svgapp": {
         "type": "streamableHttp",
         "url": "https://web.svgapp.ai/mcp",
         "disabled": false,
         "autoApprove": []
       }
     }
   }
   ```

   Do not add an `Authorization` header or ask the user for a token. Static tokens are no longer supported.

2. Ask the user to authenticate the `svgapp` server in the MCP servers view. A browser window opens where they sign in with their svgapp.ai account and approve access. The client stores and refreshes the credentials.

3. If the user has never used svgapp.ai, they must sign in once at https://svgapp.ai/app/ so their account exists. Otherwise tools return "No SVG App account found for this login".

4. Verify the connection by calling `list_workspaces` and `list_mascots`. Both only read data and do not use any generation allowance. A new account returns an empty mascot list.

## After installation

Before generating anything, read the workflow skills in this repository:

- `skills/create-mascot/SKILL.md`: project config (`svgapp.conf.json`), mascot creation and consistent poses
- `skills/animate-mascot/SKILL.md`: transparent WebM animations
- `skills/add-assets-to-app/SKILL.md`: downloading assets and using them in the UI

Generation tools are asynchronous and count against the user's plan. Poll `get_generation_status` with the returned `generationId` instead of starting a generation again.
