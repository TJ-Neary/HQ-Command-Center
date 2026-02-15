# Getting Started

> How to set up AI-augmented session management for your projects.
> Takes about 15 minutes. No dependencies to install.

---

## What You Get

After setup, your AI coding agents will:
- **Start every session informed** — the agent reads your goals, recent work, and project state before you say anything
- **End every session clean** — uncommitted work is handled, session notes are written, and the next session has a clear starting point
- **Coordinate across agents** — if you use multiple AI tools, they share state through files instead of requiring you to relay information

No servers. No databases. No dependencies. Just markdown files and conventions.

---

## Prerequisites

- Any LLM coding agent that can read and write files (Claude Code, Cursor, Codex, Gemini, Windsurf, etc.)
- A git repository (optional, but recommended)
- That's it

---

## Quick Start (5 minutes)

### 1. Copy the framework files into your project

```
your-project/
├── CLAUDE.md               <- Rename from CLAUDE.md.template, customize
├── commands/
│   ├── gogogo.md           <- Session startup protocol
│   └── wrapup.md           <- Session close protocol
└── sessions/
    ├── README.md            <- Protocol documentation
    └── .gitkeep
```

### 2. Set up session files (gitignored)

Create these files locally — they won't be committed to git:

```bash
# Create session files
echo "# Session Memory" > sessions/session_memory.md
echo -e "# Scratchpad\n\n## Open Notes" > sessions/scratchpad.md
echo -e "# Agent Activity\n\n## Recent Activity" > sessions/agent_activity.md
```

Add to your `.gitignore`:
```gitignore
sessions/session_memory.md
sessions/scratchpad.md
sessions/agent_activity.md
sessions/archive/
```

### 3. Customize CLAUDE.md

Open `CLAUDE.md.template`, rename it to `CLAUDE.md`, and fill in:
- Your project name and description
- File paths for your context files (persona, plan, etc.)
- Your communication and coding preferences

### 4. Create your context files

The framework reads two files to understand who you are and what you're working on:

**`me/persona.md`** — Your background and goals:
```markdown
# About Me

- Software engineer, 5 years experience
- Currently building a SaaS product for [domain]
- Strengths: backend systems, distributed architecture
- Learning: frontend, marketing, user research
```

**`me/plan.md`** — Your current priorities:
```markdown
# Current Plan

## This Week
1. Ship authentication feature
2. Fix performance regression in search
3. Write API documentation

## Active Blockers
- Waiting on design review for settings page

## Constraints
- Solo developer, 4 hours/day available
```

These can be as detailed or as minimal as you want. The agent uses them to prioritize suggestions and understand context — not to make decisions for you.

### 5. Run it

Start a new session with your AI agent and type:

```
/gogogo
```

The agent will read your context files, check git status, and present your priorities. When you're done:

```
/wrapup
```

The agent will commit changes, write session notes, and prepare a handoff for next time.

---

## How It Works

### The Session Lifecycle

Every session follows the same pattern:

```
/gogogo                         /wrapup
   |                               |
   v                               v
Load context                   Git cleanup
   |                               |
Check git status               Update session memory
   |                               |
Run extensions                 Update plan (if changed)
   |                               |
Present priorities             Review documentation
   |                               |
"What should we work on?"     Session summary + handoff
```

The startup and close protocols are defined in `commands/gogogo.md` and `commands/wrapup.md`. They're plain markdown instructions that any AI agent follows. You can read them, modify them, and extend them.

### How Agents Share State

There's no magic here. Agents share state through three plain markdown files:

| File | Purpose | Analogy |
|------|---------|---------|
| `session_memory.md` | What happened in past sessions | A shared work journal |
| `scratchpad.md` | Quick notes for the next agent or session | A sticky note on the monitor |
| `agent_activity.md` | Who's active and what they're touching | A sign-in sheet |

**No agent has real-time awareness of what other agents write.** They only see updates when they actively read the files. This is handled by convention:

- **At session start:** Read all three files (`/gogogo` does this automatically)
- **Mid-session:** Re-read `scratchpad.md` before starting any major new task
- **At session end:** Write to `session_memory.md` and `agent_activity.md` (`/wrapup` does this)

