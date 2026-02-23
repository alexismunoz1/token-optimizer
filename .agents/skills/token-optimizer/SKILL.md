---
name: token-optimizer
description: >
  Practical guide to reduce token consumption, lower AI costs, and improve
  Claude Code performance through file organization, context management,
  and strategic model selection. Backed by real experiment data.
  Use when user mentions "optimize tokens", "reduce costs", "Claude is slow",
  "too many tokens", "token budget", "context window full", "organize codebase
  for AI", or "reduce token consumption". Do NOT use for general coding questions,
  debugging, or performance optimization unrelated to AI token usage.
compatibility: Claude Code
license: MIT
metadata:
  author: alexismunoz1
  version: 1.1.0
---

# Token Optimizer

## Important

- **Always diagnose first**: Before recommending optimizations, have the user run `/context` to see current token usage
- **Measure before and after**: Every optimization should be verified with metrics
- **Focus on the user's specific problem**: Don't dump all optimizations — identify and address the most impactful one first
- **Be transparent about trade-offs**: Modular files save 18%+ on focused tasks but show minimal difference on cross-cutting tasks at small scale

## Instructions

This skill helps users reduce token consumption and AI costs in Claude Code. Follow this diagnostic workflow:

1. **Assess**: Ask the user to run `/context` to see current token usage
2. **Identify**: Determine the biggest source of waste (large files, bloated CLAUDE.md, accumulated context, too many MCPs)
3. **Recommend**: Suggest the highest-impact optimization from the Quick Wins Checklist
4. **Verify**: After changes, have the user re-run `/context` to measure improvement

For detailed reference material, see the `references/` folder:
- `references/file-organization-guide.md` — naming conventions, project structure templates
- `references/context-management-guide.md` — lazy loading, subagents, MCP management
- `references/metrics-report.md` — complete experiment data and methodology
- `references/claude-md-template.md` — optimized CLAUDE.md template

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

Small, focused files are the single highest-impact optimization. Our experiment showed modular code reduced tokens by 18.2% and noise by 92% on focused tasks (the majority of daily work).

### Core Rules

- **Maximum 150 lines per file** — if longer, split by responsibility
- **Single responsibility** — one concern per file
- **Descriptive names in kebab-case** — the filename should tell the AI exactly what's inside

### Why It Works

When the AI needs to fix an email validation bug:
- **Monolithic** (814 lines): reads entire file → 49,466 tokens
- **Modular** (67 lines): reads only `validation-utils.ts` → 40,447 tokens → **18.2% savings, 92% less noise**

At scale (5,000+ lines), monolithic files become impossible to process efficiently. Modular files maintain constant size regardless of project growth.

> For naming conventions, avoid/prefer tables, and project structure templates, see `references/file-organization-guide.md`

### Action Items

- [ ] Identify files over 150 lines in your project
- [ ] Split them by responsibility (one concern = one file)
- [ ] Rename generic files (`utils.ts`, `helpers.ts`) to descriptive names

## Optimized CLAUDE.md

A well-structured CLAUDE.md can reduce token consumption by 50-70%. Keep it under 500 lines — essentials only.

### Key Principles

1. **Be specific** — "PostgreSQL with Prisma" not just "database"
2. **Include structure** — tell the AI where things live
3. **List commands** — save the AI from guessing
4. **Specify what to ignore** — add directories the AI should skip
5. **Use triggers, not full docs** — reference skills/files for details, don't inline everything

> For an optimized CLAUDE.md template, see `references/claude-md-template.md`

### Action Items

- [ ] Create or audit your CLAUDE.md
- [ ] Ensure it's under 500 lines
- [ ] Include project structure, commands, and key conventions
- [ ] Remove any verbose documentation (move to references or skills)

## Context Management

Token waste often comes from accumulated irrelevant context, not from individual operations.

### Essential Commands

| Command | When to Use |
|---------|-------------|
| `/clear` | Switching tasks, after major corrections |
| `/compact` | Long conversation (>50 exchanges) |
| `/context` | Diagnosing high token use |

### Rules of Thumb

- If you've corrected Claude more than 2 times on the same topic → `/clear` and restart with a better prompt
- After completing a feature → `/clear` before starting the next one
- Conversation feeling slow → try `/compact` first, `/clear` if that doesn't help

### Lazy Loading Principle

Don't front-load all information. Use triggers in CLAUDE.md that reference detailed docs. One project achieved **54% reduction** in initial tokens (7,584 → 3,434) by putting specific instructions in skills that load on demand.

> For advanced context strategies, subagent patterns, and MCP management, see `references/context-management-guide.md`

### Action Items

- [ ] Start using `/clear` between unrelated tasks
- [ ] Use `/compact` when conversations get long
- [ ] Move detailed docs out of CLAUDE.md into referenced files

## Advanced Optimizations

### Subagents for Verbose Tasks

Use the Task tool for operations that generate large output (test runs, builds, searches). The verbose output stays in the subagent's context — only the summary returns to your main conversation.

Best candidates: test suites, broad codebase searches, build/compilation tasks, log analysis.

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

**Key experiment finding**: Focused tasks (bug fixes, specific changes) saved 18.2% tokens with modular code. Cross-cutting tasks showed minimal difference at small scale (+1-5%).

> Note: These results are from a small-scale experiment (814 lines). Cross-cutting tasks show minimal difference at this scale. Benefits increase significantly at 5,000+ lines. For the complete methodology and raw data, see `references/metrics-report.md`

### Action Items

- [ ] Baseline your current token usage with `/context`
- [ ] Apply the Quick Wins Checklist from the top
- [ ] Re-measure after changes to quantify improvement

## Examples

### Example 1: "My Claude Code sessions are getting expensive"

**Actions:**
1. Run `/context` to see current token consumption breakdown
2. Audit CLAUDE.md size — if over 500 lines, trim to essentials
3. Check for files >150 lines — identify candidates for splitting
4. Count active MCPs — disable unused ones
5. Review model usage — switch routine tasks to Sonnet/Haiku

**Result:** Actionable optimization report with specific savings estimates per change.

### Example 2: "Organize my codebase for AI"

**Actions:**
1. Scan the project for files exceeding 150 lines
2. Identify generic filenames (`utils.ts`, `helpers.ts`, `index.ts` with logic)
3. Propose file splits by responsibility with new descriptive names
4. Suggest a project structure following the organization guide

**Result:** File reorganization plan with before/after naming and structure.

### Example 3: "My context window keeps filling up"

**Actions:**
1. Run `/context` to identify what's consuming tokens
2. Check if CLAUDE.md has inline documentation that should be referenced instead
3. Recommend `/clear` between tasks and `/compact` for long sessions
4. Suggest moving verbose content to referenced files (lazy loading)

**Result:** Context management strategy with immediate actions.

## Troubleshooting

**Problem: "I applied optimizations but don't see improvement"**
- Cause: No baseline measurement was taken before changes
- Solution: Run `/context` before AND after each optimization to measure impact

**Problem: "I don't know how many tokens I'm using"**
- Cause: Token consumption isn't visible by default
- Solution: Use `/context` to see the full breakdown of what's consuming tokens

**Problem: "`/compact` doesn't reduce enough"**
- Cause: `/compact` compresses history but keeps essentials — it can't remove everything
- Solution: Use `/clear` if the prior context is irrelevant to your next task

**Problem: "Splitting files made cross-cutting tasks slower"**
- Cause: Tasks that touch many files need multiple reads (1-5% more tokens at small scale)
- Solution: This is expected and marginal. Focused tasks (80% of work) still save 18%+. At scale (5,000+ lines), modular always wins.
