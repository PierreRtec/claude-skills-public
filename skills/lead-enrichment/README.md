# Lead Enrichment - Prospect Data Enrichment for Notion CRM

Enrich prospect data from LinkedIn (via Unipile), email/phone APIs, and web search, then push structured results to your Notion CRM database.

**Approximate token count:** ~1,800 tokens

## Features

- Multi-source enrichment: LinkedIn profiles, professional emails, phone numbers, company data, web presence
- Configurable tool selection (free-only or full enrichment)
- ICP (Ideal Customer Profile) scoring with customizable criteria
- Batch enrichment with parallelized API calls
- Direct push to Notion CRM database
- Smart data merging (never overwrites better existing data)

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@lead-enrichment
```

## Configuration

Add to your project's `CLAUDE.md`:

```markdown
## Lead Enrichment Config

- Notion database ID: YOUR_NOTION_DB_ID
- Unipile account ID: YOUR_ACCOUNT_ID
- Unipile base URL: https://api1.unipile.com:13333

## ICP Scoring

- 5: CEO/VP at SMB (20-200 employees), target industry, France
- 4: Decision maker at SMB, non-target industry
- 3: Internal prescriber at interesting company
- 2: Enterprise or early startup
- 1: Competitor, freelancer, student
```

## Usage

```
> /enrich-leads
```

The skill will ask:

1. Which tools to use (Unipile only, + web search, + email API, or all)
2. Which leads to enrich (by status, score, or name)
3. Then enrich each lead and update Notion

## Requirements

- [Notion MCP server](https://www.npmjs.com/package/@anthropic-ai/notion-mcp-server) configured
- [Unipile MCP server](https://www.unipile.com/) configured
- (Optional) Email/phone enrichment API (Airscale, Hunter, Apollo, etc.)
- (Optional) Web search API (Exa, Perplexity, etc.)

## License

MIT
