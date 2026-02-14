![_HQ Command Center](assets/social-preview.png)

![Showcase](https://img.shields.io/badge/type-showcase-purple?style=flat-square)
![Python](https://img.shields.io/badge/python-3.12+-blue?style=flat-square)
![Projects](https://img.shields.io/badge/projects_managed-13-brightgreen?style=flat-square)
![Tests](https://img.shields.io/badge/portfolio_tests-~6,700-brightgreen?style=flat-square)
![Agents](https://img.shields.io/badge/AI_agents-4-orange?style=flat-square)
![Instances](https://img.shields.io/badge/agent_instances-57-orange?style=flat-square)

# _HQ — AI-Augmented Project Command Center

A multi-agent coordination system that manages 13 software projects across 4 AI agents from 3 competing platforms (Anthropic Claude, OpenAI Codex, and Google Gemini) — 57 agent instances connected through a shared infrastructure layer. _HQ gives all agents shared awareness of all projects, enables cross-project communication without human relay, and maintains consistent engineering standards across the entire portfolio — automatically.

Built to solve a real problem: running a portfolio of AI/ML projects in parallel with multiple AI assistants, while maintaining consistent quality, security, and documentation standards across all of them — without the developer becoming the bottleneck for coordination.

---

## What Makes This Different

Most developers use one AI tool in one project at a time. _HQ coordinates **4 AI agents from 3 competing platforms** across **13 projects simultaneously** — **57 agent instances** sharing awareness through a common infrastructure layer, with each agent aware of what the others have done, what every project needs, and what the developer's strategic priorities are.

The agents don't just respond to prompts — they proactively detect problems, distribute updates, flag drift, and coordinate handoffs through a shared infrastructure layer. The developer focuses on decisions that require human judgment; everything else runs autonomously.

This isn't a framework or a library. It's an operational system that runs every day.

---

## Core Capabilities

### Multi-Platform AI Agent Coordination

4 AI agents from 3 competing platforms operate concurrently across all projects with defined domain lanes:

- **Engineering agent** (Anthropic Claude) — Code implementation, standards enforcement, security scanning, git operations, cross-project sync
- **Research agent** (Google Gemini sidebar) — Web research, document analysis, image generation, browser automation, content creation
- **Background operations agent** (OpenAI Codex) — Async background tasks, GPT-perspective code review, sandboxed execution
- **Deep research agent** (Google Gemini CLI) — Native Google Search, massive context analysis (1M+ tokens), multimodal file reading (images, PDFs, audio)

All 4 agents share awareness through a common infrastructure: session memory that persists across conversations, an inter-agent scratchpad for real-time handoff notes, and an activity log so each agent knows what the others accomplished. When one agent discovers something relevant to another's domain, it posts to a shared message board rather than requiring the developer to relay the information.

No agent has real-time file-change detection, so the system uses **convention-based coordination** — structured check-in protocols at session start, mid-session, and session end ensure nothing is missed even when multiple agents are active simultaneously.

### Cross-Project Awareness and Communication

Every project registered with _HQ automatically participates in a shared ecosystem:

- **Message Board** — Projects post messages to each other with priority levels (required, review, informational) and structured response protocols. A drift detection scan in one project can automatically notify affected projects, which pick up the message during their next session sync.
- **Bulletin System** — Durable announcements that persist until every project acknowledges them. Used for portfolio-wide policy changes, new standards adoption, and cross-cutting decisions.
- **Shared Status Dashboard** — A single-page view of all 13 projects showing current phase, health status, test counts, blockers, and next milestones. Updated automatically during every session sync. Used for weekly reviews and strategic planning.

No manual relay required. Projects communicate through _HQ the way teams communicate through Slack — except the "team members" are AI agents working in different projects.

### Automatic Standards Distribution and Best Practices Sharing

Engineering standards, guides, and templates are centrally managed and automatically distributed:

- **30+ versioned assets** covering security policy, code conventions, TDD requirements, CI/CD patterns, Apple Silicon optimization, logging, error handling, and more
- **Bi-directional sync** — _HQ pushes updates to projects, and projects contribute improvements back to _HQ for distribution to others
- **Drift detection** — A Python script scans all registered projects and identifies which ones are behind on standards. Drift alerts are posted to the message board automatically.
- **Visibility enforcement** — Private assets (commercially sensitive patterns, proprietary code) never sync to public repositories. The system checks project visibility before every sync operation.
- **Two sync modes** — Standards that require human judgment sync manually (the agent presents changes for review). Templates that can be safely re-rendered sync automatically.

When a better error handling pattern emerges in one project, it can be contributed to _HQ and distributed to all other projects — automatically, with version tracking, and with visibility rules that prevent IP leakage.

### Project Scaffolding and Compliance from Day Zero

New projects start fully compliant with all portfolio standards:

- **Profile-based generation** — Choose from CLI, API, ML pipeline, library, or evaluation profiles. Each generates a complete project skeleton with the right structure, dependencies, and configuration.
- **Template engine** — Jinja2 templates render project-specific files (pyproject.toml, CI workflows, security scanners, logging config, exception hierarchies) with the project's name, tech stack, and settings baked in.
- **Auto-registration** — New projects are automatically registered in the sync system, added to the portfolio tracker, and enrolled in standards distribution. No manual setup.
- **Post-generation hooks** — After scaffolding, the system runs security scans, verifies compliance, and creates the initial git commit.

A new project goes from zero to full standards compliance — security scanner, TDD configuration, CI pipeline, agent integration files, structured logging — in a single command.

### Strategic Advisory Board

A 5-persona advisory simulation that provides multi-perspective strategic counsel:

- **Career Strategist** — Market positioning, networking, opportunity evaluation
- **Technical Architect** — System design, technology selection, scalability decisions
- **Financial Advisor** — Runway management, income prioritization, investment decisions
- **HR Executive** — Organizational transformation, credential strategy, professional development
- **Military Mentor** — Leadership principles, operational discipline, transition strategy

Each advisor speaks from their distinct perspective, then the board issues a formal resolution with specific action items and accountability assignments. Available as a full board meeting or a quick daily standup format.

This isn't a chatbot with different prompts — it's a structured deliberation protocol where advisors build on each other's input and occasionally disagree, producing decisions that reflect genuine multi-perspective analysis.

### Security Pipeline

Security is enforced automatically, not optionally:

- **9-phase scanner** — Checks for API keys, tokens, passwords, .env files, private key patterns, PII (names, emails, phone numbers), internal path references, code injection patterns, and commercial sensitivity markers
- **LLM-powered commercial detection** — An Ollama-based scanner identifies proprietary algorithms, trade secrets, and commercially sensitive code patterns
- **Pre-publish gate** — Before any repository goes public or any showcase content is updated, the security pipeline runs and blocks publication if any check fails
- **Git history hygiene** — Scans commit history for previously committed secrets and flags them for remediation
- **Content classification** — Three-level system (public, context-only, private) controls what AI agents can read and what they can output to files in different project contexts

### GitHub Portfolio Management

A three-tier publication model maximizes visibility while protecting IP:

- **Public** — Full source code visible. Professional documentation, badges, architecture diagrams, CI pipelines. Demonstrates engineering capability openly.
- **Showcase** — Documentation-only "shadow" of a private project. Architecture diagrams, tech stack, feature descriptions, and results — without exposing source code. Proves competence without risking commercial value.
- **Private** — Internal only. No public presence.

The system automatically checks publication readiness during every session sync, generates missing documentation from templates, runs security gates before any publish operation, and keeps the developer's GitHub profile README synchronized with the current state of the portfolio.

---

## Feature Summary

| Feature | What It Does | Why It Matters |
|---------|-------------|----------------|
| **Multi-Platform Agent Coordination** | 4 AI agents (Claude + Codex + Gemini x2), 57 instances across 13 projects, shared awareness via message board and scratchpad | One developer operates at the throughput of a small team |
| **Cross-Project Communication** | Projects message each other with priority levels and structured responses | No human relay needed between projects or agents |
| **Automatic Standards Distribution** | 30+ versioned assets sync to all projects with drift detection | Best practices propagate portfolio-wide without manual effort |
| **Best Practices Sharing** | Projects contribute improvements back to _HQ for distribution to others | The whole portfolio improves when any single project innovates |
| **Shared Project Awareness** | Status dashboard, portfolio tracker, message board give every session full context | Any agent in any project knows the state of the entire portfolio |
| **Project Scaffolding** | Profile-based generation with auto-registration and compliance verification | New projects are fully compliant from the first commit |
| **Strategic Advisory Board** | 5-persona simulation with structured deliberation and formal resolutions | Strategic decisions get multi-perspective analysis on demand |
| **Security Pipeline** | 9-phase scanner + LLM commercial detection + pre-publish gate | Security is enforced automatically, not remembered manually |
| **GitHub Portfolio Management** | Three-tier publication with automated readiness checks and profile sync | Professional visibility with IP protection |
| **Session Continuity** | Shared memory, activity logs, scratchpad, structured startup/wrapup | Context survives across sessions, agents, and platforms |

---

## Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Language** | Python 3.12+ | Drift detection, security scanning, project scaffolding |
| **Templating** | Jinja2 | Project file generation, standards rendering |
| **Registries** | YAML (MANIFEST + SYNC_STATUS) | Asset versioning and cross-project sync tracking |
| **Documentation** | Markdown, Mermaid.js | Standards, guides, architecture docs, C4 diagrams |
| **AI Platforms** | Anthropic Claude, OpenAI Codex, Google Gemini (sidebar + CLI) | 4-agent coordination with platform-specific strengths |
| **CI/CD** | GitHub Actions (templated) | Distributed to all projects via template engine |
| **Security** | Custom 9-phase scanner, Ollama (local LLM) | Pre-commit and pre-publish security gates |
| **Platform** | macOS, Apple Silicon (M4 Max) | Local-first, no cloud dependencies for core operations |

---

## Portfolio Scale

_HQ currently coordinates:

- **13 registered projects** spanning AI/ML, voice processing, knowledge management, career tools, and web crawling
- **~6,700 tests** across the portfolio
- **48 versioned assets** (standards, guides, and templates) distributed and version-tracked
- **4 AI agents** across 3 platforms — **57 total agent instances** with shared awareness and domain-lane coordination
- **5 advisory personas** for strategic decision-making
- **3 publication tiers** managing public, showcase, and private repository visibility

---

## System Design

See **[ARCHITECTURE.md](ARCHITECTURE.md)** for the full C4 model with Mermaid.js diagrams covering system context, container architecture, session lifecycle, standards distribution flow, and multi-agent coordination sequences.

---

Copyright 2026 TJ Neary. All Rights Reserved.
