---
name: linkedin-post-notion
description: Use when creating or editing a LinkedIn post draft in a Notion Kanban database. Handles correct block formatting, empty lines, image preservation, and property setup.
user-invocable: true
license: MIT
compatibility: Requires Notion MCP server configured with a LinkedIn posts database.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "✍️"
    homepage: https://github.com/PierreRtec/claude-skills-public
allowed-tools: Read Edit Write Glob Grep
---

# LinkedIn Post Notion

Create or edit LinkedIn post drafts in a Notion Kanban database with correct formatting.

## Configuration

Set in your `CLAUDE.md`:
```markdown
## LinkedIn Post Config
- Notion data source: YOUR_COLLECTION_ID
- Connection name: LI@your-handle
- Default status: Draft
```

## Notion Block Formatting (CRITICAL)

### Blocks

Each line of the post = a separate Notion text block.
Empty lines between paragraphs = `<empty-block/>` on its own line.

```
First line of the post.
<empty-block/>
Second paragraph line 1.
Second paragraph line 2.
<empty-block/>
Third paragraph.
```

### Newlines

Line breaks in `content` or `new_str` parameter must be REAL line breaks in the JSON string.
NEVER use literal `\n` in the string. It renders everything as a single text block.

### Lists

Use `->` (arrow) for list items, NOT `-` or `*`:

```
-> Item 1
-> Item 2
```

## Image handling

The user uploads images themselves in Notion (drag & drop).

**NEVER touch an existing image block.**

If the page already has an image:
- Use `update_content` (NOT `replace_content`)
- Target only the text to modify

If the page is empty (creation):
- `replace_content` is OK (no image to preserve)

## Workflow

### Creating a new post

1. `notion-create-pages` with parent = your data source ID
2. Properties: `Name`, `Status` (default), `Connection`
3. Content: the post formatted per rules above

### Editing an existing post

1. `notion-fetch` to read current content
2. Check if an image exists (`![](...)` block)
3. If image: `update_content` with `old_str` / `new_str`
4. If no image: `replace_content` is OK

### Variants

When the user asks for multiple variants:
- Generate them in conversation (not in Notion)
- User picks one
- Write ONLY the chosen variant to Notion
- No meta-content ("VARIANT 1", "Notes", "Angle", etc.)

## Page content rules

The page = the raw post, ready to copy-paste to LinkedIn. Nothing else.

**FORBIDDEN in the page:**
- Work headers ("Draft v1", "Variant 2")
- Writing notes
- Angle analysis
- Post objective
- Separators `---`

## Known errors

| Error | Cause | Fix |
|---|---|---|
| Everything in one block | Literal `\n` instead of real line breaks | Use real newlines |
| `<empty-block/>` shows as text | Same, not interpreted | Use real newlines |
| Image disappeared | `replace_content` on page with uploaded image | Use `update_content` |
| `u20ac` displayed | Unicode escape not interpreted | Write `EUR` or the symbol directly |
