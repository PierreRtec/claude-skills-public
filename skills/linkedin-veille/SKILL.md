---
name: linkedin-veille
description: Use when the user wants to find trending LinkedIn posts to get inspiration, find content to replicate, or monitor what performs well on LinkedIn around specific topics. Also use when the user mentions "linkedin veille", "linkedin posts", "post inspiration", "what's trending on linkedin", "content watch".
user-invocable: true
license: MIT
compatibility: Requires Unipile MCP server (LinkedIn API). Notion MCP server optional for saving results.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "📡"
    homepage: https://github.com/PierreRtec/claude-skills-public
allowed-tools: Read Edit Write Glob Grep
---

# LinkedIn Veille

Search and analyze trending LinkedIn posts on given keywords, filter noise, and present the best posts with actionable analysis.

## Prerequisites

- **Unipile MCP server** configured (LinkedIn API access)
- **Notion MCP server** configured (optional, for saving results)

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

## Workflow

```
Launch -> Define keywords -> Search via Unipile -> Filter -> Enrich (comments) -> Analyze -> Report -> User picks -> Save to Notion
```

## Step 1: Keywords

**Ask the user** or use configured defaults.
- Search params: `date_posted: "past_week"`, `sort_by: "date"`, `limit: 50` per keyword
- Unipile only supports `past_week` as minimum date range. Freshness filtering is done in post-processing (Step 3).

## Step 2: Search via Unipile

```
POST /api/v1/linkedin/search?account_id={ACCOUNT_ID}
Body: {
  "api": "classic",
  "category": "posts",
  "keywords": "<keyword>",
  "date_posted": "past_week",
  "sort_by": "date",
  "limit": 50
}
```

Response: `items[]` with `social_id`, `url`, `text`, `author` or `author_details`.

## Step 3: Filters (CRITICAL)

Apply in this order:

### 3a. Freshness filter (MANDATORY)
- Keep only posts less than 24h old (compare `parsed_datetime` with `datetime.now()`)
- Without this, week-old posts pollute results

### 3b. Language filter
- Keep only posts in configured languages (default: French + English)
- Detection: look for indicator words
  - FR: "le", "la", "les", "des", "est", "pour", "avec", "dans"
  - EN: "the", "is", "are", "for", "with", "that", "this", "how"
  - No clear match: **skip**

### 3c. Author filter: no company pages
- If author is a company account: **skip**
- Detection: no first/last name, or headline contains "Official", "Company Page"
- **Exceptions**: major tech/AI/VC companies (Anthropic, OpenAI, Google DeepMind, Microsoft, Meta AI, Vercel, Supabase, Notion, GitHub, Y Combinator, etc.)

### 3d. Minimum engagement
- Fetch comments via `GET /posts/{social_id}/comments`
- Threshold: keep posts with >= configured minimum comments (default: 10)

### 3e. Fake engagement / AI spam detection
- Flag posts where > 60% of comments are generic:
  - "Great post", "Thanks for sharing", "Very insightful", "Love this"
  - Comments < 15 characters
  - Comments starting with a lone emoji
- Mark as "suspect engagement" (don't skip, but flag)

## Step 4: Analyze each post

For each post passing filters, generate:

1. **Author**: name + headline
2. **Text**: first 200 characters
3. **Engagement**: comment count (+ flag if suspect)
4. **URL**: direct link
5. **Post type**: detect among:
   - `carousel`: "swipe", "slide", multiple images, numbered structure
   - `video`: video link or mention
   - `infographic`: image + comparison/guide/list structure
   - `thread`: long text > 500 chars, paragraph structure
   - `list`: numbered/bulleted enumeration, "X things", "X tools"
   - `story`: personal narrative, "I did", "I built", "I tested"
   - `news`: announcement, "just launched", "just released"
6. **Why it works**: 1 sentence on the viral mechanism (hook, format, timing, topic)
7. **Possible action**: what to do with it (copy format, test tool, write reaction post)

## Step 5: Report

```
## LinkedIn Veille - [DATE]
Keywords: [list]
Posts analyzed: X | Retained: Y | Filtered: Z

### Top posts

**1. [Author] - [Type]** (XX comments)
> [First 200 chars]
URL: [link]
Why it works: [1 sentence]
Action: [what to do]

**2. [Author] - [Type]** (XX comments) [suspect engagement flag if applicable]
> [First 200 chars]
...

### Content ideas to explore
- [idea 1 inspired by post X]
- [idea 2]

### Stats
- Posts scraped: N
- Language filtered: -N
- Company filtered: -N
- Engagement filtered: -N
- Suspect engagement: N
```

## Step 6: Deduplication (before saving)

If Notion DB is configured:
- Fetch recent entries from the veille DB
- Compare LinkedIn URLs
- If a post already exists: mark as "already saved" and exclude from save prompt
- Show in report: "X posts already saved (filtered)"

## Step 7: Save to Notion

User indicates which posts to save (by number). For each selected post:

| Property | Value |
|---|---|
| Title | "[Author] - [short post title]" |
| Source | "LinkedIn" |
| Type | detected type (carousel, video, thread...) |
| Account | author name |
| Keywords | search keyword used |
| Engagement | comment count |
| Summary | first 200 chars |
| URL | LinkedIn link |
| Actionable | "yes" if action identified |
| Notes | "why it works" analysis + possible action |
| Date | today's date |
| Priority | High if > 50 comments, Medium if 20-50, Low if < 20 |

## Rate limiting

- 3-5 seconds between each API call
- If many posts (>30), process in batches of 10
- Timeout: 30 seconds max per call

## Arguments

- Custom keywords: `/linkedin-veille "n8n" "AI agents" "indie hacker"`
- Engagement threshold: `/linkedin-veille min:20` (default: 10)
- Dry mode: `/linkedin-veille dry` (no Notion push)
- Single language: `/linkedin-veille lang:fr` (French only)
