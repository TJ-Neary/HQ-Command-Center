# /wrapup — Session Close

Run this at the end of every AI coding session to ensure clean handoff to the next session (or the next agent).

---

## Step 1: Detect Environment

**If shell commands are available (e.g., Claude Code, Codex, Gemini CLI):**
- Continue to Step 2 (Git Cleanup)

**If shell commands are NOT available (e.g., Claude.ai, ChatGPT):**
- Skip to Step 3 (Update Session Memory)

---

## Step 2: Git Cleanup (CLI agents only)

**If in a git repository with uncommitted changes:**

```bash
git status
```

Ask if the developer wants to commit:
- "There are uncommitted changes. Should I commit them? If yes, what commit message?"

If yes:
```bash
git add -A
git commit -m "<message>"
git push
```

Verify push succeeded:
```bash
git status  # Should show "up to date with origin"
```

---

## Step 3: Update Session Memory

Append an entry to `sessions/session_memory.md`:

```markdown
---
## [Date] — [Agent: CCT | AG | CDX | Gem] — [Brief Title]
**Project:** [project name]

**What was done:**
- [Item 1]
- [Item 2]

**Files changed:**
- `path/to/file.md` — [what changed]

**Key decisions:**
- [Any decisions made during session]

**Open items / handoff notes:**
- [Anything the next session or agent should know]
---
```

**Agent tags:** Use a consistent tag to identify which agent wrote each entry. This matters when multiple agents work on the same project. Examples: `[Agent: CCT]` for Claude Code, `[Agent: AG]` for a sidebar agent, `[Agent: Gem]` for Gemini CLI.

**Rotation check:** If `session_memory.md` exceeds 50KB or has entries older than 4 weeks, archive old entries to `sessions/archive/YYYY-MM.md`. Keep the main file focused on recent context.

---

## Step 4: Update Plan (If Needed)

Check if anything in the session affects your plan or goals file (e.g., `me/plan.md`):

- Did we complete a priority item? Check it off
- Did we discover a blocker? Add to blockers section
- Did priorities shift? Update the priority list
- Did we move to a new phase? Update phase status

Only update if there's a meaningful change. Don't update just for the sake of updating.

---

## Step 5: Documentation Review

Before committing, check whether project documentation needs updating based on this session's work.

**Scan for these files** (check whichever exist in the project):

| Document | Update When... |
|----------|---------------|
| `README.md` | Features, installation steps, or project description changed |
| `DevPlan.md` | Status changed, phases completed, new decisions made, roadmap items added/resolved |
| `CLAUDE.md` | New commands, conventions, file structure, or workflow changes |
| `CHANGELOG.md` | Any user-visible changes (if the project maintains one) |

**How to check:**
1. Review the list of files created/modified this session
2. For each doc above that exists, ask: "Would someone reading this doc have an accurate picture of the project after today's changes?"
3. If not, update it before committing

**Present findings:**
```
Documentation review:
  [OK] README.md — current
  [!!] DevPlan.md — needs update (Phase 2 items completed)
  [OK] CLAUDE.md — current
```

Update any flagged docs, then proceed to commit.

**Note:** Don't update docs just to change a "Last updated" date. Only update when content is actually stale.

---

## Step 6: Extensions

This is where you add project-specific or organization-specific close-out logic. Common extensions:

```markdown
### Portfolio Update
# If you track project status across a portfolio:
# - Update portfolio tracker with new status, test counts, technologies
# - Update status dashboard with health, phase, blockers

### Publication Impact Check
# If you publish to GitHub or other platforms:
# - Check if this session's changes affect public-facing repos
# - Flag showcase repos that may need updating
# - Update badges if test counts changed significantly

### Cross-Project Sync
# If you manage shared standards:
# - Record sync state updates
# - Post cross-project messages if this session's work affects other projects
```

---

## Step 7: Session Summary

Provide a brief summary:

```
Session complete!

**Accomplished:**
- [What was done]

**Files Created/Modified:**
- [List]

**Key Decisions:**
- [If any]

**Next Session:**
- [Clear, specific handoff — what to work on next]

Session memory updated. Ready to pick up where we left off.
```

---

## Step 8: Improve Startup (Optional)

If anything learned during this session should be added to `/gogogo`:
- New context files that should be loaded
- Project-specific setup steps discovered
- Patterns that would help future sessions

Offer to update `commands/gogogo.md` if improvements are identified.

---

## Notes

- This command works in both chat-based and CLI-based AI agents
- In chat-only agents, git operations are skipped automatically
- Session memory is critical for continuity — always update it
- Keep summaries brief but actionable
- The handoff message should let the next session start immediately