This is "convention over infrastructure" — instead of building a real-time notification system, we define when agents should check for updates. It works because:

1. Human-scale development doesn't need millisecond coordination
2. Any agent that can read files can participate (no SDKs, APIs, or platform lock-in)
3. The developer can read and edit the same files (humans are first-class participants)
4. Debugging is trivial — just open the file and read it

### How Context Compaction Works

AI agents have finite context windows. When a conversation gets long, older messages get compressed or dropped. This is a fundamental constraint of the technology, not a bug.

The session protocol handles this through **context reconstruction, not context persistence:**

1. **The conversation is disposable.** When a session ends, everything in the conversation is gone.
2. **The files are permanent.** Session memory, scratchpad, and project docs persist on disk.
3. **Every session starts clean.** `/gogogo` loads ~3-5 key files and the agent rebuilds understanding from scratch.
4. **Mid-session, the scratchpad catches drift.** If context gets compressed, the agent can re-read scratchpad and session memory to recover awareness.

This means:
- There's no "magic context" that lives only in conversation history
- Any agent can pick up where any other agent left off
- Switching between AI tools doesn't lose state
- If a session crashes, you lose at most the current session's work (which was on disk anyway)

The tradeoff is that agents need to be told to load context at the start of each session. That's what `/gogogo` automates.

---

## Customization Guide

### Adding Extensions to /gogogo

The `commands/gogogo.md` file has a Step 5 (Extensions) section with commented-out examples. To add your own:

1. Open `commands/gogogo.md`
2. Add your extension logic in Step 5
3. Each extension should check for its own prerequisites (e.g., "if `SYNC_STATUS.yaml` exists, run sync")

**Example: Adding a test status check**
```markdown
### Test Status
Run the test suite and report results:
\`\`\`bash
pytest --tb=short -q 2>/dev/null | tail -5
\`\`\`
If tests are failing, flag them prominently in the session status.
```

### Adding Extensions to /wrapup

Same pattern — the `commands/wrapup.md` file has a Step 6 (Extensions) section.

### Adapting for Non-Claude Agents

The framework uses `CLAUDE.md` as the project instruction filename because Claude Code reads it automatically. Other agents use different conventions:

| Agent | Instruction File | Adaptation |
|-------|-----------------|------------|
| Claude Code | `CLAUDE.md` | Works as-is |
| Cursor | `.cursorrules` | Copy relevant sections to `.cursorrules` |
| Codex | `AGENTS.md` | Rename or reference from `AGENTS.md` |
| Gemini (Antigravity) | `ANTIGRAVITY.md` | Create parallel file with agent-specific adjustments |
| Windsurf | `.windsurfrules` | Copy relevant sections |

The session files and commands work identically regardless of which agent reads them. Only the "how does the agent find its instructions" part differs.

### Scaling to Multiple Projects

If you manage multiple projects and want them to share standards, communicate, and track status across a portfolio:

- **Phase 2** of this framework adds standards, templates, and project scaffolding
- **Phase 4** adds cross-project sync, drift detection, and portfolio management

These are built on the same file-based protocol — no new infrastructure required. See the roadmap in `README.md`.

---

## FAQ

**Q: Does this only work with Claude?**
A: No. It works with any AI coding agent that can read markdown files. Claude Code reads `CLAUDE.md` automatically, but the commands and session files are agent-agnostic.

**Q: What if I only use one AI agent?**
A: The session management (`/gogogo`, `/wrapup`, `session_memory.md`) is still valuable for cross-session continuity. Remove the multi-agent sections from `CLAUDE.md` and the coordination sections from the session files.

**Q: How much context does this add to each session?**
A: Typically 2-5KB of text — `persona.md` + `plan.md` + recent session memory entries. This is a small fraction of modern context windows (100K-1M+ tokens).

**Q: What if my session memory gets huge?**
A: Archive entries older than 4 weeks to `sessions/archive/YYYY-MM.md`. The framework's `/wrapup` command includes a rotation check.

**Q: Can I use this with a team?**
A: The protocol is designed for a single developer with multiple AI agents, but the pattern works for small teams too. Each developer would have their own `me/` context files, and `session_memory.md` becomes a shared team log.
