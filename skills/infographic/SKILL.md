---
name: infographic
description: Use when the user wants to create a visual infographic for social media (LinkedIn, Instagram, Twitter). Also use when the user mentions "infographic", "visual post", "comparison visual", or asks to create an image/visual for a post.
user-invocable: true
license: MIT
compatibility: Requires Playwright for screenshot capture. Pure HTML output, no framework needed.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "📊"
    homepage: https://github.com/PierreRtec/claude-skills-public
    requires:
      bins:
        - npx
allowed-tools: Read Edit Write Glob Grep Bash(npx:*)
---

# Infographic

Generate high-quality static HTML infographics for social media, directly from Claude Code.

## Why this skill

Generating an infographic in HTML without process = wrong facts, redo styling, 3-4 wasted iterations. This skill forces CONTENT validation before DESIGN, and DESIGN before CODE.

## Mandatory Workflow

```
Brief -> Phase 1: Content -> Validate -> Phase 2: Visual direction -> Validate -> Phase 3: HTML -> Review -> Done
```

### Phase 1: Validate CONTENT (substance)

**BEFORE touching HTML**, present to the user:
1. Subject/angle: reformulate in 1 sentence
2. Structure: number of columns/blocks, section titles
3. Complete text: each block with exact content (facts, figures, examples)
4. Sources: verify technical facts with the user

Present content as plain text. Ask explicitly: "Here's the content. 100% correct? Any corrections?"
**Do not proceed until content is validated.**

### Phase 2: Validate VISUAL DIRECTION (form)

Describe before coding:
1. **Format**: dimensions (default LinkedIn = 1080x1350)
2. **Theme**: dark (default) or light? Offer both if unspecified
3. **Layout**: columns, grid, vertical scroll
4. **Special elements**: author badge (always), progress bar, icons

Read `references/themes.md` for full theme specifications (colors, CSS, badge).

### Phase 3: Generate HTML

Read `references/technical-rules.md` for all technical requirements.

Key rules:
- Single file: all CSS/JS inline in one `.html` file
- No frameworks: pure HTML/CSS, zero external dependencies except Google Fonts (Inter)
- Fixed viewport: `width=1080`, body `width: 1080px; min-height: 1350px`
- Dot grid: ALWAYS apply background dot pattern
- Author badge: ALWAYS at the bottom (configurable name/title)
- No watermark unless explicitly requested
- SVG icons inline (no icon libraries)

### Phase 4: Review and touch-ups

Open in browser. Touch-ups at this stage must be minor (spacing, font size, color intensity). If content or layout needs deep changes, Phase 1 or 2 was rushed.

## Platform formats

| Platform | Width | Height | Ratio |
|---|---|---|---|
| LinkedIn (post) | 1080px | 1350px | 4:5 |
| LinkedIn (carousel) | 1080px | 1080px | 1:1 |
| Instagram (post) | 1080px | 1350px | 4:5 |
| Twitter/X | 1200px | 675px | 16:9 |

## Screenshot capture

```bash
npx playwright screenshot --viewport-size="1080,1350" --full-page "file:///path/to/file.html" output.png
```

## Configuration

Set in your `CLAUDE.md`:
```markdown
## Infographic Config
- Author name: Your Name
- Author title: Your Title
- Avatar path: path/to/avatar.png
```
