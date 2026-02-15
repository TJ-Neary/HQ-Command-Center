# System Architecture

> Technical architecture documentation for _HQ — AI-Augmented Project Command Center.
> Follows the [C4 Model](https://c4model.com/) with Mermaid.js diagrams.

---

## 1. System Context (C4 Level 1)

How _HQ fits into a development workflow, coordinating across projects and AI agents on different platforms.

```mermaid
C4Context
    title System Context — _HQ Command Center

    Person(dev, "Developer", "Manages projects with AI coding agents")

    System(hq, "_HQ", "Central coordination hub — session management, multi-agent orchestration, standards distribution, drift detection, project scaffolding")

    System_Ext(eng, "Engineering Agent", "Primary coding agent — implementation, standards, commits, security")
    System_Ext(res, "Research Agent", "Web research, document analysis, image generation, content creation")
    System_Ext(bg, "Background Ops Agent", "Async tasks, code review, sandboxed execution")
    System_Ext(deep, "Deep Research Agent", "Search, massive context analysis, multimodal analysis")
    System_Ext(github, "GitHub", "Repository hosting — public, showcase, and private repos")
    System_Ext(projects, "Project Portfolio", "Registered projects across multiple domains")

    Rel(dev, hq, "Operates via commands", "/gogogo, /wrapup, /publish")
    Rel(hq, eng, "Dispatches engineering tasks", "Filesystem + CLI")
    Rel(hq, res, "Dispatches research tasks", "Message board + scratchpad")
    Rel(hq, bg, "Dispatches background tasks", "Async via scratchpad")
    Rel(hq, deep, "Dispatches deep research", "Message board + research/ files")
    Rel(hq, github, "Publishes repos, updates profile", "gh CLI")
    Rel(hq, projects, "Syncs standards, detects drift", "YAML registries + Jinja2")
    Rel(eng, projects, "Implements, tests, commits", "Git + Python")
    Rel(res, projects, "Researches, generates content", "Browser + APIs")
```

---

## 2. Container Architecture (C4 Level 2)

The logical containers that compose _HQ. Each container is a functional subsystem implemented as files and scripts — no servers, no databases.

```mermaid
C4Container
    title Container Diagram — _HQ Command Center

    Person(dev, "Developer", "Operates _HQ through session commands")

    System_Boundary(hq, "_HQ Command Center") {
        Container(commands, "Command System", "Markdown protocols", "Session lifecycle: /gogogo, /wrapup — extensible with /publish, /board-meeting, etc.")
        Container(sessions, "Session Management", "Markdown files", "session_memory.md, scratchpad.md, agent_activity.md — file-based state protocol")
        Container(standards, "Standards Engine", "Markdown + YAML", "Normative rules: security, conventions, commit format, TDD, project structure")
        Container(sync, "Sync System", "YAML + Python", "MANIFEST + SYNC_STATUS registries — version tracking, drift detection, visibility enforcement")
        Container(scaffold, "Project Scaffolding", "Python + Jinja2", "Profile-based generation with post-generation hooks")
        Container(portfolio, "Portfolio Tracker", "Markdown", "Project status dashboard — health, phases, blockers")
        Container(messages, "Message Board", "Markdown", "Cross-project and cross-agent messaging with priority levels")
        Container(security, "Security Pipeline", "Bash + Python", "Multi-phase scanner, pre-publish security gate")
        Container(board, "Board of Directors", "Markdown protocols", "Multi-persona strategic advisory with deliberation protocol")
        Container(templates, "Template Engine", "Jinja2", "Versioned assets — project files, CI/CD, security scanners, agent integration")
        Container(publish, "Publication System", "Markdown + gh CLI", "Three-tier GitHub management — public, showcase, private")
    }

    Rel(dev, commands, "Invokes", "Slash commands")
    Rel(commands, sessions, "Reads/writes state", "At session start and end")
    Rel(commands, sync, "Triggers sync", "During /gogogo extensions")
    Rel(commands, portfolio, "Updates status", "During /gogogo and /wrapup extensions")
    Rel(commands, security, "Runs security gate", "During /publish and /commit")
    Rel(commands, board, "Convenes advisors", "During /board-meeting")
    Rel(commands, publish, "Audits and publishes", "During /publish")
    Rel(sync, standards, "Distributes", "Version-checked assets")
    Rel(sync, messages, "Posts drift alerts", "When versions diverge")
    Rel(scaffold, templates, "Renders", "Jinja2 templates into project files")
    Rel(scaffold, sync, "Registers", "New projects in sync registry")
    Rel(sessions, commands, "Provides context", "Session memory loaded at startup")
    Rel(publish, security, "Requires gate pass", "Before any public push")
```

---

## 3. Framework Directory Structure

```
_HQ/
├── CLAUDE.md.template       <- AI agent instruction template
├── GETTING_STARTED.md       <- Setup guide
├── commands/                <- Session lifecycle protocols
│   ├── gogogo.md            <- Session startup (with extension points)
│   └── wrapup.md            <- Session close (with extension points)
├── sessions/                <- File-based state protocol
│   ├── README.md            <- Protocol documentation
│   ├── session_memory.md    <- Persistent cross-session log (gitignored)
│   ├── scratchpad.md        <- Ephemeral inter-agent notes (gitignored)
│   └── agent_activity.md    <- Agent coordination log (gitignored)
├── me/                      <- Developer context (user-created)
│   ├── persona.md           <- Background, skills, preferences
│   └── plan.md              <- Current goals and priorities
├── standards/               <- Engineering standards (Phase 2)
├── templates/               <- Jinja2 project templates (Phase 2)
├── scaffold/                <- Project generator (Phase 2)
├── guides/                  <- Best practices (Phase 3)
├── messages/                <- Cross-project communication (Phase 4)
├── portfolio/               <- Project status tracking (Phase 4)
└── ARCHITECTURE.md          <- You are here
```

---

## 4. Key Design Decisions

| Decision | Choice | Why | Alternatives Considered |
|----------|--------|-----|------------------------|
| Data storage | YAML registries + Markdown | Zero dependencies, human-readable, git-friendly, editable by AI agents | SQLite (overhead for file-based coordination), JSON (less readable) |
| Template engine | Jinja2 | Industry standard, supports conditionals and loops, Python-native | Cookiecutter (too opinionated), Copier (extra dependency), string formatting (insufficient) |
| Multi-platform agent coordination | Convention-based (shared files) | No agent has real-time file detection; polling conventions are reliable across all platforms | WebSocket notifications (over-engineered), database locks (wrong abstraction), single-platform (limits capability) |
| Standards enforcement | Version-tracked sync with drift detection | Projects evolve independently; central enforcement would be brittle | Git submodules (merge conflicts), monorepo (projects are independent), copy-paste (no tracking) |
| Security scanning | Multi-phase Bash + Python + local LLM | Catches secrets, PII, private paths, commercial markers in one pass before any publish | Pre-commit hooks only (misses non-git content), commercial tools (cost, vendor lock-in) |
| GitHub publication | Three-tier model (Public/Showcase/Private) | Maximizes portfolio visibility while protecting commercial IP | All-public (IP risk), all-private (no visibility), per-file access control (not supported) |
| Agent platform selection | Best-of-breed per task type | Each platform has distinct strengths; multi-platform approach maximizes capability coverage | Single platform (capability gaps), two platforms (misses lanes) |

---

## 5. Data Flow — Session Lifecycle

The primary operational flow when a developer starts a work session.

```mermaid
flowchart TD
    A["/gogogo — Session Start"] --> B[Load Context]
    B --> B1[Read persona + plan]
    B --> B2[Read session memory]
    B --> B3[Read project instructions]

    B1 & B2 & B3 --> C[Git Status Check]
    C --> D{Uncommitted changes?}
    D -->|Yes| D1[Prompt: commit, stash, or continue]
    D -->|No| E[Run Extensions]

    D1 --> E
    E --> E1{Extensions configured?}
    E1 -->|Yes| E2[Sync / Messages / Drift / Publication checks]
    E1 -->|No| F[Present Session Status]
    E2 --> F

    F --> G["Ready — What would you like to work on?"]
```

---

## 6. Data Flow — Standards Distribution

How engineering standards propagate from _HQ to all registered projects, with visibility enforcement.

```mermaid
flowchart LR
    A[Standard updated in _HQ] --> B[Version bumped in registry]
    B --> C[Project runs session sync]
    C --> D{Project version < current?}
    D -->|Yes| E{Visibility check}
    D -->|No| F[No action needed]
    E -->|Public project + private asset| G[Skip — never sync private to public]
    E -->|Eligible| H{Sync mode?}
    H -->|Manual| I[Present changes for human review]
    H -->|Auto| J[Render template and apply]
    I --> K[Update project sync record]
    J --> K
    K --> L[Record sync in registry]
```

---

## 7. Data Flow — Cross-Project Communication

How projects communicate through _HQ without requiring the developer to relay information.

```mermaid
flowchart TD
    A[Drift detected in Project A] --> B[Drift scanner posts message to Board]
    B --> C[Message tagged with recipient + priority]
    C --> D[Project B runs /gogogo]
    D --> E[Session sync reads message board]
    E --> F{Message addressed to Project B?}
    F -->|Yes| G[Present to developer with action required]
    F -->|No| H[Skip silently]
    G --> I[Developer responds: ADOPTED / DECLINED / DEFERRED]
    I --> J[Response recorded on message board]
    J --> K[Next drift scan sees resolution]
```

---

## 8. Component Interaction — Multi-Agent Coordination

How multiple AI agents coordinate through _HQ's shared infrastructure without real-time communication.

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant Eng as Engineering Agent
    participant HQ as _HQ Shared Files
    participant Res as Research Agent
    participant BG as Background Ops Agent

    Dev->>Eng: /gogogo (start engineering session)
    Eng->>HQ: Read session memory + scratchpad
    HQ-->>Eng: Previous context + all agent notes
    Eng-->>Dev: Session ready — priorities + status presented

    Dev->>Eng: Implementation task
    Eng->>HQ: Log activity + post cross-project messages

    Note over Dev: Dispatches background task

    Dev->>BG: Async code review task
    BG->>HQ: Read scratchpad for context
    BG->>HQ: Write review findings to scratchpad

    Note over Dev: Switches to research agent

    Dev->>Res: Research task
    Res->>HQ: Read scratchpad for engineering notes
    Res->>HQ: Write research findings to scratchpad

    Note over Dev: Returns to engineering agent

    Dev->>Eng: Continue implementation
    Eng->>HQ: Mid-session check — read scratchpad
    HQ-->>Eng: All agent findings available

    Dev->>Eng: /wrapup (end session)
    Eng->>HQ: Update session memory + agent activity
    Eng-->>Dev: Handoff summary for next session
```

---

## 9. Security Posture

| Concern | Approach |
|---------|----------|
| Secrets detection | Multi-phase scanner checks for API keys, tokens, passwords, .env files, private key patterns |
| PII protection | Scanner detects names, emails, phone numbers, addresses |
| Private content isolation | Visibility enforcement — private assets never sync to public projects |
| Git history hygiene | Pre-publish gate scans commit history for previously committed secrets |
| Content classification | Three-level system (public, context-only, private) controls what AI agents can read and output |
| Cross-platform security | All agents operate under the same security standards regardless of platform |

---

*This document describes the system architecture. See [GETTING_STARTED.md](GETTING_STARTED.md) for setup instructions and [README.md](README.md) for an overview.*

*Copyright 2026 TJ Neary. All Rights Reserved.*
