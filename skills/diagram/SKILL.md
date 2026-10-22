---
name: diagram
description: Draw a diagram as one self-contained HTML page, save it to Diagram (diagram.la), and give the user a link to share that stays current as it changes. Use when the user asks for a diagram, chart, timeline, map or any other visual to show someone, or to change, restore or tidy up a diagram that is on Diagram.
---

# Diagram

Diagram saves the diagrams you draw as HTML and gives the user a permanent link. Edits add versions at the same link. The tools come from the Diagram MCP server (`https://diagram.la/mcp`). If they aren't connected, point the user to https://diagram.la/guides/how-to-make-diagrams-with-ai.

## When to use

- The user asks for a diagram, chart, timeline, map, floor plan, mind map or any other visual to show someone.
- The user asks to change, restore, move or delete a diagram that is on Diagram.
- You want to show how something works before you change it.

## Before the first diagram

- **In a repo** (you can read and write its files): if `diagrams/README.md` exists, read it and follow it. If it doesn't:
  1. Call `list_projects`.
  2. Pick the project whose slug matches the repo folder or that the user named. If neither is clear, ask, and suggest the folder name in lowercase with dashes.
  3. Call `setup_repo` with that project and write the files it returns.
  4. Create `diagrams/brand.md` from the app's own colors, fonts and logo.
- **In a chat**: ask once what their product looks like (a link to their site, brand colors or a screenshot is enough). With nothing to go on, use a clean neutral style.
- **Orgs**: new projects are personal. Pass `org` to `save_diagram` or `setup_repo` only when the user wants the project in their org. Call `list_orgs` to see which orgs they're in. First tell them that everyone in that org will see what's published there, and pass `user_confirmed_org: true` only after they agree.

## Make a diagram

1. **Pick the kind of diagram** that best shows what the user wants to see. Choose from all of them: timeline, Gantt chart, sequence, state machine, tree, data model, comparison, chart, map or floor plan, exploded view, mind map, flowchart, architecture. Don't default to boxes and arrows. A schedule reads best as a Gantt chart, numbers as a chart, and the parts of an object as an exploded view.
2. **Draw it as ONE complete HTML document** with everything inline: CSS, JavaScript and SVG.
   - Scripts from a CDN such as jsDelivr load, so Mermaid and chart libraries work.
   - Include a `<title>` and keep the file under 900KB.
   - It runs in a sandbox: no cookies, localStorage or forms.
3. **Make it look like the user's product**: their colors, fonts and light or dark look, from the repo or `diagrams/brand.md`. Make it readable:
   - A title and a one-line subtitle.
   - Text 14px or larger.
   - A few colors, each with a meaning.
   - When there are arrows, one direction and a label on each.
   - Full guide: https://diagram.la/docs/drawing
4. **Call `save_diagram`** with `html`, `project` and `kind` (the form of the diagram, for example `sequence`, `gantt` or `exploded_view`). Give the user the link straight away. It updates by itself as you work.
5. **Check your work.** The result includes a screenshot and layout warnings. Compare them with what the user asked for, and fix problems before saying the diagram is ready. `get_screenshot` shows the latest version (or any earlier one) at any time.

## Change a diagram

- Use `edit_diagram` with exact find-and-replace pieces of the current HTML. The link stays the same, and you don't resend the whole document. Use `save_diagram` with `id` only for a full rewrite.
- If you don't have the current HTML (a new conversation, or someone else may have changed it), read it first with `get_diagram`, so you never undo someone else's work:
  - With no options, it returns an outline of the diagram's text.
  - `find` returns the HTML around a piece of text.
  - `start` and `length` read part of the document.
  - `include_html` returns the whole document when it's small.
- Save after each change the user asks for. Don't keep diagrams in local files unless the user asks for a copy.
- To find a diagram, call `list_projects`, then `list_diagrams` with a project.

## Versions, moving and deleting

- Every save keeps a version. `list_versions` shows the history. `restore_version` brings an earlier version back as the newest one at the same link, and nothing is deleted.
- `move_diagram` moves a diagram to another project the user owns. The link stays the same, and who can see it follows the new project.
- `delete_diagram` moves a diagram to the owner's archive and its link stops opening. Confirm with the user first. They can restore it at https://diagram.la/archive.
- Only the project's owner moves or deletes. Anyone who can see a project can add diagrams and save new versions.

## Publishing and sharing

- In a project others can see (people added to it, or an org), a new diagram is a draft. Only its maker and the project's owner can see it.
- When the user is happy with a draft, ask whether to publish it, and tell them who will then see it (the save result says who). Call `publish_diagram` with `id` and `user_confirmed: true` only after they say yes. Never publish without asking.
- Publishing doesn't make a diagram public. Making it public (anyone with the link), adding people and moving a project into an org are the user's to do on the Diagram site, never through these tools. Give them the diagram's link, or `https://diagram.la/p/<project>/settings`.

## Never

Never put real keys, tokens, passwords or connection strings in a diagram. Use placeholders such as `<API_KEY>`. A save that contains one is refused.
