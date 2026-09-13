# LODESTAR

A minimalist cosmic-mining bullet-heaven browser game. Pure HTML5 Canvas, no build step. Built entirely with [Claude Code](https://claude.com/claude-code).

**Live**: <https://lodestar-lime-nine.vercel.app>

Chase and shatter drifting space ores — pebbles, silver, gold, platinum, diamond, and the rare mithril core. Auto-firing weapons handle most of the work; you steer.

## Play

Open `index.html` directly in any modern browser — no install, no build required.

## Controls

- **WASD** / arrow keys — Move
- **SPACE** or **left click** — Basic mining shot
- All other weapons fire automatically

## Stack

- Single `index.html` (~1600 lines, HTML + CSS + JS inline)
- Canvas 2D API for rendering, procedural Web Audio for SFX
- Zero dependencies, zero build tools, zero framework
- Auto-deployed to Vercel on `main` push, tracked with Google Analytics 4

## For AI Agents Working On This Repo

If you're an AI coding agent (Claude Code, Cursor, Codex…):

1. **Read [`CLAUDE.md`](./CLAUDE.md) first** — it's the entry point.
2. Then read [`docs/AI_HANDOFF.md`](./docs/AI_HANDOFF.md) for full context.
3. Update [`docs/ROADMAP.md`](./docs/ROADMAP.md) as you complete or discover work.

## For Humans

- **Design decisions & history**: [`docs/AI_HANDOFF.md`](./docs/AI_HANDOFF.md)
- **What's done / what's next**: [`docs/ROADMAP.md`](./docs/ROADMAP.md)

## Status

Beta · v0.8 · actively iterating. Previously known as SOLARIS (rebranded 2026-09-11 when concept shifted from cosmic defense to cosmic mining).
