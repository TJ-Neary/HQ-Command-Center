# System Architecture

> Technical architecture documentation for _HQ — AI-Augmented Project Command Center.
> Follows the [C4 Model](https://c4model.com/) with Mermaid.js diagrams.

---

## 1. System Context (C4 Level 1)

How _HQ fits into the broader development ecosystem, coordinating across projects and AI agents on different platforms.

```mermaid
C4Context
    title System Context — _HQ Command Center

    Person(dev, "Developer", "Portfolio owner managing 13 projects across multiple domains")

    System(hq, "_HQ", "Central coordination hub — multi-agent orchestration, standards distribution, drift detection, project scaffolding, portfolio tracking, strategic advisory")

    System_Ext(eng, "Engineering Agent", "Anthropic Claude — code, standards, commits, security, cross-project sync")
    System_Ext(res, "Research Agent", "Google Gemini sidebar — web research, document analysis, image generation, content creation")
    System_Ext(bg, "Background Ops Agent", "OpenAI Codex — async tasks, GPT code review, sandboxed execution")
    System_Ext(deep, "Deep Research Agent", "Google Gemini CLI — native search, massive context, multimodal analysis")
    System_Ext(github, "GitHub", "Repository hosting — public, showcase, and private repos")
    System_Ext(projects, "Project Portfolio", "13 registered projects (AI/ML, voice, knowledge, career, web)")
    System_Ext(apple, "Apple Ecosystem", "Calendar, Reminders — schedule and task integration")

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
        Container(commands, "Command System", "Markdown protocols", "Session lifecycle: /gogogo, /wrapup, /publish, /board-meeting, /daily-standup")
        Container(standards, "Standards Engine", "Markdown + YAML", "Normative rules: security, conventions, commit format, TDD, project structure")
        Container(sync, "Sync System", "YAML + Python", "MANIFEST + SYNC_STATUS registries — version tracking, drift detection, visibility enforcement")
        Container(scaffold, "Project Scaffolding", "Python + Jinja2", "Profile-based generation with post-generation hooks and auto-registration")
        Container(portfolio, "Portfolio Tracker", "Markdown", "Portfolio catalog + status dashboard — project health, phases, blockers")
        Container(messages, "Message Board", "Markdown", "Cross-project and cross-agent messaging with priority levels and structured responses")
        Container(security, "Security Pipeline", "Bash + Python + Ollama", "9-phase scanner, LLM commercial detection, pre-publish security gate")
        Container(sessions, "Session Management", "Markdown", "Session memory, agent activity log, inter-agent scratchpad, startup/wrapup protocols")
        Container(board, "Board of Directors", "Markdown protocols", "5-persona strategic advisory with structured deliberation and formal resolutions")
        Container(templates, "Template Engine", "Jinja2", "48 versioned assets — project files, CI/CD, security scanners, agent integration, architecture docs")
        Container(publish, "Publication System", "Markdown + gh CLI", "Three-tier GitHub management — public, showcase, private with automated readiness checks")
    }

    Rel(dev, commands, "Invokes", "Slash commands")
    Rel(commands, sync, "Triggers sync", "During /gogogo")
    Rel(commands, portfolio, "Updates status", "During /gogogo and /wrapup")
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

## 3. Key Design Decisions

| Decision | Choice | Why | Alternatives Considered |
|----------|--------|-----|------------------------|
| Data storage | YAML registries + Markdown | Zero dependencies, human-readable, git-friendly, editable by AI agents | SQLite (overhead for file-based coordination), JSON (less readable) |
| Template engine | Jinja2 | Industry standard, supports conditionals and loops, Python-native | Cookiecutter (too opinionated), Copier (extra dependency), string formatting (insufficient) |
| Multi-platform agent coordination | Convention-based (shared files) | No agent has real-time file detection; polling conventions are reliable across all platforms | WebSocket notifications (over-engineered), database locks (wrong abstraction), single-platform (limits capability) |
| Standards enforcement | Version-tracked sync with drift detection | Projects evolve independently; central enforcement would be brittle | Git submodules (merge conflicts), monorepo (projects are independent), copy-paste (no tracking) |
| Security scanning | Multi-phase Bash + Python + local LLM | Catches secrets, PII, private paths, commercial markers in one pass before any publish | Pre-commit hooks only (misses non-git content), commercial tools (cost, vendor lock-in) |
| Advisory system | Multi-persona simulation with deliberation protocol | Strategic decisions benefit from multiple perspectives; no scheduling overhead | Actual advisory board (premature at current scale), single-perspective analysis (blind spots) |
| GitHub publication | Three-tier model (Public/Showcase/Private) | Maximizes portfolio visibility while protecting commercial IP | All-public (IP risk), all-private (no visibility), per-file access control (not supported) |
| Agent platform selection | Claude (engineering) + Codex (background ops) + Gemini (research x2) | Each platform has distinct strengths; three-platform approach maximizes capability coverage while convention-based coordination manages complexity | Single platform (capability gaps), two platforms (misses async and deep research lanes) |

---

## 4. Data Flow — Session Lifecycle

The primary operational flow when a developer starts a work session. This runs every session across every project.

```mermaid
flowchart TD
    A["/gogogo — Session Start"] --> B[Load Context]
    B --> B1[Read persona + strategic plan]
    B --> B2[Read session memory]
    B --> B3[Read project instructions]

    B1 & B2 & B3 --> C[Git Status Check]
    C --> D{Uncommitted changes?}
    D -->|Yes| D1[Prompt: commit, stash, or continue]
    D -->|No| E[Bi-Directional Sync]

    D1 --> E
    E --> E1[Pull: HQ → Project]
    E1 --> E2{Asset versions current?}
    E2 -->|No| E3[Apply updates with visibility enforcement]
    E2 -->|Yes| E4[All synced assets up to date]
    E3 --> E5[Push: Project → HQ contributions]
    E4 --> E5

    E5 --> F[Update Portfolio + Status Board]
    F --> G[GitHub Readiness Check]
    G --> H[Check Message Board + Bulletins]
    H --> I{Messages needing response?}
    I -->|Yes| I1[Present by priority: required → review → informational]
    I -->|No| J[Present Session Status]
    I1 --> J

    J --> K["Ready — What would you like to work on?"]
```

---

## 5. Data Flow — Standards Distribution

How engineering standards propagate from _HQ to all registered projects, with visibility enforcement preventing private asset leakage to public repositories.

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

## 6. Data Flow — Cross-Project Communication

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

## 7. Security Posture

| Concern | Approach |
|---------|----------|
| Secrets detection | 9-phase scanner checks for API keys, tokens, passwords, .env files, and private key patterns |
| PII protection | Scanner detects names, emails, phone numbers, addresses; three-layer PII detection guide for projects |
| Private content isolation | Visibility enforcement in sync system — private assets never sync to public projects |
| Commercial sensitivity | Local LLM-powered scanner detects proprietary algorithms, trade secrets, and commercial markers |
| Git history hygiene | Pre-publish gate scans commit history for previously committed secrets; flags for remediation |
| Showcase IP protection | Three-tier publication model ensures source code never appears in showcase repositories |
| Content classification | Three-level system (public, context-only, private) controls what AI agents can read and output |
| Cross-platform security | All four agents operate under the same security standards regardless of platform |

---

## 8. Component Interaction — Multi-Agent Coordination

How four AI agents on three platforms coordinate through _HQ's shared infrastructure without real-time communication.

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant Eng as Engineering Agent (Claude)
    participant HQ as _HQ Shared Infrastructure
    participant Res as Research Agent (Gemini)
    participant BG as Background Ops (Codex)
    participant Deep as Deep Research (Gemini CLI)

    Dev->>Eng: /gogogo (start engineering session)
    Eng->>HQ: Read session memory + scratchpad
    HQ-->>Eng: Previous context + all agent notes
    Eng->>HQ: Run sync, update status board
    Eng-->>Dev: Session ready — priorities + messages presented

    Dev->>Eng: Implementation task
    Eng->>HQ: Log activity
    Eng->>HQ: Post cross-project message (if needed)

    Note over Dev: Dispatches background task

    Dev->>BG: Async code review task
    BG->>HQ: Read scratchpad for context
    BG->>HQ: Write review findings to scratchpad

    Note over Dev: Switches to research agent

    Dev->>Res: Research task
    Res->>HQ: Read scratchpad for engineering notes
    Res->>HQ: Write research findings
    Res->>HQ: Post to message board (if action needed for other projects)

    Note over Dev: Deep research needed

    Dev->>Deep: Deep analysis with web search
    Deep->>HQ: Read research/ files for prior context
    Deep->>HQ: Write comprehensive findings to research/ files

    Note over Dev: Returns to engineering agent

    Dev->>Eng: Continue implementation
    Eng->>HQ: Mid-session check — read scratchpad for new notes
    HQ-->>Eng: All agent findings available

    Dev->>Eng: /wrapup (end session)
    Eng->>HQ: Read scratchpad for final updates
    Eng->>HQ: Update session memory with full session summary
    Eng->>HQ: Commit all changes
    Eng-->>Dev: Handoff summary for next session
```

---

## 9. Strategic Advisory Board — Deliberation Flow

How the Board of Directors simulation produces multi-perspective strategic analysis.

```mermaid
flowchart TD
    A["/board-meeting — Topic Presented"] --> B[Load Context]
    B --> B1[Read strategic plan + portfolio status]
    B --> B2[Read project health dashboard]
    B --> B3[Read session history]

    B1 & B2 & B3 --> C[Stage Setting]
    C --> D[Brief: current situation + specific question]

    D --> E[Round Table — Each Advisor Speaks]
    E --> E1["Career Strategist: Market positioning + opportunity analysis"]
    E --> E2["Technical Architect: System design + technology decisions"]
    E --> E3["Financial Advisor: Runway + income prioritization"]
    E --> E4["HR Executive: Credentials + organizational strategy"]
    E --> E5["Military Mentor: Leadership + operational discipline"]

    E1 & E2 & E3 & E4 & E5 --> F[Cross-Talk and Debate]
    F --> G[Board Resolution]
    G --> G1[Decision summary]
    G --> G2[Action items with owners]
    G --> G3[Dissenting opinions noted]
    G --> G4[Follow-up timeline]
```

---

*This document describes the system architecture without exposing implementation details, private content, or proprietary configurations.*
*Copyright 2026 TJ Neary. All Rights Reserved.*
