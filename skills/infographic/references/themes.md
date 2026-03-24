# Infographic Themes

## Dark Theme (default)

```css
body {
  background: #0A0A0B;
  color: #F5F5F5;
  background-image: radial-gradient(circle, rgba(255,255,255,0.04) 1px, transparent 1px);
  background-size: 24px 24px;
}
body::before {
  background: radial-gradient(ellipse at 50% 50%, rgba(232, 145, 90, 0.06) 0%, transparent 70%);
}
```

- **Background**: #0A0A0B with subtle dot grid (rgba(255,255,255,0.04))
- **Glow**: radial-gradient terracotta center, opacity 6%
- **Accent**: terracotta #E8845C / #D4714A (gradient)
- **Primary text**: #F5F5F5
- **Secondary text**: #888
- **Cards**: #141415, border rgba(255,255,255,0.08), border-radius 12-16px
- **Accent border**: border-left 3px solid #E8845C on important cards

## Light Theme

```css
body {
  background: #FAFAFA;
  color: #1a1a1a;
  background-image: radial-gradient(circle, rgba(0,0,0,0.06) 1px, transparent 1px);
  background-size: 24px 24px;
}
```

- **Background**: #FAFAFA with subtle dot grid (rgba(0,0,0,0.06))
- **Accent**: terracotta #E8845C / #D4714A (gradient)
- **Primary text**: #1a1a1a
- **Secondary text**: #666, #999
- **Cards**: white #FFFFFF, border rgba(232,145,90,0.15), subtle shadow
- **Accent border**: border-left 3px solid #E8845C

## Common elements

- **Font**: Inter (Google Fonts), weights 400/600/700/800
- **Dot grid**: ALWAYS present (radial-gradient 24px)
- **No watermark**: never add unless explicitly requested
- **Anti-aliasing**: `-webkit-font-smoothing: antialiased`

## Author badge (REQUIRED)

Always placed at the bottom of the infographic.

```html
<div class="author-badge">
  <img src="avatar.png" alt="Author" />
  <div class="author-info">
    <div class="author-name">Your Name</div>
    <div class="author-handle">Your Title</div>
  </div>
</div>
```

```css
.author-badge {
  display: flex;
  align-items: center;
  gap: 14px;
}
.author-badge img {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid rgba(232, 145, 90, 0.3);
  flex-shrink: 0;
}
.author-name { font-size: 15px; font-weight: 700; }
.author-handle { font-size: 12px; color: #888; }
```
