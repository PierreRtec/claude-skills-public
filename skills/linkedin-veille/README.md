# LinkedIn Veille - Trending Post Monitor & Content Intelligence

Monitor trending LinkedIn posts on any topic, filter noise (company pages, spam engagement, old posts), and get actionable content inspiration with one command.

**Approximate token count:** ~2,500 tokens

## Features

- Keyword-based LinkedIn post search via Unipile API
- 5-layer filtering: freshness (24h), language, company pages, engagement threshold, spam detection
- Post type classification (carousel, video, thread, story, list, infographic, news)
- Viral mechanism analysis ("why it works") for each post
- Actionable suggestions (copy format, test tool, write reaction)
- Deduplication against existing Notion entries
- Optional save to Notion with structured properties

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@linkedin-veille
```

## Configuration

Add to your `CLAUDE.md`:

```markdown
## LinkedIn Veille Config
- Unipile account ID: YOUR_ACCOUNT_ID
- Notion veille DB: YOUR_COLLECTION_ID (optional)
- Default keywords: ["AI agents", "claude code", "automation"]
- Default language filter: fr,en
- Default engagement threshold: 10
```

## Usage

```
> /linkedin-veille
> /linkedin-veille "AI agents" "MCP servers" min:20
> /linkedin-veille lang:en dry
```

## Requirements

- [Unipile MCP server](https://www.unipile.com/) configured (LinkedIn API access)
- [Notion MCP server](https://www.npmjs.com/package/@anthropic-ai/notion-mcp-server) (optional, for saving results)

## License

MIT
