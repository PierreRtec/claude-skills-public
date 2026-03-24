# Infographic - HTML Infographic Generator for Social Media

Generate high-quality static HTML infographics for LinkedIn, Instagram, and Twitter/X directly from Claude Code.

**Approximate token count:** ~2,800 tokens (SKILL.md + 2 reference files)

## Features

- Structured 4-phase workflow: Content validation, Visual direction, HTML generation, Review
- Two polished themes: Dark (default) and Light, both with terracotta accents and dot grid
- Platform-aware dimensions (LinkedIn 4:5, Instagram 4:5, Twitter 16:9)
- Single-file HTML output with zero external dependencies (except Google Fonts)
- Inline SVG icons, configurable author badge
- Playwright screenshot capture command included

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@infographic
```

## Usage

```
> Create an infographic comparing React, Vue, and Svelte for LinkedIn
```

The skill will:
1. Draft and validate content with you (facts, structure, text)
2. Propose visual direction (dark/light theme, layout)
3. Generate a single HTML file at 1080x1350
4. Open for review and minor touch-ups

## Configuration

Add to your `CLAUDE.md`:

```markdown
## Infographic Config
- Author name: Your Name
- Author title: Your Role
- Avatar path: path/to/avatar.png
```

## File Structure

```
infographic/
├── SKILL.md                          # Main skill definition
├── README.md                         # This file
└── references/
    ├── themes.md                     # Dark/light theme specs, colors, CSS, author badge
    └── technical-rules.md            # HTML rules, structure, common mistakes
```

## License

MIT
