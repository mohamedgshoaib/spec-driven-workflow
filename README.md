# Agent Workflow System

A reusable, git-based memory system for coordinating AI planning and execution agents across sessions. Built for projects where one agent plans (Perplexity) and another executes (Codex, Claude Code, Cursor), with git as the only communication channel between them.

---

## The Problem This Solves

AI agents have no memory between sessions. When you close a chat, context is gone. When you switch from a planning agent to a coding agent, nothing transfers. Teams working with multiple agents end up re-explaining the same architecture decisions, re-answering the same questions, and watching agents contradict each other.

This system solves that with a `/spec` folder that acts as shared, persistent memory — exchanged via git, readable by any agent, structured so it never grows into an unreadable wall of text.

---

## How It Works

```
You → Perplexity (plans) → writes to /spec/sessions/[date]-[topic]/
→ git push → git pull in editor
→ Codex (executes) → writes back to /spec/sessions/[date]-[topic]/executor.md
→ updates /spec/status/ → git push
→ git pull → Perplexity reads updates → next session
```

The `/spec` folder is the single source of truth. Agents never rely on chat history — they rely on spec files.

---

## Folder Structure

```
/
  AGENTS.md                     ← Read by all coding agents (Codex, Claude Code, Cursor)
  docs/
    SESSION-GUIDE.md            ← Your personal guide for running sessions (not for agents or project repos)
  spec/
    core/                       ← Permanent reference. Read every session. Never large.
      context.md                ← What the project is. Changes only if vision changes.
      config-matrix.md          ← Every configuration option and its rules.
      decisions.md              ← Append-only log of every architectural decision.
      stack.md                  ← Confirmed technology choices and versions.
      constraints.md            ← Non-negotiables, invalid combos, known edge cases.
    sessions/                   ← One folder per session. Past folders are immutable.
      YYYY-MM-DD-NNN-topic/
        planner.md              ← Written by planning agent. Plan, research, decisions.
        handoff.md              ← Written by planning agent. Exact instructions for executor.
        executor.md             ← Written by coding agent. What was done, deviations, blockers.
    status/                     ← Live state. Updated every session.
      progress.md               ← What is done, in progress, and blocked.
      next-session.md           ← Seed for the next planning session.
```

---

## What's Included

| File                             | Purpose                                                                                                                                           |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AGENTS.md`                      | Instructions for coding agents — architecture rules, how to read spec, how to write back, output formats                                          |
| `docs/SESSION-GUIDE.md`          | Your personal operating manual — how to start, close, and hand off sessions. Lives in this workflow repo only. Do not copy it into project repos. |
| `docs/PROJECT-CLARITY-INTAKE.md` | Intake checklist for vague prompts. Defines what must be clarified before implementation.                                                         |
| `spec/core/*.md`                 | Pre-filled core files ready to be adapted to your project                                                                                         |
| `spec/status/*.md`               | Pre-filled status files with empty starting state                                                                                                 |
| `spec/sessions/.gitkeep`         | Empty placeholder so the sessions folder is tracked by git                                                                                        |

---

## How to Adapt This to Your Project

1. **Fork or clone this repo**
2. **Edit `spec/core/context.md`** — replace the placeholder project description with your project
3. **Edit `spec/core/config-matrix.md`** — replace with your project's configuration dimensions, or delete if not applicable
4. **Edit `spec/core/stack.md`** — replace with your confirmed tech choices
5. **Clear `spec/core/decisions.md`** — remove any example decisions, keep the format header
6. **Clear `spec/core/constraints.md`** — remove any example rules, keep your own non-negotiables
7. **Keep `spec/sessions/.gitkeep` until first real session** — then create `spec/sessions/YYYY-MM-DD-NNN-topic/`
8. **Update `AGENTS.md`** — add project-specific architecture rules and constraints where needed
9. **Copy only `AGENTS.md` and `spec/` into your project repo** — leave `docs/SESSION-GUIDE.md` here. It is your personal reference, not a project artifact.
10. **Adapt your planning agent's custom instructions** — use `docs/PLANNER-INSTRUCTIONS.md`

---

## Working With Vague Prompts

If a user prompt is underspecified, do not jump directly to implementation.

Required process:

1. Run the intake checklist in `docs/PROJECT-CLARITY-INTAKE.md`
2. Resolve blocking ambiguities before irreversible work
3. Label defaults as assumptions, not facts
4. Log assumptions and confidence in session notes

This prevents AI quality degradation from silent assumptions.

---

## The Two-Layer Reading Strategy

Agents never read the full history. They use a two-layer approach that keeps token cost low regardless of how many sessions have run:

**Layer 1 — Always read (stable, small):**
All files in `spec/core/` — these never grow large because they are maintained, not appended to freely.

**Layer 2 — Recent context only:**
The last 2 session folders in `spec/sessions/` (sorted by date) + `spec/status/next-session.md`.

This gives any agent the permanent rules and the recent context — nothing more.

---

## Session Naming Convention

```
YYYY-MM-DD-NNN-short-topic-slug
```

- `YYYY-MM-DD` — date the session was planned
- `NNN` — zero-padded sequential session number (001, 002, 003...)
- `short-topic-slug` — what the session is about

Examples:

```
2025-01-20-001-preparation
2025-01-21-002-cli-framework-selection
2025-01-22-003-config-resolver-architecture
2025-02-01-004-rtl-shared-utility
```

---

## Key Rules

- **Past session folders are immutable.** Never edit `planner.md`, `handoff.md`, or `executor.md` after the session ends.
- **`decisions.md` and `constraints.md` are append-only.** Never delete past entries.
- **`spec/core/` files are maintained, not grown.** Update them when something changes — do not keep adding to them indefinitely.
- **Agents never rely on chat history.** If the spec files are not enough to orient an agent, the spec files are incomplete — update them.
- **Every deviation gets documented.** If the executor did something differently than the handoff said, it goes in `executor.md` with a reason.

---

## Compatibility

`AGENTS.md` is respected by:

- OpenAI Codex
- Claude Code (Anthropic)
- Cursor
- Any agent that reads repository context files at session start

The planning workflow is designed for Perplexity but works with any research-capable AI that accepts custom instructions.

---

## Example Session

No sample session is included by default. Create real session folders under `spec/sessions/YYYY-MM-DD-NNN-topic/` as you work.

---

## License

MIT — use this freely in your own projects.
