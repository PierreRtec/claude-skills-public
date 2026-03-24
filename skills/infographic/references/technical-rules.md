# Technical Rules

## HTML generation

- **Single file**: all CSS/JS inline in one `.html` file
- **No frameworks**: pure HTML/CSS, zero external dependencies except Google Fonts
- **Fixed viewport**: `<meta name="viewport" content="width=1080" />`
- **Fixed body width**: `width: 1080px; min-height: 1350px; margin: 0 auto;`
- **Anti-aliasing**: `-webkit-font-smoothing: antialiased;`
- **Overflow**: `overflow-x: hidden;` on body
- **Dot grid**: ALWAYS apply the dot pattern in background
- **Author badge**: ALWAYS at the bottom
- **No watermark** unless explicitly requested

## Animations (optional, on request only)

- CSS `@keyframes` + `animation-delay` for sequences
- `animation-fill-mode: forwards` + `opacity: 0` initial
- Keep animations subtle (fade, slide, scale) unless told otherwise

## Typical HTML structure (3-column comparison)

```
body
  .title-area (h1 + subtitle)
  .columns
    .column.col-1 (.col-header + .col-body > .section-block*)
    .column.col-2
    .column.col-3 (star column = flex: 1.15, accent border)
  .progression (optional)
  .author-badge
```

## Common mistakes to avoid

| Mistake | Consequence | Prevention |
|---|---|---|
| Generate HTML without validating content | Wrong facts, 2+ correction iterations | Phase 1 mandatory |
| Pick a style without asking | Dark when user wants light | Phase 2 mandatory |
| External dependencies (CDN icons) | Breaks offline, heavy | SVG inline, Google Fonts only |
| Responsive viewport | Different rendering per screen | Fixed viewport and body width |
| Too much text per block | Unreadable on mobile LinkedIn | Max 3-4 lines per section |
| Missing dot grid | Loses visual identity | Always apply radial-gradient 24px |
