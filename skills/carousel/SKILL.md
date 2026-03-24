---
name: carousel
description: Use when the user wants to create a LinkedIn carousel. Also use when the user mentions "carousel", "carrousel", "slides LinkedIn", or asks to create slides for social media.
user-invocable: true
license: MIT
compatibility: Requires Node.js for screenshot/PDF pipeline (Puppeteer or Playwright).
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "🎠"
    homepage: https://github.com/PierreRtec/claude-skills-public
    requires:
      bins:
        - node
allowed-tools: Read Edit Write Glob Grep Bash(node:*) Bash(npx:*)
---

# LinkedIn Carousel Generator

Generate educational LinkedIn carousels (HTML slides + PNG screenshots + PDF) from Claude Code.

## Profile Configuration

Before using, set your profile in the carousel config or pass it inline:

- **Name**: Your display name
- **Subtitle**: Your tagline (e.g. "AI Engineer" or "Marketing Lead")
- **Avatar**: Absolute path to your avatar image (PNG, square, min 200px)

## Workflow

### Step 1: Understand the content

If the user provides a **topic**: write the full content (hook + points + CTA).
If the user pastes **raw text**: restructure into slides.

Writing rules:
- Direct tone, no corporate BS
- Hook = attention-grabbing: a number, a provocative question, or a bold statement
- Each slide = ONE clear point, max 3-4 lines
- CTA = follow, save, or open question
- Use inline SVG icons to make slides more visual and professional
- Be verbose on content slides: concrete examples, analogies, not just dry statements
- **FORBIDDEN**: AI-flag phrases like "nobody talks about this", "the truth they don't want you to know". Humble and direct tone, not guru.

### Step 2: Present content as plain text

Show the full slide content to the user:
- Slide 1 (Hook): title + subtitle
- Slide 2-N (Content): number + title + body
- Slide N+1 (CTA): text + button

Ask for validation. Do not proceed without OK.

### Step 3: Choose theme

**ALWAYS propose both variants:**
- **Dark** (default): dark grid background, terracotta accent
- **Light**: light background, terracotta accent, dark text

User picks one or both. If no preference, use Dark.

### Step 4: Create workspace

**Directory structure:**
```
workspace/carousels/YYYY-MM-DD-slug/
├── dark/
│   ├── slide-01-hook.html
│   ├── slide-02.html
│   ├── ...
│   ├── slide-N-cta.html
│   └── config.json
└── light/
    ├── slide-01-hook.html
    ├── slide-02.html
    ├── ...
    ├── slide-N-cta.html
    └── config.json
```

Only create subfolders for selected themes. Slug = kebab-case of the topic (max 5 words).

### Step 5: Generate HTML files

Each slide is a standalone HTML file.

**CRITICAL RULE: one theme = consistent end to end.**
- Dark = ALL slides dark. No light slide in the middle.
- Light = ALL slides light. No dark slide in the middle.
- NEVER mix themes within the same carousel.

Files:
- `slide-01-hook.html`: centered hook
- `slide-02.html` to `slide-N.html`: content with accent bar
- `slide-N+1-cta.html`: centered CTA with avatar

Also generate a `config.json` with carousel metadata.

**User-provided images**:
If the user provides images, copy them into the carousel folder and embed via `<img class="content-image" src="image-name.png" />` in the relevant slide.

### Step 6: Capture screenshots

Use a Puppeteer/Playwright script to screenshot each HTML file at 1080x1350.

```bash
node screenshot.mjs path/to/dark
node screenshot.mjs path/to/light
```

### Step 7: Generate PDF

Combine screenshots into a single PDF per theme:

```bash
node combine-pdf.mjs path/to/dark
node combine-pdf.mjs path/to/light
```

### Step 8: Output

Display:
- Path to the folder(s)
- Number of slides
- Confirmation that PDF(s) are ready
- Show screenshots for validation

## Dimensions

- Width: 1080px
- Height: 1350px (LinkedIn carousel 4:5 format)

## Themes

### Dark (default)

**Each slide must have these elements:**
- Background: `#0d0d0f` with subtle grid (`rgba(255,255,255,.03)`, 40px)
- Radial terracotta glow in background (`body::before`)
- Main text: `#f0f0f0`
- Secondary text: `#8a8f98` (muted) / `#b0b5bd` (muted-light)
- Accent: terracotta gradient (`#d97757` > `#e8956a` > `#f0b090`)

**Common graphic elements:**
- Slide number badge: bg `rgba(217,119,87,0.1)`, border `rgba(217,119,87,0.25)`, terracotta text
- Vertical accent bar on the left of content slides (vertical terracotta gradient)
- Bullet points: terracotta 8px dots
- Highlight box: bg `rgba(217,119,87,0.08)`, border `rgba(217,119,87,0.2)`
- Pills: double background trick (bg padding-box + gradient border-box), transparent border
- CTA button: terracotta gradient + `box-shadow: 0 4px 30px rgba(217,119,87,0.3)`
- Footer with 36px avatar + name on each content slide
- Footer with 48px avatar + name/handle on hook slide

**Special slides:**
- Hook: centered, h1 66px, optional badge top-right, double glow (bottom-right + top-left)
- CTA: centered, 120px avatar with terracotta ring, thematic pills, gradient button

### Light

**Each slide must have these elements:**
- Background: `#fafafa` (no grid)
- Top accent line: `height: 4px; background: linear-gradient(90deg, var(--terracotta), var(--terracotta-light))` at top of each slide
- Main text: `#1a1a1a`
- Secondary text: `#6b7280` (muted) / `#9ca3af` (muted-light)
- Accent: same terracotta (`#d97757` > `#e8956a` > `#f0b090`)

**Common graphic elements:**
- Slide number badge: bg `rgba(217,119,87,0.08)`, terracotta text
- Vertical accent bar same as dark
- Cards/boxes: bg `#ffffff`, `border: 1px solid #e5e7eb`, `box-shadow: 0 2px 16px rgba(0,0,0,0.04)`, `border-radius: 16px`
- Highlight box: bg `rgba(217,119,87,0.05)`, border `rgba(217,119,87,0.15)`
- Pills: white background, light terracotta border
- Footer identical (avatar + name)

**Special slides:**
- Hook: centered, bg `#fafafa`, optional badge top-right, light terracotta glow in corner
- CTA: bg `#fafafa`, avatar with terracotta ring, terracotta gradient button

## SVG Icons

Use inline SVG icons in the HTML to make slides more impactful:
- Size: 20-48px depending on context
- Style: **stroke**, not fill (more professional and consistent)
- Colors: `currentColor`, terracotta, or semantic colors (green check, red cross, yellow warning)
- Embed directly in HTML (no external file, for screenshot compatibility)
- Useful patterns: check, cross, warning triangle, lightning, clock, calendar, document, message, arrow

## Common Mistakes

| Mistake | Prevention |
|---------|-----------|
| Generating HTML without validating content | Step 2 is mandatory |
| Too much text per slide | Max 3-4 lines body |
| Images too large | max-height: 500px, border-radius: 16px |
| Fonts not loaded in screenshot | Script must await document.fonts.ready |
| Only proposing one theme | ALWAYS propose dark AND light |
| Mixing dark and light in a carousel | FORBIDDEN. One theme = consistent across all slides |
| Putting both themes in same folder | Separate subfolders: `dark/` and `light/` |
| AI-flag phrases ("nobody talks about this") | Humble and direct tone, not guru |
