# Meeting Recap - Structured Meeting Notes for Notion

Turn any meeting (prospect call, client sync, partnership discussion) into a structured Notion page with follow-up email and tracked action items.

**Approximate token count:** ~1,200 tokens

## Features

- Structured Notion page with consistent sections: participants, company context, needs, proposal, debrief, next steps
- Auto-drafted follow-up email ready to send
- Action items with assigned owners
- Optional pipeline/CRM database update
- Works with transcripts (paste or file path) or manual input

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@meeting-recap
```

## Configuration

Add to your project's `CLAUDE.md`:

```markdown
## Meeting Recap Config
- Notion parent page: `YOUR_PAGE_ID`
- Name: Your Name
- Title: Your Role
- Company: Your Company
- Pipeline DB: `YOUR_DB_ID` (optional)
```

## Usage

After a meeting:

```
> Create a meeting recap for my call with Jane Smith from Acme Corp today.
> We discussed their need for AI automation in their marketing workflow.
> They want a 3-day pilot at 500 EUR/day. Jane will confirm by Friday.
```

Or with a transcript:

```
> Create a meeting recap from this transcript: [paste or file path]
```

## Requirements

- [Notion MCP server](https://www.npmjs.com/package/@anthropic-ai/notion-mcp-server) configured in `.mcp.json`

## License

MIT
