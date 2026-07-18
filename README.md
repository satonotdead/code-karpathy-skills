# Karpathy-Inspired Claude Code Guidelines

> **⚠️ Human documentation only — agents read [`CLAUDE.md`](CLAUDE.md) as the canonical source.**
>
> **Fork notice:** This is a personal fork of [`multica-ai/andrej-karpathy-skills`](https://github.com/multica-ai/andrej-karpathy-skills), updated with merged upstream PRs for experimentation. The canonical upstream repo is the source of truth for official releases.
>
> Check out the original author's project [Multica](https://github.com/multica-ai/multica) — an open-source platform for running and managing coding agents with reusable skills.
>
> Follow the original author on X: [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

A single `CLAUDE.md` file to improve Claude Code behavior, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

English | [简体中文](./README.zh.md)

## The Problems

From Andrej's post:

> "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should."

> "They really like to overcomplicate code and APIs, bloat abstractions, don't clean up dead code... implement a bloated construction over 1000 lines when 100 would do."

> "They still sometimes change/remove comments and code they don't sufficiently understand as side effects, even if orthogonal to the task."

## The Solution

Four principles in one file that directly address these issues:

| Principle | Addresses |
|-----------|-----------|
| **Think Before Coding** | Wrong assumptions, hidden confusion, missing tradeoffs, unresolved inconsistencies |
| **Simplicity First** | Overcomplication, bloated abstractions |
| **Surgical Changes** | Orthogonal edits, touching code you don't sufficiently understand |
| **Goal-Driven Execution** | Unlock looping capability through verifiable success criteria |

## The Four Principles in Detail

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs. Recommend clearly.**

LLMs often pick an interpretation silently and run with it. This principle forces explicit reasoning:

- **State assumptions explicitly** — If uncertain, ask rather than guess
- **Surface inconsistencies** — If the request contradicts existing code, prior requirements, or itself, name it explicitly. Don't silently pick a side
- **Present tradeoffs with costs** — When multiple technical approaches exist (speed vs. simplicity, now vs. later), name them with rough costs — don't pick silently
- **Push back when warranted** — If a simpler approach exists, say so
- **Stop when confused** — Name what's unclear and ask for clarification

**Recommend, don't just list:** When surfacing options, always mark your recommended choice with 1–2 sentences of reasoning — why it fits *this* specific context. Presenting a neutral menu pushes the decision burden back without the codebase context you have. Keep the door open: the user may have constraints you don't see.

```
Option A: [description] — [tradeoff]
Option B: [description] — [tradeoff]

→ Recommend: Option A. [reasoning given what you know about the codebase/goals.]

Your call — happy to go with B if [condition that would change the recommendation].
```

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

Combat the tendency toward overengineering:

- No features beyond what was asked
- No abstractions for single-use code
- No "flexibility" or "configurability" that wasn't requested
- No error handling for impossible scenarios
- If 200 lines could be 50, rewrite it
- Exception: Do not sacrifice established patterns that improve long-term maintainability or testability just for immediate simplicity.
- Exception: Never compromise security for simplicity. Security is a high priority and justifies necessary complexity within healthy limits.
- Don't implement existing or native functionality yourself; don't introduce new third-party dependencies unless asked
- Don't use complex one-liners or fancy tricks to appear "clever"; prefer the most straightforward and readable code
- If you take shortcuts for the sake of simplicity, you must use `// TODO: [shortcut]` comments to mark the ceiling and upgrade path

**The test:** Would a senior engineer say this is overcomplicated? If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting
- Don't refactor things that aren't broken
- Match existing style, but if you notice bad habits or poor practices, point them out and ask before continuing them
- If you notice unrelated dead code, mention it — don't delete it

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused
- Don't remove pre-existing dead code unless asked

**Why:** Adjacent code that looks wrong or redundant often encodes constraints you don't see — a workaround, a performance tradeoff, an interface contract. Karpathy's exact phrase: LLMs change "code they don't sufficiently understand as side effects." If you don't know why it's there, don't touch it.

**The test:** Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**LLMs excel at looping until specific goals are met. The unlock: give verifiable criteria instead of vague instructions, then let execution run.**

Transform imperative tasks into verifiable goals:

| Instead of... | Transform to... |
|--------------|-----------------|
| "Add validation" | "Write tests for invalid inputs, then make them pass" |
| "Fix the bug" | "Write a test that reproduces it, then make it pass" |
| "Refactor X" | "Ensure tests pass before and after" |

For multi-step tasks, state a brief plan:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let the LLM loop independently. Weak criteria ("make it work") require constant clarification.

## Self-Check Additions

Prose rules alone get rationalized past. The guidelines add three mechanisms that make each principle checkable in the moment:

- **Rigor calibration table** — replaces "for trivial tasks, use judgment" with three explicit tiers (obvious one-liner / standard change / ambiguous or irreversible), so how much ceremony to apply is a decision, not a vibe.
- **Concrete overcomplication smells** — a checklist under Simplicity First (needless `try/except`, single-implementation interfaces, config for constants, wrapper classes, stdlib reimplementation) that turns "would a senior engineer call this overcomplicated?" into pattern matches.
- **Red-flags table** — names the rationalizing thought itself ("while I'm here...", "it should work now") and maps each one to the principle it's about to violate, catching drift mid-task instead of in review.

Goal-Driven Execution is also hardened into an explicit loop: define the check → run it → implement → run it again → only then report. Claiming "done" without having run the check is banned.

## Install

**Option A: Claude Code Plugin (recommended)**

From within Claude Code, first add the marketplace:
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

Then install the plugin:
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

This installs the guidelines as a Claude Code plugin, making the skill available across all your projects.

**Option B: CLAUDE.md (per-project)**

New project:
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

Existing project (append):
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## Using with Cursor

This repository includes a committed Cursor project rule ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)) so the same guidelines apply when you open the project in Cursor. See **[CURSOR.md](CURSOR.md)** for setup, using the rule in other projects, and how this relates to Claude Code.

