---
name: token-optimizer
description: >
  Optimize token usage, reduce AI costs, and improve Claude Code performance.
  Activate when users mention: optimize tokens, reduce costs, Claude is slow,
  organize codebase for AI, context limit, too many tokens, save tokens,
  token budget, expensive AI, context window full, AI code organization,
  file structure for AI, reduce token consumption, efficient prompting.
license: MIT
metadata:
  author: alexismunoz1
  version: 1.0.0
---

# Token Optimizer

Practical guide to reduce token consumption, lower costs, and get faster responses from Claude Code. Every recommendation is backed by real experiment data (see `references/metrics-report.md`).

## Quick Wins Checklist

Apply these in order of impact:

1. **Split large files** (>150 lines) into focused modules → saves 18%+ tokens on focused tasks
2. **Optimize your CLAUDE.md** → can reduce consumption 50-70%
3. **Use `/clear` between tasks** → eliminates irrelevant context
4. **Use `/compact` in long conversations** → compresses history
5. **Use the right model** → Haiku costs 18x less than Opus for simple tasks
6. **Limit active MCPs** to ≤10 → fewer tools = less overhead per call
7. **Use subagents for verbose tasks** → output stays in subagent context

## File Organization Rules

Small, focused files are the single highest-impact optimization. Our experiment showed that modular code reduced tokens by 18.2% and noise by 92% on focused tasks (the majority of daily work).

### Core Rules

- **Maximum 150 lines per file** — if longer, split by responsibility
- **Single responsibility** — one concern per file
- **Descriptive names in kebab-case** — the filename should tell the AI exactly what's inside

### Naming Convention

| Avoid | Prefer |
|-------|--------|
| `utils.ts` | `string-utils.ts`, `date-utils.ts` |
| `helpers.ts` | `date-formatting-helpers.ts` |
| `api.ts` (all endpoints) | `users-api.ts`, `products-api.ts` |
| `index.ts` (with logic) | `user-authentication.ts` |
| `data.ts` | `product-catalog-data.ts` |

### Why It Works

When the AI needs to fix an email validation bug:
- **Monolithic** (814 lines): reads entire file → 49,466 tokens
- **Modular** (67 lines): reads only `validation-utils.ts` → 40,447 tokens → **18.2% savings, 92% less noise**

At scale (5,000+ lines), monolithic files become impossible to process efficiently. Modular files maintain constant size regardless of project growth.

> For detailed tables and project structure templates, see `references/file-organization-guide.md`

### Action Items

- [ ] Identify files over 150 lines in your project
- [ ] Split them by responsibility (one concern = one file)
- [ ] Rename generic files (`utils.ts`, `helpers.ts`) to descriptive names

## Optimized CLAUDE.md Template

A well-structured CLAUDE.md can reduce token consumption by 50-70%. Keep it under 500 lines — essentials only.

```markdown
# Project Name

Brief one-line description of the project.

## Tech Stack

- Language: TypeScript
- Framework: Next.js 14 (App Router)
- Database: PostgreSQL with Prisma ORM
- Testing: Vitest

## Project Structure

src/           → application code
tests/         → unit tests
docs/          → documentation
components/    → UI components
services/      → business logic
utils/         → specific utilities (string-utils, date-utils, etc.)

## Key Conventions

- Files: kebab-case, max 150 lines, single responsibility
- Tests: colocated in __tests__/ folders
- Imports: absolute paths from @/

## Commands

- `npm run dev` — start dev server
- `npm test` — run tests
- `npm run lint` — lint code

## Important Patterns

- All API routes in src/app/api/
- Shared types in src/types/
- Business logic in src/services/, never in components
```

### Key Principles

1. **Be specific** — "PostgreSQL with Prisma" not just "database"
2. **Include structure** — tell the AI where things live
3. **List commands** — save the AI from guessing
4. **Specify what to ignore** — add directories the AI should skip
5. **Use triggers, not full docs** — reference skills/files for details, don't inline everything

