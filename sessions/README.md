# Session Management — File-Based State Protocol

> How AI agents maintain continuity across sessions without databases, servers, or shared memory.

---

## The Core Idea

AI coding agents lose all memory when a session ends. Every new conversation starts from zero. The file-based state protocol solves this by storing everything an agent needs to reconstruct context in plain markdown files that persist between sessions.

**Convention over infrastructure.** There are no databases, no message queues, no shared memory systems. Just markdown files in a `sessions/` directory, with conventions about when to read and write them. Any AI agent that can read and write files can participate.

**Context is reconstructed, not persisted.** The agent doesn't "remember" — it re-reads a few key files at the start of each session and rebuilds understanding from scratch. The conversation is disposable. The files are permanent.

---

## The Three Session Files

### 1. `session_memory.md` — The Persistent Log

**Purpose:** Cross-session continuity. What happened, what was decided, what's next.

**When to write:** End of every session (during `/wrapup`).

**When to read:** Start of every session (during `/gogogo`).

**Format:**

```markdown
---
## 2026-02-15 — [Agent: CCT] — Added authentication middleware
**Project:** MyApp

**What was done:**
- Implemented JWT token validation middleware
- Added rate limiting to /api/auth endpoints
- Created tests for token expiry edge cases

**Files changed:**
- `src/middleware/auth.py` — new file, JWT validation
- `src/routes/auth.py` — added rate limiting decorator
- `tests/test_auth.py` — 12 new tests (all passing)

**Key decisions:**
- Chose JWT over session cookies for stateless scaling

**Open items / handoff notes:**
- Refresh token rotation not implemented yet — next session
- Need to add CORS headers for frontend integration
---
```

**Conventions:**
- Most recent entries at the top (reverse chronological)
- One entry per session, not per task
- Agent tag (`[Agent: CCT]`, `[Agent: AG]`, etc.) identifies which AI wrote it
- Keep entries concise — 10-20 lines max
- Archive entries older than 4 weeks to `sessions/archive/YYYY-MM.md`

### 2. `scratchpad.md` — The Ephemeral Handoff Pad

**Purpose:** Quick notes between agents or between sessions. Think of it as a sticky note on the monitor.

**When to write:** Anytime an agent has something the next agent (or next session) needs to know *right now*.

**When to read:** Before starting any new major task block during a session.

**Format:**

```markdown
## Open Notes

### [2026-02-15] From CCT
Auth middleware is committed but tests need the mock server running.
Start the mock with: `python -m pytest tests/ --mock-server`

### [2026-02-14] From AG
Researched OAuth providers — comparison doc at `research/oauth_comparison.md`.
Recommending Auth0 based on free tier limits. CCT should review before implementing.
```

**Conventions:**
- Not a permanent record — clear resolved notes regularly
- Check this file before major task blocks (mid-session convention)
- Any agent can write, any agent can read
- Keep notes actionable and short

### 3. `agent_activity.md` — The Coordination Log

**Purpose:** Track which agents are active and what they're touching. Prevents conflicts when multiple agents work on the same project.

**When to write:** Start and end of every agent session.

**When to read:** Start of every session (check what other agents did recently).

**Format:**

```markdown
## Recent Activity

### 2026-02-15 14:30 — CCT session start
**Files working on:** src/middleware/auth.py, tests/test_auth.py
**Task:** Implementing JWT middleware

### 2026-02-15 13:00 — AG session end
**Files modified:** research/oauth_comparison.md
**Summary:** Completed OAuth provider comparison
**Handoff:** Recommendation ready for CCT review
```

**Conventions:**
- Log session start with intended files/tasks
- Log session end with files actually modified and handoff notes
- If you see another agent is currently active on the same files, warn the developer
- This file is append-only — don't edit other agents' entries

---

## Why Files Over Databases

| Concern | File-Based Approach | Why It Works |
|---------|-------------------|-------------|
| **Persistence** | Files survive session boundaries | Git-trackable, backed up automatically |
| **Multi-agent access** | Any agent that reads markdown can participate | No driver, SDK, or API required |
| **Human readability** | Open in any editor | Developer can read, edit, and verify agent notes |
| **Conflict resolution** | Convention-based (check before writing) | At human-managed scale, conflicts are rare |
| **Portability** | Copy the `sessions/` folder anywhere | Works across machines, platforms, and AI tools |
| **Debugging** | `git log sessions/` shows the full history | Complete audit trail for free |

The tradeoff: no real-time notifications. Agents don't know when another agent writes something — they only see it when they actively read the file. This is handled by the **mid-session convention**: before starting any new major task, re-read `scratchpad.md`.

---

## Gitignore Convention

Session files contain ephemeral, local state. They should **not** be committed to git:

```gitignore
# Session files — local state, not committed
sessions/session_memory.md
sessions/scratchpad.md
sessions/agent_activity.md
sessions/archive/
```

The `sessions/` directory structure and this README **should** be committed, so anyone cloning the repo knows the convention exists.

---

## Quick Setup

1. Create the directory: `mkdir -p sessions/archive`
2. Add the gitignore rules above to your project's `.gitignore`
3. Create empty starter files:
   - `sessions/session_memory.md` — add a header: `# Session Memory`
   - `sessions/scratchpad.md` — add a header: `# Scratchpad` and `## Open Notes`
   - `sessions/agent_activity.md` — add a header: `# Agent Activity` and `## Recent Activity`
4. Reference `/gogogo` (session start) and `/wrapup` (session end) in your project's instruction file

That's it. No installation, no configuration, no dependencies.