## Key Insight

From Andrej:

> "LLMs are exceptionally good at looping until they meet specific goals... Don't tell it what to do, give it success criteria and watch it go."

The "Goal-Driven Execution" principle captures this: transform imperative instructions into declarative goals with verification loops.

## How to Know It's Working

These guidelines are working if you see:

- **Fewer unnecessary changes in diffs** — Only requested changes appear
- **Fewer rewrites due to overcomplication** — Code is simple the first time
- **Clarifying questions come before implementation** — Not after mistakes
- **Clean, minimal PRs** — No drive-by refactoring or "improvements"

## Customization

These guidelines are designed to be merged with project-specific instructions. Add them to your existing `CLAUDE.md` or create a new one.

For project-specific rules, add sections like:

```markdown
## Project-Specific Guidelines

- Use TypeScript strict mode
- All API endpoints must have tests
- Follow the existing error handling patterns in `src/utils/errors.ts`
```

For high-stakes or domain-specific projects, add explicit safety boundaries:

```markdown
## Domain-Specific Guidelines

- Do not process private, regulated, or sensitive data unless the task explicitly requires it.
- Separate source facts, assumptions, and recommendations.
- Preserve uncertainty when evidence is limited.
- Ask for clarification before turning research summaries into operational advice.
```

## Multi-Agent Best Practices

### What multi-agent means here
- Reusing the same core skills (Karpathy principles) across different coding agents.

### Core principles
- Single source of truth for skills.
- Per-agent overrides only when necessary.
- Minimal, surgical changes per project.

### Per-agent patterns
- Claude Code: use CLAUDE.md at repo root.
- Cursor: use CURSOR.md and project-level skills.
- OpenCode: use AGENTS.md and per-project rules.

### Examples
- How to keep behavior consistent across agents.
- How to avoid conflicting instructions.

### Pitfalls
- Overlapping skill files.
- Agent-specific hacks that break simplicity.

## Tradeoff Note

These guidelines bias toward **caution over speed**. For trivial tasks (simple typo fixes, obvious one-liners), use judgment — not every change needs the full rigor.

The goal is reducing costly mistakes on non-trivial work, not slowing down simple tasks.

## License

MIT
