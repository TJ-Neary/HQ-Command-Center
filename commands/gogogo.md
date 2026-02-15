# /gogogo — Session Startup

Run this at the start of every AI coding session. It loads project context, checks git status, and presents current priorities so the agent can work effectively from the first prompt.

Works with any LLM coding agent that reads markdown instructions (Claude Code, Gemini, Codex, Cursor, etc.).

---

## Step 1: Confirm Model

Ask which model is currently active:

```
Which model am I running on? (e.g., Claude Opus, Claude Sonnet, Gemini 2.0 Flash, etc.)
```

Record the response for session awareness. This helps with:
- Understanding current capability set (different models have different strengths)
- Work tracking (documenting which model performed which work)
- Coordination across agents (if different models handle different task types)

Once confirmed, proceed to load context.

---

## Step 2: Load Context

Read these files to understand who you're working with and what's happening:

1. **`me/persona.md`** — Who the developer is, their background, and goals
2. **`me/plan.md`** — Current priorities, active goals, and constraints
3. **`sessions/session_memory.md`** — What happened in recent sessions

Internalize this context but don't summarize it verbosely. Just note the current phase and this week's priorities.

**Customization:** Update the file paths above to match your project structure. The key principle is: load enough context for the agent to understand *who* it's working with, *what* the current priorities are, and *what happened last time*.

---

## Step 3: Detect Environment

**If shell commands are available (e.g., Claude Code, Codex, Gemini CLI):**
- Continue to Step 4 (Git Status)

**If shell commands are NOT available (e.g., Claude.ai, ChatGPT):**
- Skip to Step 5 (Extensions)

---

## Step 4: Git Status (CLI agents only)

Check if we're in a project directory:

```bash
# Check for git repo
git rev-parse --is-inside-work-tree 2>/dev/null
```

**If in a git repo:**

```bash
git status
git log -5 --oneline
```

**If there are uncommitted changes**, ask:
- "I see uncommitted changes. Should I commit them now, stash them, or continue without committing?"

**If behind remote:**
- Offer to pull: `git pull`

**Load project context:**
- If `CLAUDE.md` (or equivalent project instruction file) exists in current directory, read it
- If `DevPlan.md` exists, note the current phase

---

## Step 5: Extensions

This is where you add project-specific or organization-specific startup logic. The core session protocol above handles universal needs (context, git, priorities). Extensions handle everything specific to your workflow.

**Common extensions (uncomment or add what you need):**

```markdown
### Cross-Project Sync
# If you manage multiple projects from a central hub:
# - Check a sync registry for asset version drift
# - Pull updated standards/templates from the hub
# - Push contributions back from this project
# - Update portfolio tracking

### Message Board
# If you use a cross-project or cross-agent communication system:
# - Read message board for messages addressed to this project or agent
# - Present required actions first, then review items, then informational
# - Ask if any messages should be addressed now

### Standards Drift Check
# If you maintain shared engineering standards:
# - Compare this project's adopted versions against current versions
# - Flag any required standards that haven't been adopted yet
# - Report drift summary

### Publication Readiness
# If you publish to GitHub or other platforms:
# - Audit required files (README, LICENSE, ARCHITECTURE, etc.)
# - Check badge accuracy, repo description, topics
# - Flag stale showcase repos
```

Each extension should be self-contained — it either runs or is skipped based on whether the required files/tools exist. See the `GETTING_STARTED.md` customization guide for how to build extensions.

---

## Step 6: Present Status

After loading context, present a brief status and ask what to work on:

```
Session ready!

**Current Phase:** [from plan.md or DevPlan.md]
**Top Priorities:**
1. [Priority 1]
2. [Priority 2]
3. [Priority 3]

**Last Session:** [Brief summary from session_memory.md]

**Git Status:** [Clean / X uncommitted changes / Not in a repo]
[Extension output, if any — drift flags, messages, etc.]

What would you like to work on?
```

---

## Mid-Session Convention

During the session (not just at startup), re-read `sessions/scratchpad.md` before starting any new major task block. No AI agent has real-time file-change detection — you only see updates when you actively read. This convention ensures inter-agent handoff notes aren't missed when multiple agents are active simultaneously.

---

## Notes

- This command works in both chat-based and CLI-based AI agents
- In chat-only agents, git operations and CLI-dependent extensions are skipped automatically
- Always load context first, then project context if applicable
- Keep the status presentation brief — the developer has the full docs if they need details
- Extensions are optional — the core protocol (Steps 1-4, 6) works standalone
