# BMad Game Dev Studio (BMGD) — Project Overview

**Generated:** 2026-04-02
**Version:** 0.2.2
**License:** MIT

## Executive Summary

BMAD Game Dev Studio (BMGD) is a BMad Method module that brings structured, AI-assisted game development workflows to multiple game engines. It provides 7 specialized AI agents, 20+ workflows across 4 development phases, and engine-specific knowledge files for Unity, Unreal, Godot, and Phaser. The module is installed via the BMad Method installer and integrates with Claude Code for AI-assisted game development.

## Project Classification

| Property | Value |
|----------|-------|
| **Repository Type** | Monolith |
| **Project Type** | Library / BMAD Module |
| **Primary Language** | JavaScript (Node.js >=22), YAML, Markdown |
| **Architecture** | Data-driven knowledge-fragment system |
| **Package Name** | `bmad-game-dev-studio` |
| **Registry** | npm (private) |

## Technology Stack

| Category | Technology | Version |
|----------|-----------|---------|
| Runtime | Node.js | >=22.0.0 |
| Docs Site | Astro + Starlight | ^5.16.0 / ^0.37.0 |
| Linting | ESLint + Prettier + markdownlint | ^9.33 / ^3.7 / ^0.19 |
| Testing | Jest + c8 | ^30.0 / ^10.1 |
| Git Hooks | Husky + lint-staged | ^9.1 / ^16.1 |
| Build | Custom `tools/build-docs.mjs` | — |
| Content | YAML + Markdown | — |
| Validation | Zod | ^3.25 |

## Architecture Overview

BMGD uses a **modular, knowledge-fragment-based architecture** with five integration layers:

1. **Engine Knowledge** (`src/workflows/3-technical/.../knowledge/`) — Deep architecture guides per engine
2. **Decision Catalog** (`decision-catalog.yaml`) — Engine options with `pairs_with` relationships
3. **Architecture Patterns** (`architecture-patterns.yaml`) — Game pattern categories with engine recommendations
4. **Engine MCPs** (`engine-mcps.yaml`) — AI dev tool (MCP server) recommendations per engine
5. **Testing Knowledge** (`src/gametest/knowledge/`) — Engine-specific testing patterns + 14 generic testing guides

Workflows are **engine-agnostic in structure, engine-aware in data loading**. No conditional engine logic exists — the system is entirely data-driven.

## Agents (7)

| Agent | Directory | Role |
|-------|-----------|------|
| Game Architect | `gds-agent-game-architect` | System architecture and technical design |
| Game Designer | `gds-agent-game-designer` | Game design and mechanics |
| Game Developer | `gds-agent-game-dev` | Code implementation |
| Game QA | `gds-agent-game-qa` | Testing and quality assurance |
| Scrum Master | `gds-agent-game-scrum-master` | Sprint planning and tracking |
| Solo Dev | `gds-agent-game-solo-dev` | Full-stack rapid development |
| Tech Writer | `gds-agent-tech-writer` | Documentation |

## Workflow Phases

| Phase | Directory | Workflows |
|-------|-----------|-----------|
| 1 - Preproduction | `1-preproduction/` | Brainstorm, Game Brief, Research |
| 2 - Design | `2-design/` | PRD, GDD, Narrative, UX Design |
| 3 - Technical | `3-technical/` | Architecture, Epics/Stories, Project Context, Implementation Readiness |
| 4 - Production | `4-production/` | Sprint Planning, Story Creation, Dev Story, Code Review, Retrospective |
| Game Testing | `gametest/` | E2E Scaffold, Performance, Playtest, Test Automation, Test Design, Test Framework, Test Review |
| Quick Flow | `gds-quick-flow/` | Quick Dev, Quick Spec |

## Engine Support

| Engine | Knowledge | Testing | MCP Config | Setup Guide | module.yaml |
|--------|-----------|---------|------------|-------------|-------------|
| Unity | Yes | Yes | Yes | Yes | Yes |
| Unreal | Yes | Yes | Yes | Yes | Yes |
| Godot | Yes | Yes | Yes | Yes | Yes |
| Phaser | Yes | — | — | — | — |
| **Roblox** | **No** | **No** | **No** | **No** | **No** |

## Links

- [Source Tree Analysis](./source-tree-analysis.md)
- [Development Guide](./development-guide.md)
- [Documentation Site](./index.md)
