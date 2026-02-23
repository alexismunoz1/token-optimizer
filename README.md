# token-optimizer

An Agent Skill for [skills.sh](https://skills.sh) that helps you reduce token consumption, lower AI costs, and get faster responses from Claude Code.

## Install

```bash
npx skills add alexismunoz1/token-optimizer
```

Or manually:

```bash
cp -r token-optimizer ~/.claude/skills/
```

## What's Included

- **SKILL.md** — Main guide with quick wins, file organization rules, CLAUDE.md template, context management, and advanced optimizations
- **references/file-organization-guide.md** — Detailed naming conventions, avoid/prefer tables, and project structure templates
- **references/context-management-guide.md** — Lazy loading, subagents, MCP management, and model selection strategies
- **references/metrics-report.md** — Real experiment data comparing monolithic vs modular architectures

## Key Metrics

From our real experiment with an 814-line TypeScript e-commerce app:

| Optimization | Impact |
|-------------|--------|
| Modular files (focused tasks) | **-18.2% tokens** |
| Noise reduction (lines processed) | **-92%** |
| Optimized CLAUDE.md | **-50-70% consumption** |
| Lazy loading context | **-54% initial tokens** |
| Haiku vs Opus (simple tasks) | **-94% cost** |

## When Does It Activate?

The skill triggers when you mention things like:
- "optimize tokens" / "reduce costs"
- "Claude is slow" / "too many tokens"
- "organize codebase for AI"
- "context limit" / "token budget"

## Contributing

This is a community skill — contributions are welcome! Here's how:

1. **Fork** the repo and clone it locally
2. **Edit** the relevant file:
   - `SKILL.md` — main guide (keep under 500 lines)
   - `references/` — detailed guides (keep each under 200 lines)
3. **Follow** these guidelines:
   - Content in English (skills.sh standard)
   - Each section should end with actionable items
   - Back claims with data when possible
4. **Submit a PR** with a clear description of what you're adding or improving

Ideas for contributions:
- New optimization techniques with real metrics
- Guides for specific frameworks or languages
- Translations (as separate files in `references/`)
- Additional experiment data at different scales

## License

MIT
