# LinkedIn Post Notion - LinkedIn Draft Manager for Notion

Create and edit LinkedIn post drafts in a Notion Kanban database with pixel-perfect formatting, image preservation, and variant workflows.

**Approximate token count:** ~1,500 tokens

## Features

- Correct Notion block formatting (each line = separate block, empty blocks for spacing)
- Image-safe editing (never overwrites uploaded images)
- Variant workflow: generate in conversation, write only the chosen one
- Clean pages: raw post only, no meta-content or work headers
- Arrow-style lists for LinkedIn aesthetic
- Known error patterns documented with fixes

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@linkedin-post-notion
```

## Configuration

Add to your `CLAUDE.md`:

```markdown
## LinkedIn Post Config
- Notion data source: YOUR_COLLECTION_ID
- Connection name: LI@your-handle
- Default status: Draft
```

## Usage

```
> Write a LinkedIn post about why AI agents will replace SaaS dashboards. Push to Notion as draft.
```

The skill will:
1. Generate the post (or multiple variants if asked)
2. Let you pick the best version
3. Create a properly formatted Notion page in your LinkedIn Kanban

## Requirements

- [Notion MCP server](https://www.npmjs.com/package/@anthropic-ai/notion-mcp-server) configured in `.mcp.json`
- A Notion database with Status, Connection, and Name properties

## License

MIT
