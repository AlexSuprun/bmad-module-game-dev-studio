# AI Game Development with Roblox, VS Code, Claude Code & BMAD

**Audience:** Experienced backend developer / systems architect learning Roblox game dev
**Stack:** Roblox Studio · Luau · VS Code · Rokit · Rojo · Wally · Claude Code · BMAD Game Dev Studio
**Date:** 2026-04-04

---

## Learning Path Overview

This documentation set covers the complete workflow for building Roblox games professionally — from understanding the platform architecture to running AI-assisted sprints with the BMAD Game Dev Studio module.

Each phase builds on the previous. Start at Phase 1 if you're new to Roblox. Jump to Phase 6 if you're primarily here for the Claude Code + BMAD workflow.

---

## Phases

### Phase 1 — Platform Foundations
> The Roblox engine: how it works, what makes it different, core mental models

| Module | Description |
|--------|-------------|
| [1.1 Roblox Engine & DataModel](1-platform-foundations/1.1-roblox-engine-datamodel.md) | DataModel hierarchy, services, instance system, RunService lifecycle |
| [1.2 Client-Server Model & Replication](1-platform-foundations/1.2-client-server-replication.md) | Server authority, filtering enabled, what replicates and how |
| [1.3 Luau Language](1-platform-foundations/1.3-luau-language.md) | Luau vs Lua 5.1, type system, OOP patterns, module system |

---

### Phase 2 — Dev Toolchain Setup
> Professional workflow: VS Code, filesystem sync, package management, linting

| Module | Description |
|--------|-------------|
| [2.1 VS Code & Luau LSP](2-dev-toolchain/2.1-vscode-luau-lsp.md) | Extension setup, diagnostics, go-to-definition, sourcemap integration |
| [2.2 Rokit, Rojo & Filesystem Sync](2-dev-toolchain/2.2-rokit-rojo-sync.md) | Toolchain version management, Rojo project config, live sync pipeline |
| [2.3 Wally, Selene, StyLua & CI/CD](2-dev-toolchain/2.3-wally-selene-stylua-cicd.md) | Package management, linting, formatting, CI/CD pipeline |

---

### Phase 3 — Architecture Patterns
> How to structure a Roblox codebase for scale and maintainability

| Module | Description |
|--------|-------------|
| [3.1 Service-Controller Pattern](3-architecture-patterns/3.1-service-controller.md) | Server services, client controllers, bootstrap pattern, singletons |
| [3.2 RemoteEvent & RPC Patterns](3-architecture-patterns/3.2-remote-events-rpc.md) | RemoteEvent, RemoteFunction, UnreliableRemoteEvent, five-step validation |
| [3.3 State Machine Architecture](3-architecture-patterns/3.3-state-machines.md) | Game flow states, per-entity states, transitions, invalid state prevention |
| [3.4 ECS with Matter & Jecs](3-architecture-patterns/3.4-ecs-matter-jecs.md) | Entity-Component-System, when to use ECS, Matter vs Jecs comparison |

---

### Phase 4 — Data & Platform APIs
> Persistence, cross-server communication, external tooling

| Module | Description |
|--------|-------------|
| [4.1 DataStore & ProfileStore](4-data-and-apis/4.1-datastore-profilestore.md) | DataStore API, session locking, ProfileStore setup, schema design |
| [4.2 Cross-Server Communication](4-data-and-apis/4.2-cross-server-communication.md) | MessagingService, MemoryStoreService, TeleportService, place architecture |
| [4.3 Open Cloud & External APIs](4-data-and-apis/4.3-open-cloud-external-apis.md) | Open Cloud REST API, HttpService, MarketplaceService, monetization hooks |

---

### Phase 5 — Performance & Security
> Building games that run well and can't be cheated

| Module | Description |
|--------|-------------|
| [5.1 Performance Optimization](5-performance-and-security/5.1-performance-optimization.md) | Frame budget, object pooling, event connections, StreamingEnabled, LOD |
| [5.2 Anti-Cheat Architecture](5-performance-and-security/5.2-anti-cheat-architecture.md) | Server validation, physics ownership, network ownership, exploit patterns |
| [5.3 Network Replication Optimization](5-performance-and-security/5.3-network-replication.md) | Replication costs, ByteNet, buffer encoding, NPC replication strategies |

---

### Phase 6 — AI-Powered Development
> Claude Code, Roblox MCP, and BMAD Game Dev Studio workflow

| Module | Description |
|--------|-------------|
| [6.1 Claude Code + Roblox MCP Setup](6-ai-powered-development/6.1-claude-code-roblox-mcp.md) | MCP server install, Studio integration, capabilities and limitations |
| [6.2 CLAUDE.md & AI Context Engineering](6-ai-powered-development/6.2-claude-md-context-engineering.md) | Crafting CLAUDE.md for Roblox, Luau rules, service patterns, Rojo conventions |
| [6.3 BMAD Game Dev Studio Workflow](6-ai-powered-development/6.3-bmad-game-dev-workflow.md) | The 7 agents, 4 phases, sprint workflow, from brief to shipped feature |

---

### Phase 7 — Game Dev Mindset
> Mental model shifts for backend developers entering game development

| Module | Description |
|--------|-------------|
| [7.1 Tick-Based Simulation vs Request-Response](7-game-dev-mindset/7.1-tick-vs-request-response.md) | Game loop, frame budget, spatial programming, physics as a system |
| [7.2 Live Ops, Monetization & Economy Design](7-game-dev-mindset/7.2-live-ops-monetization.md) | Rolling updates, feature flags, game passes, developer products, DevEx |

---

## Reference Documents

| Document | Description |
|----------|-------------|
| [Glossary](glossary.md) | All key terms, relationships, and common errors |
| [Cheat Sheet](cheat-sheet.md) | Quick-reference: commands, patterns, config snippets |

---

## Key Sources

- Roblox Creator Docs: https://create.roblox.com/docs
- Luau Language: https://luau.org
- Rojo: https://rojo.space/docs/v7/
- Rokit: https://github.com/rojo-rbx/rokit
- Wally: https://wally.run
- Matter ECS: https://matter-ecs.github.io/matter/
- BMAD Game Dev Studio Module: `/src/` (this repo)
- Roblox MCP Server: https://devforum.roblox.com/t/introducing-the-open-source-studio-mcp-server/3649365
