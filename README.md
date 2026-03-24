# Claude Code Skills

AI agent skills that extend Claude Code with domain-specific expertise : LinkedIn content, clean code, lead enrichment, meeting workflows, and more.

> **Crafted by a human.** Built with Claude, tested, reviewed and refined by [Pierre](https://pro-dev.fr) (3x Anthropic Certified). No AI slop.

## Skills

### Content & LinkedIn

| Skill | Description | SKILL.md | Full |
|---|---|---|---|
| [carousel](./skills/carousel/) | Generate LinkedIn carousel slides (HTML + PDF) with dark/light themes | ~2,400 | ~2,400 |
| [infographic](./skills/infographic/) | Generate static HTML infographics for social media with structured workflow | ~2,800 | ~4,200 |
| [linkedin-post-notion](./skills/linkedin-post-notion/) | Create and edit LinkedIn post drafts in Notion with correct formatting | ~1,500 | ~1,500 |
| [linkedin-veille](./skills/linkedin-veille/) | Monitor trending LinkedIn posts, filter noise, get content inspiration | ~2,500 | ~2,500 |

### Development

| Skill | Description | SKILL.md | Full |
|---|---|---|---|
| [clean-code](./skills/clean-code/) | Apply Uncle Bob's Clean Code & Clean Architecture principles | ~4,800 | ~6,500 |

### Business & CRM

| Skill | Description | SKILL.md | Full |
|---|---|---|---|
| [lead-enrichment](./skills/lead-enrichment/) | Enrich prospect data via LinkedIn/Unipile and push to Notion | ~1,800 | ~1,800 |
| [meeting-recap](./skills/meeting-recap/) | Structured meeting notes, follow-up emails, and action items in Notion | ~1,200 | ~1,200 |

## Installation

### Claude Code (recommended)

```bash
claude skills add PierreRtec/claude-skills-public@<skill-name>
```

Pick the skills you need :

```bash
# Content & LinkedIn
claude skills add PierreRtec/claude-skills-public@carousel
claude skills add PierreRtec/claude-skills-public@infographic
claude skills add PierreRtec/claude-skills-public@linkedin-post-notion
claude skills add PierreRtec/claude-skills-public@linkedin-veille

# Development
claude skills add PierreRtec/claude-skills-public@clean-code

# Business & CRM
claude skills add PierreRtec/claude-skills-public@lead-enrichment
claude skills add PierreRtec/claude-skills-public@meeting-recap
```

<details>
<summary>Other platforms (Cursor, Copilot, Gemini CLI, etc.)</summary>

These skills follow the [Agent Skills specification](https://github.com/anthropics/agent-skills). Clone the repo and point your tool to the skill directory :

```bash
git clone https://github.com/PierreRtec/claude-skills-public.git
```

Refer to your platform's documentation for configuring custom skill directories.

</details>

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- Skills that interact with Notion require the [Notion MCP server](https://www.npmjs.com/package/@anthropic-ai/notion-mcp-server) configured
- `lead-enrichment` and `linkedin-veille` require [Unipile MCP server](https://www.unipile.com/) configured

## Token budgets

Skills are designed to be lightweight. Typically 2-4 skills load concurrently; total loaded SKILL.md should stay below ~10k tokens.

- **SKILL.md** : the entry point, loaded when the skill triggers (~1,200-4,800 tokens)
- **Full directory** : includes `references/` subdirectories for lazy-loaded details
- Secondary files are only read when the topic is relevant, keeping context lean

## Contributing

Contributions welcome. If you build a skill that fits this collection :

1. One directory per skill under `skills/`, kebab-case naming
2. `SKILL.md` as entry point with full YAML frontmatter (name, description, license, compatibility, metadata, allowed-tools)
3. Keep SKILL.md under ~2,500 tokens ; use `references/` for depth
4. Full skill directory under ~10,000 tokens total
5. Include a `README.md` with usage examples and requirements
6. Bump `metadata.version` on every update (semver)

## License

MIT - Copyright (c) 2025-2026 [Pierre](https://pro-dev.fr)
