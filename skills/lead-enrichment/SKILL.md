---
name: lead-enrichment
description: Enrich leads with LinkedIn profile data, professional emails, phone numbers, and company info via Unipile and web search. Push enriched data to a Notion CRM database.
user-invocable: true
license: MIT
compatibility: Requires Unipile MCP server (LinkedIn API). Notion MCP server for CRM push.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "🔍"
    homepage: https://github.com/PierreRtec/claude-skills-public
allowed-tools: Read Edit Write Glob Grep WebFetch WebSearch
---

# Lead Enrichment

Enrich prospect data from multiple sources (LinkedIn via Unipile, web search) and push structured results to your Notion CRM database.

## Configuration

Before first use, set these values in your project's CLAUDE.md:

```markdown
## Lead Enrichment Config

- Notion database ID: YOUR_DB_ID
- Unipile account ID: YOUR_ACCOUNT_ID
- Unipile base URL: https://api1.unipile.com:13333
- Scoring criteria: see "ICP Scoring" section below
```

## Available Tools

At launch, ask the user which tool(s) to use:

### 1. Unipile (MCP, always available)

- **What**: LinkedIn profile data (headline, location, followers, connections, email if public, company, network_distance)
- **How**: `GET /api/v1/users/{slug}` via the Unipile MCP server
- **Input**: LinkedIn slug OR provider_id
- **Free**: yes (included in Unipile subscription)
- **Best for**: basic LinkedIn data, checking connection degree

### 2. Email/Phone enrichment API (optional, configure your own)

- **What**: professional email, phone number, enriched profile
- **How**: REST API calls (Airscale, Hunter, Apollo, or any enrichment provider)
- **Input**: LinkedIn profile URL or first_name + last_name + company
- **Credits**: depends on provider
- **Best for**: contact information not available on LinkedIn

### 3. Web Search (optional)

- **What**: semantic web search, website scraping, non-LinkedIn info
- **How**: Exa, Perplexity, or any search API/MCP
- **Best for**: personal websites, articles, mentions, company summaries, non-LinkedIn info

## Notion CRM Fields

Configure your Notion database with these properties (adapt names to your setup):

| Notion Field     | Type         | Recommended Source               |
| ---------------- | ------------ | -------------------------------- |
| Name             | title        | Existing                         |
| Headline         | text         | Unipile / enrichment API         |
| LinkedIn         | url          | Existing or search               |
| Email            | email        | Enrichment API                   |
| Phone            | phone_number | Enrichment API                   |
| Company          | text         | Unipile / enrichment API         |
| Company Size     | text         | Company enrichment or web search |
| Lead Website     | url          | Web search                       |
| Company Website  | url          | Company enrichment or web search |
| Company LinkedIn | url          | Unipile / enrichment API         |
| Website Summary  | text         | Web search + scraping            |
| Score            | number       | Calculated (see ICP below)       |
| Notes            | text         | Enrichment synthesis             |
| Contact          | text         | Full name                        |
| Type             | select       | "Prospect"                       |
| Status           | select       | "First contact"                  |
| Follow-up Date   | date         | Today or D+2                     |
| Amount           | number       | If known                         |

## Enrichment Workflow

### Step 1: Choose tools

Ask: "Which tools do you want to use for enrichment?"

- `[1]` Unipile only (free, basic LinkedIn data)
- `[2]` Unipile + Web search (free, LinkedIn + web)
- `[3]` Unipile + Email/Phone API (LinkedIn + contact info, uses credits)
- `[4]` All (maximum enrichment)

### Step 2: Select leads

Ask which leads to enrich:

- "All leads with status 'First contact' and no email" (recommended)
- "Leads with Score >= X"
- "Specific lead: NAME"
- Custom list

### Step 3: Enrich each lead

For each lead, in order:

1. **Unipile** (if selected): GET /users/{slug} for headline, location, followers, public email, company
2. **Enrichment API /profile** (if selected): full enriched profile
3. **Enrichment API /email** (if selected): validated professional email
4. **Enrichment API /phone** (if selected): phone number
5. **Enrichment API /company** (if selected, company LinkedIn known): company profile, size, website
6. **Web search** (if selected): "NAME COMPANY" for personal site, articles, mentions
7. **Web scraping** on lead's site or company site (if found) for summary

### Step 4: ICP Scoring

Score 1-5 based on your configured Ideal Customer Profile. Default scoring template:

- **5**: Decision maker (CEO/VP/Director) + SMB 20-200 employees + target industry + target geography
- **4**: Decision maker + SMB but non-target industry, OR target company but not the direct decision maker
- **3**: Internal prescriber (digital lead, project manager) + interesting company
- **2**: Enterprise (long sales cycle) OR early-stage startup OR non-target industry
- **1**: Competitor, solo freelancer, student, outside target geography

Customize these criteria in your CLAUDE.md config.

### Step 5: Update Notion

For each enriched lead, update the Notion page with all collected data.
Display a summary table to the user.

## Important Rules

- **Check API credits** before launching a batch (if using paid enrichment)
- **Parallelize** Unipile/API/Web calls when possible (no dependency between them)
- **Do not overwrite** existing Notion data unless the new data is more complete
- **Never invent** LinkedIn URLs or email addresses; only use verified data from APIs
- **Respect rate limits** on all APIs
