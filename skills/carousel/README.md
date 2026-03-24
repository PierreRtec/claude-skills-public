# Carousel - LinkedIn Carousel Generator

Generate professional LinkedIn carousels (HTML slides + PNG + PDF) with dark and light themes, directly from Claude Code.

**Approximate token count:** ~2,400 tokens

## Features

- Structured workflow: content drafting, validation, theme selection, generation
- Two polished themes: Dark (grid + terracotta accents) and Light (clean + terracotta accents)
- Inline SVG icons for visual impact
- Standalone HTML slides at 1080x1350 (LinkedIn 4:5 format)
- Screenshot capture and PDF assembly pipeline
- Content guardrails: no AI-flag phrases, direct tone, clear structure

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@carousel
```

## Usage

```
> Create a LinkedIn carousel about "5 mistakes developers make with error handling"
```

The skill will:
1. Draft slide content (hook + 5 points + CTA)
2. Present it for your validation
3. Ask for theme preference (dark/light/both)
4. Generate HTML files in a dated workspace folder
5. Capture screenshots and combine into a PDF

## Requirements

- Node.js (for screenshot and PDF scripts - bring your own Puppeteer/Playwright setup)
- An avatar image (square PNG, min 200px)

## Customization

Edit `SKILL.md` to change:
- Color scheme (replace terracotta hex values)
- Slide dimensions
- Writing rules and tone
- Workspace output path

## License

MIT
