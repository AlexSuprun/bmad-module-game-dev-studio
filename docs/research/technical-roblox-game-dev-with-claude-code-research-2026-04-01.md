---
stepsCompleted: [1, 2, 3, 4, 5, 6]
inputDocuments: []
workflowType: 'research'
lastStep: 1
research_type: 'technical'
research_topic: 'Roblox Game Development with Claude Code and BMAD'
research_goals: 'Set up Roblox dev workflow with Claude Code, understand capabilities and limitations, evaluate BMAD game-dev potential'
user_name: 'Alex Suprun'
date: '2026-04-01'
web_research_enabled: true
source_verification: true
---

# AI-Powered Roblox Game Development: Comprehensive Technical Research on Claude Code, MCP Integration, and BMAD Workflows

**Date:** 2026-04-01
**Author:** Alex Suprun
**Research Type:** Technical

---

## Executive Summary

The convergence of AI-assisted coding tools and Roblox's expanding developer ecosystem has created a fundamentally new paradigm for game development on the platform. This research reveals that **Claude Code, combined with the official Roblox MCP Server and Rojo filesystem sync, provides a production-viable workflow** for building complete Roblox games — with real developers shipping full titles in days rather than months. The key enabler is the MCP integration that gives Claude Code direct access to Roblox Studio for code execution, model insertion, and play mode testing.

**BMAD Game Dev Studio** offers valuable engine-agnostic workflows (GDD creation, narrative design, sprint planning) but currently **lacks a Roblox-specific knowledge base** — the module supports Unity, Unreal, Godot, and Phaser. However, the gap is bridgeable by combining BMAD's planning workflows with a well-crafted `CLAUDE.md` containing Roblox-specific patterns and conventions.

The research identifies a clear implementation path: a 4-week ramp-up from environment setup through CI/CD pipeline establishment, with typical API costs of $6/day for active development. The critical success factor is understanding Roblox's mandatory client-server architecture and ensuring Claude Code places game logic in the correct execution context.

**Key Technical Findings:**

- Official Roblox MCP Server provides 6 tools for direct Claude Code ↔ Studio interaction
- Rojo is the essential bridge enabling filesystem-based development with Git version control
- Knit (Service-Controller) is the dominant game architecture framework; Jecs ECS for entity-heavy systems
- Roblox is investing heavily in AI-native development (31% more publishes from AI tool users, 50-120x faster prototyping)
- BMAD Game Dev Studio works for planning but needs Roblox engine knowledge base supplementation

**Top Recommendations:**

1. Start with Rojo + Claude Code + `CLAUDE.md` (works today, no MCP required)
2. Add MCP Server for direct Studio manipulation (significant workflow boost)
3. Use BMAD for GDD and sprint planning; supplement with Roblox-specific context
4. Invest in Luau fundamentals to effectively review AI-generated code
5. Implement CI/CD early (GitHub Actions + SaveToRoblox)

## Table of Contents

