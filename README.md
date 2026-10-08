# PageProofer MCP server

Read, fix and reply to [PageProofer](https://pageproofer.com) website feedback notes from GitHub Copilot, Claude Code, Cursor and other AI agents.

PageProofer collects visual feedback on websites: clients and teammates click an element, leave a note, and PageProofer captures the page, the element, the browser and any console errors. This MCP server lets your coding agent work through those notes. It finds the issue in your code, fixes it, comments on the note to say what changed, and moves the note to review.

This is a hosted (remote) server, so there's nothing to install:

- **URL:** `https://app.pageproofer.com/mcp`
- **Transport:** streamable HTTP
- **Auth:** `Authorization: Bearer YOUR_TOKEN`

This repository only documents the server. The server itself runs as part of PageProofer.

## What the agent can do

| Tool | What it does |
| --- | --- |
| `list-sites` | List the websites you can work on, with their domains, open note counts and the people on each site. |
| `list-notes` | List notes on a site, newest first. Filter by status, #tags, text, page, assignee or overdue. Open notes only by default. |
| `get-note` | Everything about one note: the feedback, page URL, the element it was pinned to (CSS selector and HTML), browser and window size, console errors, failed network requests, comments and attachments. |
| `get-attachment` | Fetch a screenshot or other attachment. Images come back as images the agent can see, text files as their contents. |
| `add-comment` | Comment on a note as you, for example to say what was fixed and in which commit. Can be internal (team only). |
| `update-note` | Change a note's status, priority, assignee or due date. |

Agents can't mark notes complete or closed. When an agent has fixed something, it moves the note to **in review** so a person can check it.

## Get a token

1. Sign in to PageProofer, open **My Settings** and go to the **AI agents** tab.
2. Name the token (for example "VS Code on my laptop") and click **Create token**.
3. Copy it straight away. It's only shown once.

AI agent access is included on every PageProofer plan, including the free trial. You can create a token per computer or tool and revoke any of them at any time.

## Configure

### GitHub Copilot in VS Code

Add this to `.vscode/mcp.json` in your project, or to your user MCP configuration. VS Code asks for the token the first time and stores it securely.

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "pageproofer-token",
      "description": "PageProofer token (My Settings > AI agents)",
      "password": true
    }
  ],
  "servers": {
    "pageproofer": {
      "type": "http",
      "url": "https://app.pageproofer.com/mcp",
      "headers": {
        "Authorization": "Bearer ${input:pageproofer-token}"
      }
    }
  }
}
```

Then open Copilot Chat in **Agent** mode and ask it about your notes.

### Claude Code

```
claude mcp add --transport http pageproofer https://app.pageproofer.com/mcp --header "Authorization: Bearer YOUR_TOKEN"
```

### Cursor

In `~/.cursor/mcp.json` or `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "pageproofer": {
      "url": "https://app.pageproofer.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

### Other agents

Any MCP client that supports remote servers over HTTP can connect with the URL `https://app.pageproofer.com/mcp` and the header `Authorization: Bearer YOUR_TOKEN`.

## Use it

Ask in plain language, from your project's folder:

- "What are the open notes on the Northwind site?"
- "Fix the open #checkout notes, then comment on each with what you changed."
- "What's wrong with note 12? Look at the screenshot."
- "Show me overdue notes assigned to me."

A typical loop is: the agent lists the open notes, reads one with its screenshot and console errors, finds the code behind the element, makes the fix, comments with the commit, and moves the note to in review.

## How it acts

- The agent acts as you. It sees the sites and notes you can see, nothing more.
- Its comments and changes show your name, marked "via AI agent".
- Notifications work as usual, so a comment from the agent notifies the people on the note.
- Note text and comments come from clients and site visitors. The server tells the agent to treat them as descriptions of a problem, not as instructions.

Treat your token like a password. Don't commit it to a repository. If it might have leaked, revoke it in **My Settings > AI agents**.

## Links

- Help: [Connect an AI agent](https://pageproofer.com/help/ai-agents/)
- [How to use the AI agent](https://pageproofer.com/help/how-to-use-the-ai-agent/)
- Support: support@pageproofer.com
