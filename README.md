# Diagram MCP server

[Diagram](https://diagram.la) saves the diagrams your AI draws and gives you a link to share. Ask Cursor, Claude Code, Codex, Copilot, Claude or ChatGPT for a diagram in plain words; it draws one, saves it, and hands you a link. Ask for a change and the same link updates. Every version is kept.

- **One link per diagram.** It stays current as the diagram changes, so the copy in your pull request or doc is never stale.
- **Viewers need nothing.** No AI account and no editor, only the link.
- **Any kind, any look.** Diagrams are plain HTML: timelines, Gantt charts, sequence diagrams, state machines, data models, charts, maps and floor plans, exploded views, mind maps, flowcharts, architecture, or something custom. They're drawn in your product's colors and fonts.
- **Private by default.** A new diagram is private. In a project your team can see, it starts as a draft that only you and the project's owner see, until you say to publish it.
- **Free** for your first 5 diagrams. Nothing to install: it is a hosted server.

Server URL: `https://diagram.la/mcp` (Streamable HTTP, sign in with OAuth in the browser).

## Install

### Cursor

[Add to Cursor](https://diagram.la/start/cursor), or add this to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "diagram": {
      "url": "https://diagram.la/mcp"
    }
  }
}
```

Then open Cursor Settings → MCP and click Connect next to `diagram`.

This repo is also a Cursor plugin: it adds the server, a rule that tells the agent when to use it, and a skill for drawing diagrams that read well.

### Claude Code

```sh
claude mcp add -s user --transport http diagram https://diagram.la/mcp
```

Start Claude Code, type `/mcp`, pick `diagram`, and choose Authenticate.

### The Diagram skill (Claude Code, Codex, Cursor, Gemini CLI and other Agent Skills hosts)

```sh
npx skills add Automatique-Inc/diagram-mcp
```

It installs `skills/diagram`, the full drawing and publishing guidance, for the agents you pick. It doesn't add the server: connect that as above.

### Codex

```sh
codex mcp add diagram --url https://diagram.la/mcp
```

Or in `~/.codex/config.toml`:

```toml
[mcp_servers.diagram]
url = "https://diagram.la/mcp"
```

### VS Code with GitHub Copilot

Run **MCP: Add Server** from the Command Palette, choose HTTP, paste `https://diagram.la/mcp`, and choose Global. Switch Copilot Chat to Agent mode.

### Gemini CLI

```sh
gemini mcp add -s user --transport http diagram https://diagram.la/mcp
```

### Claude and ChatGPT

Add a custom connector with the URL `https://diagram.la/mcp`. Step-by-step for each app, and for Windsurf, Zed, Cline, JetBrains, OpenCode and others: [diagram.la/guides](https://diagram.la/guides/how-to-make-diagrams-with-ai).

## What to ask for

- "Make an architecture diagram of this repo and give me a link I can share."
- "Draw how a request flows through this app, from the browser to the database."
- "Show the data model as an entity diagram, from the schema in this repo."
- "Update the architecture diagram: we just added a queue and a worker."
- "Make a Gantt chart of this quarter's launch plan."
- "Draw an exploded view of this keyboard from its parts list."

## Tools

| Tool | What it does |
| --- | --- |
| `save_diagram` | Save an HTML diagram, with the kind of diagram it is, and return its permanent link. In a project others can see, new diagrams are drafts. Returns a screenshot and layout warnings. |
| `edit_diagram` | Change part of a diagram by exact find and replace; saves a new version at the same link. |
| `publish_diagram` | Publish a draft to the people on its project, only after the user says yes. It doesn't make the diagram public. |
| `get_diagram` | A diagram's details and an outline of its text, the HTML around a piece of text, a part of the HTML, or the whole document when it's small. |
| `get_screenshot` | A screenshot of a diagram with plain-language layout warnings. |
| `list_diagrams`, `list_projects`, `list_orgs` | What the user can see. |
| `list_versions`, `restore_version` | Version history, and bringing an earlier version back. |
| `move_diagram`, `delete_diagram` | Move between the user's projects; delete moves to an archive it can be restored from. |
| `setup_repo` | Links a repo to a project: returns `diagrams/README.md` and a block for `CLAUDE.md` or `AGENTS.md` that tell agents how its diagrams are kept. |

After each save the server returns a screenshot and layout warnings, so the agent checks its own work before telling you it is done.

## What's in this folder

| Path | What it is |
| --- | --- |
| `skills/diagram/SKILL.md` | Agent Skill: when to use Diagram, picking the kind of diagram, matching the user's brand, checking the screenshot, versions, and asking before publishing |
| `rules/diagram.mdc` | Cursor rule with the server's instructions |
| `mcp.json` | The server entry for Cursor |
| `.cursor-plugin/plugin.json` | Cursor plugin manifest |
| `server.json` | The Official MCP Registry listing (`la.diagram/diagram`) |

The source lives in the `plugin/` folder of the Diagram app repo, next to the MCP server it describes (`src/lib/mcp.ts`, `src/lib/mcp-instructions.ts`), and is copied to this repo on every change. `test/plugin.check.mjs` fails when a tool is added there without being added here. Pull requests here are overwritten by the next copy, so send changes to support@diagram.la.

## Safety

- Sharing is done by a person on the Diagram site, never by a tool: making a diagram public, adding people, or moving a project into an org. An agent that has been prompt-injected cannot expose your diagrams.
- Diagram HTML is treated as untrusted. It is only ever shown in a sandboxed frame.
- A save that contains a credential (an API key, token or connection string) is refused.
- Diagrams are encrypted at rest.

More: [security](https://diagram.la/docs/security) · [privacy](https://diagram.la/privacy) · [terms](https://diagram.la/terms)

## Links

- Site: https://diagram.la
- Docs: https://diagram.la/docs
- For agents: https://diagram.la/llms.txt
- Official MCP Registry: `la.diagram/diagram`
- Support: support@diagram.la

## License

The contents of this repository (the plugin manifest, rule, skill and docs) are MIT licensed. The Diagram service itself is hosted and not part of this repository.