1. [Technical Research Scope Confirmation](#technical-research-scope-confirmation)
2. [Technology Stack Analysis](#technology-stack-analysis)
3. [Integration Patterns Analysis](#integration-patterns-analysis)
4. [Architectural Patterns and Design](#architectural-patterns-and-design)
5. [Implementation Approaches and Technology Adoption](#implementation-approaches-and-technology-adoption)
6. [Technical Research Recommendations](#technical-research-recommendations)
7. [Future Technical Outlook](#future-technical-outlook)
8. [Research Methodology and Sources](#research-methodology-and-sources)
9. [Research Conclusion](#research-conclusion)

## Research Overview

This comprehensive technical research examines the feasibility and best practices for developing Roblox games using Claude Code (Anthropic's AI-assisted terminal tool), the Roblox MCP Server, and the BMAD Game Dev Studio framework. The research was conducted through extensive web search verification across developer forums, official documentation, GitHub repositories, and community guides, with all claims cited from current (2025-2026) sources. The investigation covers the complete technology stack (Luau, Rojo, MCP, Knit, ECS), integration patterns between Claude Code and Roblox Studio, architectural best practices for game systems, implementation workflows from setup to CI/CD, and a forward-looking analysis of AI-native game development trends on the Roblox platform.

---

## Technical Research Scope Confirmation

**Research Topic:** Roblox Game Development with Claude Code and BMAD
**Research Goals:** Set up Roblox dev workflow with Claude Code, understand capabilities and limitations, evaluate BMAD game-dev potential

**Technical Research Scope:**

- Architecture Analysis - Roblox Studio architecture, client-server model, game structure patterns, Claude Code integration
- Implementation Approaches - Luau scripting, game logic patterns, AI-assisted coding workflows, BMAD methodology for game dev
- Technology Stack - Roblox Studio, Luau, Claude Code, BMAD framework, supporting tools and plugins
- Integration Patterns - Syncing code between Claude Code and Roblox Studio, file structure, version control, asset pipelines
- Performance Considerations - Roblox platform constraints, scripting performance, development velocity with AI assistance

**Research Methodology:**

- Current web data with rigorous source verification
- Multi-source validation for critical technical claims
- Confidence level framework for uncertain information
- Comprehensive technical coverage with architecture-specific insights

**Scope Confirmed:** 2026-04-01

## Technology Stack Analysis

### Programming Languages

**Luau** is the sole scripting language for Roblox game development. It is a derivative of Lua, rewritten from scratch in C++17 by Roblox, with key enhancements including a gradual type system, string interpolation, generalized table iteration, and performance optimizations. As of late 2024, a reworked type system and solver was introduced as a Beta feature, adding type functions and read-only table properties. Luau has also seen broader adoption beyond Roblox in titles like Alan Wake 2, Farming Simulator 2025, Second Life, and Warframe.

_Primary Language:_ Luau (Roblox's Lua derivative with gradual typing)
_Type System:_ Optional type annotations with compile-time checking (new solver in beta)
_Performance:_ Optimized runtime with native code generation support
_Confidence:_ HIGH — well-documented, official Roblox technology
_Source: [Luau Official](https://luau.org/why/), [Luau GitHub](https://github.com/luau-lang/luau), [Roblox Wiki](https://roblox.fandom.com/wiki/Luau)_

### Development Frameworks and Libraries

Roblox development does not use traditional external frameworks — the **Roblox Engine** itself serves as the framework, providing physics, rendering, networking, UI, and data persistence. Key libraries and patterns include:

- **Knit** — popular framework for structuring game services and controllers
- **ProfileService / DataStore2** — community data persistence libraries
- **Roact/React-Lua** — React-like UI frameworks for Roblox (Roblox has been porting React to Luau)
- **Rojo project structure** — enables modular, file-system-based code organization

_Major Framework:_ Roblox Engine (built-in physics, rendering, networking, UI)
_Community Libraries:_ Knit, ProfileService, Roact/React-Lua
_Code Organization:_ Rojo-based file system projects
_Confidence:_ HIGH — verified through multiple developer forum sources
_Source: [Rojo Docs](https://rojo.space/docs/v7/), [Roblox Creator Hub](https://create.roblox.com/docs/studio/plugins)_

### Database and Storage Technologies

Roblox provides built-in cloud data persistence:

- **DataStoreService** — key-value cloud storage for player data (6MB per key limit)
- **MemoryStoreService** — in-memory ephemeral storage for session data, leaderboards, matchmaking
- **MessagingService** — cross-server pub/sub communication
- **OrderedDataStore** — sorted data for leaderboards and rankings

No external databases are directly accessible from Roblox game scripts due to the sandboxed runtime. External data requires HTTP Service calls to proxy APIs.

_Built-in Storage:_ DataStoreService (persistent), MemoryStoreService (ephemeral)
_Cross-Server:_ MessagingService for pub/sub
_External Access:_ HTTPService to external APIs only
_Confidence:_ HIGH — official Roblox documentation
_Source: [Roblox Creator Hub](https://create.roblox.com/docs)_

### Development Tools and Platforms

**Core IDE:**
- **Roblox Studio** — the primary IDE with built-in 3D editor, script editor, debugger, terrain tools, animation editor, and test play modes

**External Code Editors (via sync tools):**
- **VS Code** — most popular external editor, used with Rojo or Argon extensions
- **Claude Code** — AI-assisted terminal-based development with MCP integration

**Sync Tools (Critical for Claude Code workflow):**
- **Rojo** — the de-facto standard for filesystem-to-Roblox syncing. Enables Git version control, external editors, and professional workflows. Builds `.rbxl`/`.rbxm` files from filesystem
- **Argon** — two-way real-time sync between VS Code and Roblox Studio
- **Lune** — standalone Luau runtime for build scripts and automation
- **rbxmk** — command-line tool for manipulating Roblox files

**MCP Servers (AI Integration):**
- **Official Roblox MCP Server** (Rust-based) — ships with Roblox Studio, provides `insert_model` and `run_code` tools for AI agents
- **Community MCP implementations** — [roblox-studio-mcp-claude-code](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code), [robloxstudio-mcp](https://github.com/boshyxd/robloxstudio-mcp)

**Roblox Studio Plugins:**
- TerrainTools, AnimationEditor, Transform Tool (built-in)
- Community plugins for building, UI design, and asset management
- **Developer Intelligence** — Roblox-specific AI assistant plugin (2026)

_IDE:_ Roblox Studio (primary), VS Code (external)
_Sync Tools:_ Rojo (standard), Argon (real-time two-way)
_AI Integration:_ Official Roblox MCP Server, community MCP servers
_Confidence:_ HIGH — verified across multiple sources
_Source: [Rojo GitHub](https://github.com/rojo-rbx/rojo), [Official MCP Server](https://github.com/Roblox/studio-rust-mcp-server), [Roblox Studio MCP Claude Code](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)_

### Cloud Infrastructure and Deployment

Roblox handles all hosting, scaling, and infrastructure:

- **Roblox Cloud** — fully managed game hosting, no server management required
- **Open Cloud API** — REST APIs for managing places, datastores, and assets programmatically
- **Publishing** — direct from Roblox Studio or via Rojo CLI builds
- **CDN** — Roblox manages all asset delivery and content distribution

For external services (analytics, webhooks, admin dashboards), developers typically use:
- Serverless functions (AWS Lambda, Cloudflare Workers) as HTTP proxy endpoints
- External databases accessed via HTTPService

_Hosting:_ Fully managed by Roblox (no infrastructure to manage)
_APIs:_ Open Cloud API for external automation
_Publishing:_ Studio direct or Rojo CLI builds
_Confidence:_ HIGH — official Roblox platform feature
_Source: [Roblox Creator Hub](https://create.roblox.com/docs)_

### Technology Adoption Trends

**AI-Assisted Development (Major Trend):**
- Claude Code with MCP integration is enabling developers to build full Roblox games with minimal manual coding. One developer reported building a complete game with Claude Opus 4.6 in 4 days with no scripter touching the code
- Official Roblox MCP Server signals Roblox's commitment to AI-assisted development
- Multiple community tools (ClaudeBlox, Developer Intelligence) emerging

**Professional Tooling Adoption:**
- Rojo-based workflows becoming standard for serious development teams
- Git version control increasingly expected for collaborative projects
- External editor workflows (VS Code + Rojo) replacing Studio-only development

**BMAD Game Dev Studio:**
- BMAD offers a dedicated Game Dev Studio (BMGD) module with agents for game architecture, design, and development
- Currently supports Unity, Unreal, Godot, and Phaser with engine-specific knowledge bases
- **Roblox is NOT explicitly listed** as a supported engine, but the GDD workflow, narrative design, and production planning are engine-agnostic and applicable
- The BMGD module provides Game Design Document creation, mechanics design, progression/balance systems, and epic-driven sprint planning

_AI Development:_ Rapidly growing — MCP integration is a game-changer
_Tooling:_ Professional workflows (Rojo + Git + external editors) becoming standard
_BMAD:_ Game Dev Studio module exists but lacks Roblox-specific knowledge base
_Confidence:_ HIGH for tooling trends, MEDIUM for BMAD Roblox applicability
_Source: [BMAD Game Dev Studio](https://github.com/bmad-code-org/bmad-module-game-dev-studio), [ClaudeBlox](https://www.claude-blox.com/), [Roxlit Guide](https://roxlit.dev/blog/how-to-use-claude-code-with-roblox), [Developer Forum](https://devforum.roblox.com/t/we-built-a-full-game-with-2-people-using-ai-in-studio-heres-the-tool/4308613)_

## Integration Patterns Analysis

### Claude Code ↔ Roblox Studio (MCP Integration)

The **Model Context Protocol (MCP)** is the primary integration layer between Claude Code and Roblox Studio. The official Roblox MCP Server (Rust-based) provides these tools:

| Tool | Direction | Purpose |
|------|-----------|---------|
| `run_code` | Claude → Studio | Execute Luau code in Studio, returns printed output. Used for both modifications and information retrieval |
| `insert_model` | Claude → Studio | Insert a model from the Roblox Creator Store into the workspace |
| `get_console_output` | Studio → Claude | Read Studio console output for debugging |
| `start_stop_play` | Claude → Studio | Start/stop play mode or run the server |
| `run_script_in_play_mode` | Claude → Studio | Execute scripts in play mode with automatic stop, returns logs, errors, and duration |
| `get_studio_mode` | Studio → Claude | Check current Studio mode (start_play, run_server, stop) |

**Setup**: Build the MCP server from source (Rust), install the companion Roblox Studio plugin, configure via JSON MCP config file pointing to the server binary.

_Protocol:_ MCP (Model Context Protocol) over local IPC
_Tools:_ 6 core tools for code execution, model insertion, console reading, and play mode control
_Confidence:_ HIGH — official Roblox implementation
_Source: [Official MCP Server](https://github.com/Roblox/studio-rust-mcp-server), [Roblox DevForum Announcement](https://devforum.roblox.com/t/introducing-the-open-source-studio-mcp-server/3649365)_

### Filesystem ↔ Roblox Studio (Rojo Sync)

**Rojo** is the critical bridge that enables Claude Code to write Luau files on disk and have them sync into Roblox Studio in real-time:

**Project Structure** (`default.project.json`):
- Maps filesystem directories to Roblox service containers (ReplicatedStorage, ServerScriptService, etc.)
- `$path` fields specify filesystem-to-Roblox mappings relative to the project file
- Directories become `Folder` instances; special files (`init.lua`, `init.server.lua`, `init.client.lua`) change directory types

**File Conventions**:
- `.lua` / `.luau` files → Script instances (type determined by suffix or init file)
- `.json` files → ModuleScript returning equivalent table
- `.model.json` files → Roblox model instances
- Directories with `init.server.lua` → Script; `init.client.lua` → LocalScript; `init.lua` → ModuleScript

**Sync Modes**:
- `rojo serve` — live sync during development (watches filesystem for changes)
- `rojo build` — one-shot build to `.rbxl` or `.rbxm` file for publishing

**Two-Workflow Approach**: Claude Code can work through either:
1. **Rojo workflow** — write `.luau` files to disk → Rojo syncs to Studio (best for scripting)
2. **MCP workflow** — execute code directly in Studio via `run_code` (best for instance manipulation, testing)

_Sync Protocol:_ HTTP-based local server (Rojo serve) or file build (Rojo build)
_File Mapping:_ Configurable via `default.project.json`
_Confidence:_ HIGH — well-established standard
_Source: [Rojo Sync Details](https://rojo.space/docs/v7/sync-details/), [Rojo Project Format](https://rojo.space/docs/v6/project-format/)_

### Roblox Client-Server Communication

Roblox uses a **mandatory client-server model** (Filtering Enabled) where the server is authoritative:

**RemoteEvents** (one-way, async):
- Client → Server: `RemoteEvent:FireServer()` → `OnServerEvent`
- Server → Client: `RemoteEvent:FireClient(player)` → `OnClientEvent`
- Server → All Clients: `RemoteEvent:FireAllClients()`

**RemoteFunctions** (two-way, yields):
- Client → Server: `RemoteFunction:InvokeServer()` → `OnServerInvoke` callback
- Server → Client: `RemoteFunction:InvokeClient()` (discouraged — client can block)

**Data Replication**: Server changes automatically replicate to all clients. Client changes do NOT replicate to server (security model).

**Implications for Claude Code**: When Claude writes game logic, it must correctly place scripts in:
- `ServerScriptService` / `ServerStorage` — server-only code
- `StarterPlayerScripts` / `StarterGui` — client-side code
- `ReplicatedStorage` — shared modules accessible by both

_Architecture:_ Authoritative server, replicated clients
_Communication:_ RemoteEvent (async one-way), RemoteFunction (sync two-way)
_Security:_ Server-authoritative; never trust client data
_Confidence:_ HIGH — core Roblox architecture
_Source: [Roblox Creator Docs](https://github.com/Roblox/creator-docs/blob/main/content/en-us/scripting/events/remote.md), [RemoteEvent Docs](https://create.roblox.com/docs/reference/engine/classes/RemoteEvent)_

### Roblox Open Cloud API (External Automation)

The **Open Cloud API** enables external tools and CI/CD workflows to interact with Roblox resources:

**Available Endpoints**:
- **Place Publishing API** — publish places programmatically (enables CI/CD)
- **DataStore API** — read/write game datastores externally (analytics, admin tools)
- **Messaging Service API** — send cross-server messages from external systems
- **Universes API** — manage game universe metadata
- **Webhooks** — receive real-time notifications on external endpoints

**Authentication**: API keys or OAuth 2.0 applications with granular permission scoping.

**Python SDK**: `rblx-open-cloud` — community Python wrapper for Open Cloud endpoints, useful for build scripts and automation.

_API Style:_ REST with API key or OAuth2 authentication
_Key Endpoints:_ Place publishing, DataStore, Messaging, Universes
_Automation:_ Enables CI/CD pipelines for Roblox game publishing
_Confidence:_ HIGH — official Roblox API
_Source: [Cloud API Reference](https://create.roblox.com/docs/cloud/reference), [rblx-open-cloud](https://github.com/treeben77/rblx-open-cloud)_

### BMAD ↔ Roblox Integration Pattern

BMAD integrates with Roblox development at the **workflow and planning level**, not the engine level:

**What BMAD provides for Roblox**:
- Game Design Document (GDD) creation workflow — engine-agnostic, fully applicable
- Narrative design (story, characters, dialogue) — fully applicable
- Epic/story-driven sprint planning — fully applicable
- Agent personas (Game Architect, Game Designer, Game Developer) — applicable with Roblox context

**What's missing for Roblox**:
- No Roblox-specific engine knowledge base (unlike Unity/Unreal/Godot)
- No Luau code generation patterns
- No Roblox Studio-aware architecture templates
- No integration with Rojo project structure

**Workaround**: Use BMAD's engine-agnostic workflows (GDD, narrative, sprints) and supplement with a custom `CLAUDE.md` containing Roblox-specific architecture patterns, Luau conventions, and Rojo project structure guidance.

_Integration Level:_ Workflow/planning (not engine-level)
_Applicable:_ GDD, narrative design, sprint planning, agent personas
_Gap:_ No Roblox engine knowledge base
_Confidence:_ HIGH — verified against BMGD module documentation
_Source: [BMAD Game Dev Studio](https://github.com/bmad-code-org/bmad-module-game-dev-studio), [BMGD Docs](https://game-dev-studio-docs.bmad-method.org/)_

## Architectural Patterns and Design

### System Architecture Patterns

Roblox game architecture follows two dominant patterns, depending on project complexity:

**1. Service-Controller Pattern (Knit Framework)**

The most widely adopted architectural pattern for Roblox games. **Knit** is a lightweight framework that provides:
- **Services** (server-side) — define server logic, data management, game state
- **Controllers** (client-side) — define client logic, UI, input handling
- Automatic networking — Knit handles RemoteEvent/RemoteFunction creation between services and controllers
- Single entry point — one server Script and one client LocalScript bootstrap all services/controllers

**Best Practice**: Services and controllers should not hold mutable state directly. Consumers should go through methods/events, not properties. This prevents global state anti-patterns.

**2. Entity Component System (ECS)**

Growing in adoption for complex games with many dynamic entities:
- **Jecs** — fast, portable ECS for Luau; can iterate 800,000 entities at 60 FPS; supports entity relationships as first-class citizens
- **ECS-Lua** — lightweight ECS engine focused on ease of use
- **EntityManager** — modular entity-component system for reusable game objects

ECS is preferred over OOP hierarchies when games have many similar entities with varying behaviors (NPCs, projectiles, collectibles). Components and Systems are organized in separate module folders for expandability.

**3. Hybrid Approach (Recommended for Claude Code)**

Use Knit for high-level game services (data, matchmaking, economy) and ECS for entity-heavy systems (combat, AI, physics interactions). Claude Code can scaffold both layers via Rojo file structure.

_Dominant Pattern:_ Service-Controller (Knit) for game services
_Alternative:_ ECS (Jecs) for entity-heavy systems
_Recommended:_ Hybrid approach combining both
_Confidence:_ HIGH — verified across multiple community sources
_Source: [Knit Framework](https://sleitnick.github.io/Knit/), [Jecs](https://github.com/Ukendio/jecs), [ECS Tutorial](https://devforum.roblox.com/t/ecs-in-roblox-studio-why-is-it-better-than-oop-and-how-to-cook-it/3962745)_

### Design Principles and Best Practices

**Roblox-Specific Design Principles:**

1. **Server Authority** — all game-critical logic (damage, currency, progression) MUST run server-side. Client predicts for responsiveness but server validates and reconciles
2. **Module-First Organization** — use ModuleScripts in ReplicatedStorage/ServerStorage for shared logic. Avoid putting logic directly in Scripts/LocalScripts
3. **Event-Driven Over Polling** — use `.Changed`, `.ChildAdded`, `RemoteEvent` signals instead of `while wait() do` loops. Reduces CPU usage and improves responsiveness
4. **Single Responsibility** — each service/controller/module handles one domain (Combat, Inventory, UI, etc.)
5. **Data-Code Separation** — store game configuration (weapon stats, level data, shop items) in ModuleScripts or JSON configs, not hardcoded in logic

**Rojo Project Organization Pattern:**
```
src/
├── server/           → ServerScriptService
│   ├── services/     → Knit services
│   └── init.server.luau  → Server bootstrap
├── client/           → StarterPlayerScripts
│   ├── controllers/  → Knit controllers
│   └── init.client.luau  → Client bootstrap
├── shared/           → ReplicatedStorage
│   ├── modules/      → Shared modules
│   ├── types/        → Type definitions
│   └── config/       → Game configuration data
└── assets/           → Various asset containers
```

_Confidence:_ HIGH — established community consensus
_Source: [Organizing Project Structure](https://docs.superbulletstudios.com/prompt-engineering/organizing-project-structure), [Knit Best Practices](https://medium.com/@sleitnick/knit-its-history-and-how-to-build-it-better-3100da97b36)_

### Scalability and Performance Patterns

**Scripting Performance:**
- Replace `while wait() do` loops with event-driven triggers — can cut redundant processing significantly
- Use `task.spawn` / `task.defer` for non-blocking operations
- Limit per-frame computation; move heavy work to heartbeat-throttled systems
- Object pooling for frequently created/destroyed instances (projectiles, effects)

**Asset and Rendering:**
- **StreamingEnabled** — loads only nearby content, reduces initial load by up to 35%
- **LOD (Level of Detail)** — reduce mesh complexity at distance (up to 50% FPS improvement)
- Limit 3D models to ~500 parts maximum for optimal performance
- Texture atlasing and mesh simplification reduce GPU load by ~30%
- Reduce collision mesh complexity by 30% for 25% frame stability improvement

**Memory and Networking:**
- Minimize RemoteEvent frequency — batch updates where possible
- Use `UnreliableRemoteEvent` for non-critical high-frequency data (positions, animations)
- Clean up connections and instances to prevent memory leaks
- Use `Debris:AddItem()` for timed cleanup of temporary instances

_Key Metric:_ Well-optimized games see 20%+ longer play sessions
_Confidence:_ HIGH — official Roblox documentation and community benchmarks
_Source: [Roblox Performance Optimization](https://create.roblox.com/docs/performance-optimization), [Hitchhiker's Guide to Optimization](https://devforum.roblox.com/t/the-hitchhikers-guide-to-optimization-optimize-your-game-using-just-one-post-instead-of-many/3358345)_

### Security Architecture Patterns

**Core Principle: Never Trust the Client**

All client input must be validated server-side. Common exploit vectors:
- **Input manipulation** — altering health, currency, or inventory values
- **Time warping** — disrupting frame timing for unauthorized actions
- **Memory access** — reading/writing protected memory areas
- **Command injection** — triggering unauthorized server commands via RemoteEvents

**Server Validation Patterns:**

1. **Rate limiting** — throttle RemoteEvent calls per player (e.g., max 10 actions/second)
2. **Sanity checks** — validate that requested actions are physically possible (distance checks, cooldown enforcement, inventory verification)
3. **Server-side state** — maintain authoritative player data (health, currency, inventory) exclusively on server; client only displays
4. **Input validation** — type-check and bounds-check all RemoteEvent arguments
5. **Network ownership management** — carefully assign physics ownership to prevent teleportation exploits

**Claude Code Implications:** When Claude generates game logic, it should always:
- Place critical game state and validation on the server
- Add sanity checks to all RemoteEvent handlers
- Never expose admin/debug RemoteEvents in production
- Use server-side cooldowns for all player actions

_Confidence:_ HIGH — official Roblox security documentation
_Source: [Security Tactics](https://create.roblox.com/docs/scripting/security/security-tactics), [Exploit Prevention Tutorial](https://devforum.roblox.com/t/exploiting-and-how-to-prevent-it/2304937)_

### Data Architecture Patterns

**Player Data:**
- Use **ProfileService** or **DataStore2** for robust player data management with session locking, retry logic, and data loss prevention
- Structure player data as a single serializable table per player
- Implement data versioning for schema migrations
- Use `MemoryStoreService` for ephemeral data (matchmaking queues, live leaderboards)

**Game Configuration:**
- Store in ModuleScripts or JSON files synced via Rojo
- Claude Code can generate/modify config files directly on filesystem
- Keep configs in `ReplicatedStorage` for client access or `ServerStorage` for server-only

**Cross-Server:**
- `MessagingService` for real-time cross-server events (global announcements, trading)
- `TeleportService` for moving players between places in a universe
- Open Cloud DataStore API for external admin tools and analytics

_Confidence:_ HIGH — established community patterns
_Source: [Roblox Creator Hub](https://create.roblox.com/docs)_

### Deployment and Operations Architecture

**Development → Production Pipeline:**

1. **Local Development**: Claude Code writes Luau files → Rojo syncs to Studio → test in Studio play mode
2. **Version Control**: Git repository with Rojo project; `.rbxl` files in `.gitignore`
3. **Testing**: Use MCP `run_script_in_play_mode` for automated testing; Studio play mode for manual QA
4. **Build**: `rojo build` produces `.rbxl` file from filesystem
5. **Publish**: Studio publish or Open Cloud Place Publishing API for CI/CD
6. **Monitoring**: Developer Console in-game; analytics via external dashboards

**Multi-Place Architecture:**
- Use Roblox Universes for multi-place games (lobby + game servers)
- Share code via Packages or shared Rojo modules
- TeleportService for player movement between places

_Confidence:_ HIGH — verified workflow pattern
_Source: [Rojo Docs](https://rojo.space/docs/v7/), [Open Cloud API](https://create.roblox.com/docs/cloud)_

## Implementation Approaches and Technology Adoption

### Getting Started: Step-by-Step Setup

**Phase 1: Environment Setup (30 minutes)**

1. **Install tooling**:
   - Install [aftman](https://github.com/LPGhatguy/aftman) (tool manager)
   - `aftman add rojo-rbx/rojo` → install Rojo
   - `rojo plugin install` → install Rojo Studio plugin
   - Install Claude Code CLI (`npm install -g @anthropic-ai/claude-code` or via Homebrew)
   - Set up Anthropic API key

2. **Initialize project**:
   - `rojo init my-game` → creates `default.project.json` and `src/` structure
   - `git init` → version control
   - Create `.gitignore` with `*.rbxl`, `*.rbxlx`, `node_modules/`

3. **Configure Claude Code**:
   - Create `CLAUDE.md` in project root — Claude reads this automatically on every session
   - Document: project uses Luau (not Lua), Rojo file structure, Knit framework conventions, server/client/shared separation

4. **Set up MCP** (optional but recommended):
   - Build official Roblox MCP server from source (requires Rust)
   - Or use community MCP: [roblox-studio-mcp-claude-code](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)
   - Configure MCP JSON config file pointing to server binary

5. **Start developing**:
   - Terminal 1: `rojo serve` (live sync to Studio)
   - Terminal 2: `claude` (Claude Code session)
   - Roblox Studio: open and connect to Rojo

**Alternative Quick Start**: Use [Roxlit](https://roxlit.dev/) to connect Claude Code to Roblox Studio in under 5 minutes with no terminal configuration.

_Confidence:_ HIGH — verified setup guides
_Source: [Roxlit Complete Guide](https://roxlit.dev/blog/how-to-use-claude-code-with-roblox), [Roblox MCP Setup](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code/blob/main/SETUP_GUIDE.md)_

### Development Workflows and Tooling

**Daily Development Workflow with Claude Code:**

```
1. Open Roblox Studio → connect to Rojo
2. Start Claude Code in project directory
3. Describe what you want to build ("create an inventory system with 20 slots, drag-and-drop UI, server-side validation")
4. Claude writes .luau files → Rojo auto-syncs to Studio
5. Test in Studio play mode (or via MCP run_script_in_play_mode)
6. Iterate: describe issues or next features to Claude
7. Git commit when feature is stable
```

**What Claude Code excels at for Roblox:**
- Generating complete service/controller modules (DataStore, combat, inventory)
- Writing repetitive boilerplate (RemoteEvent setup, type definitions)
- Refactoring and restructuring code across multiple files
- Debugging — describe the bug, Claude reads context and suggests fixes
- Writing configuration data (item databases, level configs)

**What Claude Code struggles with:**
- 3D world building, terrain, and visual design (use Studio's visual editor)
- Asset creation (models, textures, sounds — use external tools)
- Real-time visual tuning (animation timing, particle effects — requires Studio)
- Complex physics interactions that need visual debugging

**Recommended Tooling Ecosystem:**

| Tool | Purpose |
|------|---------|
| Claude Code | AI-assisted Luau scripting and architecture |
| Roblox Studio | Visual editor, 3D building, testing, publishing |
| Rojo | Filesystem ↔ Studio sync |
| VS Code | Luau extension for syntax highlighting (optional alongside Claude Code) |
| Git + GitHub | Version control and collaboration |
| Lune | Standalone Luau runtime for build scripts |
| Selene | Luau linter for code quality |
| StyLua | Luau code formatter |
| Wally | Package manager for Roblox libraries (Knit, ProfileService, etc.) |

_Confidence:_ HIGH — verified across multiple developer guides
_Source: [Roxlit Guide](https://roxlit.dev/blog/how-to-use-claude-code-with-roblox), [Rojo Docs](https://rojo.space/docs/v7/)_

### Testing and Quality Assurance

**Testing Frameworks:**
- **TestEZ** — Roblox's official BDD-style testing framework (describe/it syntax, similar to Mocha/RSpec). Can run within Roblox Studio or in CI via Lemur
- **MCP-based testing** — use `run_script_in_play_mode` to execute test scripts and capture results automatically
- **Manual playtesting** — Studio's built-in play mode with server/client split view

**CI/CD Pipeline (GitHub Actions):**

```yaml
# Example workflow
1. Push to GitHub → trigger action
2. Run Selene linter + StyLua formatter checks
3. Run TestEZ tests via Lemur (headless)
4. rojo build → produce .rbxl file
5. Upload via SaveToRoblox GitHub Action (Open Cloud Place Publishing API)
6. Tag-based environments: v*.*.* → production, v*.*.*-qa.* → test
```

Roblox has published a [Place CI/CD demo](https://github.com/Roblox/place-ci-cd-demo) using Rojo and the Open Cloud Luau Execution API.

_Confidence:_ HIGH — official Roblox CI/CD demo exists
_Source: [Roblox Place CI/CD Demo](https://github.com/Roblox/place-ci-cd-demo), [TestEZ](https://github.com/Roblox/testez), [SaveToRoblox](https://devforum.roblox.com/t/savetoroblox-continuous-delivery-to-roblox-using-github-actions/2270997)_

### Team Organization and Skills

**Solo Developer (Claude Code + You):**
- You: game design, visual building, playtesting, publishing
- Claude Code: scripting, architecture, data structures, debugging
- This is the most common and effective setup for indie Roblox developers

**Small Team (2-5 people):**
- Scripter(s) + Claude Code: game systems, networking, data
- Builder(s): 3D environments, maps, props
- UI Designer: interface design and layout
- Git branching strategy: feature branches per developer, main branch for stable builds

**Skills Required:**
- **Essential**: Basic Luau understanding (to review and guide Claude's output), Roblox Studio navigation, Git basics
- **Recommended**: Understanding of client-server architecture, game design principles
- **Nice to have**: Rojo project structure, CI/CD concepts, performance profiling

_Confidence:_ HIGH — based on community patterns and developer testimonials
_Source: [Roblox DevForum](https://devforum.roblox.com/t/we-built-a-full-game-with-2-people-using-ai-in-studio-heres-the-tool/4308613)_

### Cost Optimization and Resource Management

**Claude Code Costs:**
- **API pricing (pay-as-you-go)**: Opus $5/$25 per million input/output tokens; Sonnet $3/$15; Haiku $1/$5
- **Typical Roblox session**: A few dollars per month for light usage
- **Average developer spend**: ~$6/day, 90% of users under $12/day (~$100-200/month)
- **Pro plan alternative**: Claude Pro subscription ($20/month) includes Claude Code access with usage limits

**Cost Optimization Tips:**
- Use Sonnet for routine scripting tasks (3x cheaper than Opus)
- Reserve Opus for complex architecture decisions and debugging
- Write detailed `CLAUDE.md` to reduce back-and-forth token usage
- Use Rojo file sync for code changes (cheaper than MCP round-trips for simple edits)

**Roblox Platform Costs:**
- Roblox Studio: **Free**
- Game hosting: **Free** (Roblox handles all infrastructure)
- Publishing: **Free**
- DevEx (Developer Exchange): Roblox takes a revenue share on in-game purchases

_Confidence:_ HIGH — verified pricing data
_Source: [Claude Code Pricing](https://www.sparkagents.com/blog/claude-code-pricing), [Roxlit Guide](https://roxlit.dev/blog/how-to-use-claude-code-with-roblox)_

### Risk Assessment and Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| Claude generates insecure code (client-side game logic) | HIGH — exploitable | Always review server/client placement; add security validation prompts to CLAUDE.md |
| Rojo sync conflicts with manual Studio edits | MEDIUM — lost work | Use fully-managed Rojo workflow; avoid editing synced scripts directly in Studio |
| API cost overrun during heavy development | LOW-MEDIUM | Set API spending limits; use Sonnet for routine tasks |
| BMAD Game Dev Studio lacks Roblox knowledge base | LOW — workaround available | Supplement with custom CLAUDE.md; use engine-agnostic GDD/sprint workflows |
| Claude hallucinates deprecated Roblox APIs | MEDIUM | Include API version notes in CLAUDE.md; test all generated code in Studio |
| MCP server instability | LOW | Rojo-based workflow works independently of MCP; MCP is optional enhancement |

_Confidence:_ HIGH — based on documented developer experiences
_Source: [Roxlit Guide](https://roxlit.dev/blog/how-to-use-claude-code-with-roblox), [Roblox Security Tactics](https://create.roblox.com/docs/scripting/security/security-tactics)_

## Technical Research Recommendations

### Implementation Roadmap

**Week 1 — Foundation:**
1. Set up Rojo + Git + Claude Code environment
2. Create comprehensive `CLAUDE.md` with Roblox patterns, Luau conventions, and project structure
3. Build a simple proof-of-concept game (e.g., obby or tycoon) to validate the workflow

**Week 2 — Expand Tooling:**
4. Install and configure Roblox MCP Server for direct Studio manipulation
5. Set up Wally for package management (install Knit, ProfileService)
6. Add Selene (linter) and StyLua (formatter) to the workflow

**Week 3 — Production Workflow:**
7. Implement CI/CD pipeline with GitHub Actions
8. Write TestEZ tests for critical game systems
9. Establish Git branching strategy for your team

**Week 4 — BMAD Integration:**
10. Install BMAD Game Dev Studio module for GDD creation and sprint planning
11. Use BMAD's Game Architect agent for system design documentation
12. Create Roblox-specific knowledge base additions to supplement BMGD

### Technology Stack Recommendations

| Layer | Recommended | Alternative |
|-------|------------|-------------|
| AI Assistant | Claude Code (Opus for architecture, Sonnet for routine) | Cursor + Rojo |
| Framework | Knit (services/controllers) | Custom framework or none for simple games |
| Data Persistence | ProfileService | DataStore2, raw DataStoreService |
| UI Framework | Roact/React-Lua | Native Roblox GUI |
| ECS (if needed) | Jecs | ECS-Lua |
| Sync Tool | Rojo | Argon |
| Package Manager | Wally | Manual library management |
| Testing | TestEZ | Manual playtesting |
| CI/CD | GitHub Actions + SaveToRoblox | Manual Studio publishing |
| Planning | BMAD (GDD + sprints) | Manual documentation |

### Skill Development Requirements

**For you (Alex):**
1. **Luau fundamentals** — enough to review and guide Claude's output (not memorize APIs)
2. **Roblox client-server model** — understand where code runs and why
3. **Rojo project structure** — know how filesystem maps to Studio hierarchy
4. **Git basics** — commit, branch, merge, push
5. **Prompt engineering for game dev** — learn to describe game systems clearly to Claude

### Success Metrics and KPIs

- **Development velocity**: Time from concept to playable prototype (target: 1-2 weeks for a simple game)
- **Code quality**: Selene lint pass rate, TestEZ test coverage
- **Security**: Zero server-side vulnerabilities in exploiter testing
- **Player retention**: Session length and return rate after launch
- **Cost efficiency**: Claude API spend per feature delivered

## Future Technical Outlook

### Near-Term (2026-2027)

**Roblox AI-Native Development:**
- Roblox is integrating MCP into Studio's built-in Assistant, enabling multi-step workflows across third-party tools (e.g., design UI in Figma → import directly into Studio via MCP)
- "Fully functional objects" generation from prompts is rolling out — initial categories include vehicles and weapons with interactive behaviors, representing 50-120x faster prototyping
- Roblox's "4D generative AI" will produce dynamic 3D objects, going beyond static asset creation
- Creators using AI tools showed 31% increase in game publishes

**Claude Code + Roblox Evolution:**
- Expect tighter MCP integration as Roblox expands its official MCP server capabilities
- Claude Code's model improvements will increase Luau code quality and reduce hallucinated APIs
- Community tooling (Roxlit, ClaudeBlox, Developer Intelligence) will mature and consolidate

**BMAD Game Dev Studio:**
- BMAD roadmap includes Cross Platform Agent Team improvements and Skills Architecture evolution
- Roblox engine knowledge base is not currently on the public BMAD roadmap, but the "any engine" philosophy means custom knowledge bases can be contributed
- As AI game development grows, pressure for Roblox-specific BMGD support will increase

### Medium-Term (2027-2028)

- AI co-pilots directing entire game narratives and generating multiplayer bots
- Brain-computer interfaces for intuitive game control (experimental)
- Full AI-native game development pipelines where natural language replaces most manual scripting

### Challenges and Considerations

- 47% of developers worry about "soulless outputs" — maintaining creative originality alongside AI efficiency
- Ethical concerns: biased training data affecting NPC behavior, IP disputes over AI-generated art
- Job role evolution: scripters becoming AI supervisors/prompt engineers rather than line-by-line coders

_Confidence:_ HIGH for near-term, MEDIUM for medium-term predictions
_Source: [Roblox AI Vision](https://about.roblox.com/newsroom/2023/02/generative-ai-roblox-vision-future-creation), [AI Transforming Roblox 2026](https://www.seeles.ai/resources/blogs/create-roblox-game-ai-powered-guide-2026), [Roblox AI Ecosystem 2026](https://www.ainvest.com/news/roblox-ecosystem-ai-driven-creativity-revolution-market-expansion-2026-2509/)_

## Research Methodology and Sources

### Research Approach

- **Scope**: Full lifecycle of Roblox game development with AI assistance (Claude Code, MCP, BMAD)
- **Data Sources**: Official Roblox documentation, GitHub repositories, Roblox Developer Forum, community guides, developer testimonials, BMAD official docs
- **Verification**: All technical claims verified against current (2025-2026) web sources with multiple source cross-referencing
- **Time Period**: April 2026, with forward-looking analysis to 2028
- **Confidence Framework**: HIGH (official docs, verified), MEDIUM (community consensus), LOW (speculative)

### Key Sources

**Official Documentation:**
- [Roblox Creator Hub](https://create.roblox.com/docs) — official Roblox documentation
- [Roblox MCP Server](https://github.com/Roblox/studio-rust-mcp-server) — official open-source MCP implementation
- [Roblox Place CI/CD Demo](https://github.com/Roblox/place-ci-cd-demo) — official CI/CD reference
- [Luau Language](https://luau.org/) — official Luau documentation

**Frameworks and Tools:**
- [Rojo](https://rojo.space/docs/v7/) — filesystem sync tool
- [Knit Framework](https://sleitnick.github.io/Knit/) — game framework
- [Jecs](https://github.com/Ukendio/jecs) — ECS library
- [TestEZ](https://github.com/Roblox/testez) — testing framework
- [BMAD Game Dev Studio](https://github.com/bmad-code-org/bmad-module-game-dev-studio) — game dev planning module

**Community Guides:**
- [Roxlit — Claude Code + Roblox Complete Guide](https://roxlit.dev/blog/how-to-use-claude-code-with-roblox)
- [Roblox Studio MCP for Claude Code](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)
- [Roblox DevForum — Game Built with AI](https://devforum.roblox.com/t/we-built-a-full-game-with-2-people-using-ai-in-studio-heres-the-tool/4308613)

### Research Limitations

- BMAD Game Dev Studio Roblox support is inferred from "any engine" philosophy; no direct Roblox testing was conducted
- Claude Code API pricing may change; costs cited are as of April 2026
- MCP server capabilities are evolving rapidly; additional tools may be available by the time of reading
- Performance benchmarks are community-reported, not independently verified

---

## Research Conclusion

### Summary of Key Findings

Developing Roblox games with Claude Code is **production-viable today**. The workflow — Claude Code writing Luau files synced via Rojo, with optional MCP for direct Studio interaction — is already being used by developers to ship complete games. The critical enablers are:

1. **Rojo** for filesystem-based development (Git-friendly, Claude Code-compatible)
2. **Official Roblox MCP Server** for direct Studio manipulation
3. **Knit framework** for clean service-controller architecture
4. **A well-crafted `CLAUDE.md`** that teaches Claude about Roblox conventions

### BMAD Assessment

BMAD Game Dev Studio provides genuine value for **planning and design** (GDD creation, narrative design, sprint planning) but requires supplementation for Roblox-specific implementation. The engine-agnostic workflows are directly applicable; the gap is in engine-specific code patterns and architecture templates. This is a solvable gap through custom `CLAUDE.md` and knowledge base additions.

### Strategic Impact

The combination of Claude Code + MCP + Rojo fundamentally changes the economics of Roblox game development. Solo developers and small teams can now tackle projects that previously required larger teams and longer timelines. The key competitive advantage is **speed to playable prototype** — measured in days rather than months.

### Next Steps for Alex

1. **Today**: Install Rojo, initialize a project, create your `CLAUDE.md`
2. **This week**: Build a simple game (obby or tycoon) end-to-end with Claude Code
3. **Next week**: Add MCP Server for enhanced workflow; evaluate BMAD Game Dev Studio for your planning needs
4. **Ongoing**: Develop Luau review skills; iterate on your `CLAUDE.md` as you learn what works

---

**Technical Research Completion Date:** 2026-04-01
**Research Period:** Comprehensive current technical analysis (2025-2026 data)
**Source Verification:** All technical facts cited with current sources
**Technical Confidence Level:** High — based on multiple authoritative technical sources

_This comprehensive technical research document serves as an authoritative reference on Roblox game development with Claude Code and BMAD, providing strategic insights for informed decision-making and implementation._
