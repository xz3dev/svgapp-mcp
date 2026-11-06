<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/svgapp-logo-dark.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/svgapp-logo-light.png">
    <img src="assets/svgapp-logo-light.png" width="96" alt="svgapp.ai logo">
  </picture>
</p>

# svgapp.ai MCP server and agent skills

[svgapp.ai](https://svgapp.ai) creates a brand mascot from a prompt, then keeps that character consistent across new poses, emotions and animations. This repository connects it to your coding agent. Claude Code, Cursor, Codex or Windsurf can create the mascot, generate a pose for each screen (onboarding, empty states, 404 pages, success messages) and put the files straight into your project.

- **Remote MCP server:** `https://web.svgapp.ai/mcp` (Streamable HTTP, OAuth sign-in, no API key)
- **Outputs:** PNG with transparent background, SVG (vectorized), transparent WebM animations
- **Registry:** [`io.github.Xyz3R/svgapp`](https://registry.modelcontextprotocol.io/v0/servers?search=io.github.Xyz3R/svgapp) on the official MCP registry

This repository contains only connection config and agent instructions. The server is hosted by svgapp.ai.

## Connect

You sign in with your svgapp.ai account in the browser; your client stores and refreshes the credentials. There is nothing to copy or paste. If you have never used svgapp, [sign in to the app](https://svgapp.ai/app/) once first so your account exists.

### Claude Code

```bash
claude mcp add --transport http svgapp https://web.svgapp.ai/mcp
```

Then run `/mcp` inside Claude Code, select `svgapp` and choose **Authenticate**. Add `--scope user` to use it in every project, or `--scope project` to share the entry with your team through `.mcp.json` (each teammate signs in with their own account).
[Full guide](https://svgapp.ai/blog/claude-code-mascot-svg/)

### Cursor

Add this to `.cursor/mcp.json` in your project, or `~/.cursor/mcp.json` for all projects:

```json
{
  "mcpServers": {
    "svgapp": {
      "url": "https://web.svgapp.ai/mcp"
    }
  }
}
```

Open **Settings → Tools & MCP** and click **Connect** next to `svgapp`.
[Full guide](https://svgapp.ai/blog/cursor-ai-mascot-design/)

### Codex

```bash
codex mcp add svgapp --url https://web.svgapp.ai/mcp
codex mcp login svgapp
```

Or add it to `~/.codex/config.toml`:

```toml
[mcp_servers.svgapp]
url = "https://web.svgapp.ai/mcp"
```

[Full guide](https://svgapp.ai/blog/codex-mascot-design/)

### Cline

Add the server to `cline_mcp_settings.json`, then authenticate it in Cline's MCP servers view:

```json
{
  "mcpServers": {
    "svgapp": {
      "type": "streamableHttp",
      "url": "https://web.svgapp.ai/mcp"
    }
  }
}
```

Agents can follow [`llms-install.md`](llms-install.md) to set this up for you.

### Windsurf and other clients

Any client that supports remote MCP servers with OAuth can connect to `https://web.svgapp.ai/mcp`. For Windsurf, add the server to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "svgapp": {
      "serverUrl": "https://web.svgapp.ai/mcp"
    }
  }
}
```

## Check the connection

Ask your agent:

> List my svgapp workspaces and mascots. Don't generate anything.

This calls `list_workspaces` and `list_mascots`, which only read data. A new account returns an empty mascot list.

## What you can ask for

- "Create a friendly mascot for this app that matches our brand colors."
- "Generate our mascot looking confused, for the 404 page, and add it to the not-found route."
- "Make an empty-state illustration for the projects list with our mascot holding an empty folder."
- "Animate the mascot waving for the onboarding welcome screen, as a loop."
- "Vectorize the celebrating pose and save it as an SVG in `src/assets/mascot/`."

## Tools

| Tool | What it does |
| --- | --- |
| `init_svgapp_config` | Creates `svgapp.conf.json` in your project with the default art style |
| `list_art_styles` | Lists the available art styles |
| `create_color_palette` | Saves your brand colors so every asset matches them |
| `create_mascot` | Creates a reusable mascot from a description |
| `list_mascots` | Lists the mascots in your workspace |
| `generate_mascot_pose` | Draws the same mascot in a new pose, emotion or UI state |
| `create_mascot_animation` | Animates a mascot, optionally between two poses, as transparent WebM |
| `generate_composition` / `create_generation` | Generates a standalone illustration (hero, feature, empty state, diagram) |
| `remove_image_background` | Makes an image transparent so it sits cleanly on any UI background |
| `vectorize_image` | Converts a finished image to SVG |
| `get_generation_status` / `get_generated_asset` / `list_recent_generations` | Tracks generations, which run asynchronously |
| `list_workspaces` | Lists your workspaces; every tool accepts an optional workspace |

Generations use your workspace's plan allowance. See [pricing](https://svgapp.ai/pricing/).

## Agent skills

The `skills/` folder has workflow instructions that help agents use the tools well:

| Skill | Use it to |
| --- | --- |
| [`create-mascot`](skills/create-mascot/SKILL.md) | Set up the project config, create a mascot and generate consistent poses |
| [`animate-mascot`](skills/animate-mascot/SKILL.md) | Turn a mascot or pose into a transparent animation |
| [`add-assets-to-app`](skills/add-assets-to-app/SKILL.md) | Download finished assets and use them in your UI |

Install them with the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add xz3dev/svgapp-mcp
```

Skills only add instructions. Connect the MCP server as shown above so the agent can use the tools.

### Cursor plugin

This repository is also a Cursor plugin (`.cursor-plugin/plugin.json`) that bundles the skills and the server config. For a local install, copy the whole directory to `~/.cursor/plugins/local/svgapp`, reload Cursor and enable **svgapp.ai** under **Customize**.

## Troubleshooting

- **Sign-in fails or loops:** remove any old `Authorization` header or `bearer_token_env_var` from your config. Earlier versions used static tokens, which no longer work. Then sign in again.
- **"No SVG App account found for this login":** sign in to [svgapp.ai/app](https://svgapp.ai/app/) once to finish setting up your account, then reconnect.
- **Plan or allowance errors:** the error names the workspace settings page where you can upgrade.

## Free tools (no account needed)

- [Free mascot generator](https://svgapp.ai/free-mascot-generator/)
- [Animated mascot library](https://svgapp.ai/library/): free for commercial use, no attribution
- [Palette generator](https://svgapp.ai/palette-generator/)
- [Style preset tool](https://svgapp.ai/style-preset-tool/)

## License

The configuration and skills in this repository are MIT licensed. Use of the svgapp.ai service is governed by the [terms](https://svgapp.ai/terms/).