### Action Items

- [ ] Create or audit your CLAUDE.md
- [ ] Ensure it's under 500 lines
- [ ] Include project structure, commands, and key conventions
- [ ] Remove any verbose documentation (move to references or skills)

## Context Management

Token waste often comes from accumulated irrelevant context, not from individual operations.

### Essential Commands

| Command | When to Use | Effect |
|---------|-------------|--------|
| `/clear` | Switching tasks | Resets context completely |
| `/compact` | Long conversation (>50 exchanges) | Compresses history, keeps essentials |
| `/context` | Diagnosing high token use | Shows what's consuming tokens |

### Rules of Thumb

- If you've corrected Claude more than 2 times on the same topic → `/clear` and restart with a better prompt
- After completing a feature → `/clear` before starting the next one
- Conversation feeling slow → try `/compact` first, `/clear` if that doesn't help

### Lazy Loading Principle

Don't front-load all information. Use triggers in CLAUDE.md that reference detailed docs:

```markdown
## Authentication
For auth implementation details, see docs/auth-guide.md
```

One project achieved **54% reduction** in initial tokens (7,584 → 3,434) by:
- Putting specific instructions in skills that load on demand
- Keeping only triggers in CLAUDE.md, not complete documentation
- Principle: Claude doesn't need all info upfront — it needs to know *when* to load it

> For advanced context strategies, see `references/context-management-guide.md`

### Action Items

- [ ] Start using `/clear` between unrelated tasks
- [ ] Use `/compact` when conversations get long
- [ ] Move detailed docs out of CLAUDE.md into referenced files

## Advanced Optimizations

### Subagents for Verbose Tasks

Use the Task tool for operations that generate large output (test runs, builds, searches). The verbose output stays in the subagent's context — only the summary returns to your main conversation.

Best candidates for subagents:
- Running test suites
- Broad codebase searches
- Build/compilation tasks
- Log analysis

### MCP Management

- Keep **maximum 10 active MCPs** at a time
- Keep **maximum 80 total tools** across all MCPs
- Disable MCPs not needed for the current task
- Each unused MCP still costs tokens in tool descriptions

### Strategic Model Selection

| Task Type | Model | Why |
|-----------|-------|-----|
| 80% of daily tasks | Sonnet | Best cost/performance ratio |
| Complex architecture | Opus | Deeper reasoning needed |
| Simple/quick tasks | Haiku | Up to 18x cheaper than Opus |

Default to Sonnet. Escalate to Opus only for genuinely complex problems. Use Haiku for simple tasks, tests, and searches.

### Action Items

- [ ] Delegate verbose tasks to subagents
- [ ] Audit active MCPs — disable unused ones
- [ ] Use Sonnet as default, Haiku for simple tasks

## How to Measure

Track these metrics to verify optimizations are working:

1. **Tokens per task** — check with `/context` before and after changes
2. **Files read per task** — fewer files read = less noise
3. **Lines processed** — compare monolithic vs modular reads
4. **Time per response** — faster responses indicate less processing

### Our Experiment Results

| Scenario | Monolithic | Modular | Difference |
|----------|-----------|---------|------------|
| Bug fix (focused) | 49,466 tokens | 40,447 tokens | **-18.2%** |
| Lines processed (bug fix) | 814 lines | 67 lines | **-92% noise** |
| New feature (cross-cutting) | 50,350 tokens | 50,949 tokens | +1.2% |
| Refactor (cross-cutting) | 49,687 tokens | 51,699 tokens | +4.1% |

**Key insight**: Focused tasks (80% of daily work) benefit enormously from modular code. Cross-cutting tasks show minimal difference at small scale but modular wins decisively at 5,000+ lines.

> For the complete experiment methodology and raw data, see `references/metrics-report.md`

### Action Items

- [ ] Baseline your current token usage with `/context`
- [ ] Apply the Quick Wins Checklist from the top
- [ ] Re-measure after changes to quantify improvement
