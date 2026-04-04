---
stepsCompleted: [1, 2]
inputDocuments: []
workflowType: 'research'
lastStep: 1
research_type: 'technical'
research_topic: 'Roblox Platform for Game Dev Studio'
research_goals: 'Understand Roblox tech stack, architecture, design patterns, and game development topics specific to running a Game Dev Studio — tailored for an experienced backend developer/architect'
user_name: 'Alex Suprun'
date: '2026-04-04'
web_research_enabled: true
source_verification: true
---

# Research Report: Technical

**Date:** 2026-04-04
**Author:** Alex Suprun
**Research Type:** technical

---

## Research Overview

**Purpose:** Personal reference + architecture decisions for running a Roblox Game Dev Studio. Tailored for an experienced backend developer/architect — Roblox-specific and game-dev-specific concepts only.

**Scope confirmed:** 2026-04-04
**Research methodology:** Parallel web searches across 5 domains, verified against current (2024–2026) sources.

---

## Technical Research Scope Confirmation

**Research Topic:** Roblox Platform for Game Dev Studio
**Research Goals:** Understand Roblox tech stack, architecture, design patterns, and game development topics specific to running a Game Dev Studio — tailored for an experienced backend developer/architect

**Technical Research Scope:**
- Architecture Analysis — engine, client-server model, DataModel, replication
- Implementation Approaches — Luau patterns, module systems, event-driven architecture
- Technology Stack — Studio IDE, Luau, external toolchain (Rojo, Wally, etc.)
- Integration Patterns — Roblox service APIs, datastores, HTTP, monetization hooks
- Performance Considerations — streaming, physics budget, memory, network replication
- Game Dev Architecture — concepts that differ from full-stack SDLC
- VS Code + Roblox Integration — editing outside Studio, sync pipeline
- Claude Code + AI Integration — workflow and building AI features into studio

**Scope Confirmed:** 2026-04-04

---

## Outline: Roblox Platform for Game Dev Studio

> Topics + subtopics. No deep dive. Designed as input for a learning path.

---

### 1. Roblox Platform Architecture

#### 1.1 Engine & DataModel
- DataModel as root instance tree (source of truth for place state)
- Services as direct children (Workspace, ServerScriptService, ReplicatedStorage, etc.)
- Class-based instantiation — every instance has a type that determines behavior
- Parent-child hierarchy: how state is organized and accessed

#### 1.2 Client-Server Model
- Server = authoritative simulation; client = rendering + local prediction
- How state flows from server to client (selective, event-driven replication)
- What runs where: LocalScript vs Script vs ModuleScript
- Trust model: never trust client input

#### 1.3 Replication System
- What gets replicated automatically (properties, attributes, physics)
- Selective replication: controlling what clients see
- Attribute limits (64 per instance, 50-char names, 50-char strings)
- Delta replication — only changed data transmitted

#### 1.4 RunService Lifecycle
- `Heartbeat` (~60Hz, post-physics), `Stepped` (pre-physics), `RenderStepped` (client only)
- Binding to simulation vs spawning loops
- Error isolation: why RunService callbacks beat `while true` loops

#### 1.5 Place & Universe Structure
- Universe = collection of places; each place = separate server instance
- Single-place vs multi-place architecture (lobby → hub → game places)
- TeleportService: how it works, player disconnect/reconnect on teleport
- Persistent player data across places (DataStore + session handoff)

#### 1.6 Server Infrastructure & Scaling
- Max 200 players/server (700 in beta as of 2024)
- Roblox manages server provisioning — no DevOps for game servers
- Federated architecture: 18,000+ servers, horizontal scaling
- What you can and cannot control at the infrastructure level

---

### 2. Luau Language

#### 2.1 Luau vs Lua 5.1
- Luau is a heavy fork of Lua 5.1, not a superset of later Lua versions
- Runtime-compatible with Lua 5.1 syntax; some post-5.1 features absent or diverge
- Deliberate design divergences based on Roblox platform constraints

#### 2.2 Type System
- Gradual typing — optional annotations coexist with dynamic typing
- State-of-the-art type inference (no annotations required for basic inference)
- Type refinements and type functions (major rework 2024, released 2025)
- Type solver rewrite graduated from beta (Nov 2025)

#### 2.3 Performance & Runtime
- Fast bytecode compiler + interpreter
- Optional JIT (x64, arm64)
- Incremental garbage collector — implications for game loop
- No manual memory management, but GC pressure is a real concern

#### 2.4 Luau-Specific Patterns
- Metatables and OOP in Luau (class simulation)
- Module pattern (ModuleScript + `require()`)
- String interpolation, generalized iteration
- Type-checked interfaces (structural typing)

---

### 3. Development Toolchain

#### 3.1 Rojo — Filesystem Sync
- Maps local directory structures to Roblox DataModel hierarchy
- Bi-directional sync between filesystem and Studio
- `default.project.json` — project configuration format
- Sourcemap generation for IDE integration (`sourcemap.json`)
- Enables Git, code review, CI/CD workflows
- v7+ is current standard

#### 3.2 Wally — Package Manager
- Cargo-inspired; `wally.toml` manifest + `wally.lock` lockfile
- 1200+ packages in the official registry
- Efficient dependency deduplication and linking
- Reproducible builds across team

#### 3.3 Selene — Linter
- Rust-based, fast static analysis for Luau
- Roblox-aware ruleset (knows Roblox globals)
- `selene.toml` configuration, configurable severity per rule
- Integrates into CI and editor workflows

#### 3.4 StyLua — Formatter
- Opinionated Luau formatter (follows Roblox Lua Style Guide with deviations)
- `stylua.toml` config: indentation, column width, quote style, require sorting
- `-- stylua: ignore` inline directives for exceptions
- Supports `.editorconfig` fallback

#### 3.5 Foreman — Toolchain Version Manager
- Manages Rojo, Selene, StyLua, Remodel, etc. versions per project
- `foreman.toml` per-project or global; downloads from GitHub/GitLab
- Equivalent of `asdf` or `rustup` for the Roblox toolchain

#### 3.6 TestEZ — Testing Framework
- BDD-style (describe/it), assertion syntax from Chai
- Run in Studio or headless via Lemur
- **Archived by Roblox (Sept 2024)** — no active development, still widely used

#### 3.7 Argon — Alternative Sync Tool
- Full-featured two-way sync (code AND non-script instances)
- VS Code extension + Roblox plugin architecture
- 100% compatible with Rojo projects (minor migration: `placeId` → `placeIds`)
- Chosen when Rojo's one-way/script-only sync is limiting

---

### 4. VS Code + Roblox Integration

#### 4.1 Luau LSP Extension
- `JohnnyMorganz/luau-lsp` — primary extension (VS Code Marketplace: `Nightrains.robloxlsp`)
- Full diagnostics, type checking, autocompletion, go-to-definition, find references
- Inlay hints, semantic tokens, code actions, quick fixes
- Requires sourcemap from Rojo for DataModel instance resolution

#### 4.2 Rojo + Sourcemap Workflow
- `rojo sourcemap --watch default.project.json --output sourcemap.json`
- LSP reads sourcemap to resolve `game.Workspace.XYZ` paths
- Live reload: file changes sync to Studio in real time

#### 4.3 Project Configuration
- `.luaurc` — strictness, lints, require aliases
- `luau-lsp.*` VS Code settings namespace
- Custom type definition files (external URL support)

#### 4.4 CI/CD Integration
- `luau-lsp analyze` for static analysis in pipelines
- Full Rojo sourcemap resolution in CI
- Selene + StyLua checks in pre-commit or PR pipeline

---

### 5. Claude Code + AI Integration

#### 5.1 Official Roblox MCP Server
- Roblox released open-source MCP Server (2025) — LLMs can read/modify Studio directly
- Capabilities: insert Creator Store models, run Luau code in Studio
- Integrates with Claude Code via standard MCP protocol

#### 5.2 Claude Code Workflow for Roblox Dev
- CLAUDE.md setup: Luau syntax rules, Roblox services, Rojo conventions, RemoteEvent patterns
- Rojo bridges Claude-generated code files into Studio automatically
- Practical: full DataStore systems generated in ~40 seconds
- Cost: ~a few dollars/month at typical usage

#### 5.3 Built-in Roblox Assistant AI
- Integrated in Studio UI; good for basic/intermediate scripting assistance
- Limitations: daily quotas, struggles with complex interconnected systems
- Not a replacement for Claude/external LLMs for experienced developers

#### 5.4 Luau-Specific AI Tools
- **RoCode**: Luau-specific AI assistant; addresses deprecated API issues that plague ChatGPT/Gemini
- General LLMs struggle with Roblox-specific APIs and current patterns

#### 5.5 Building AI Features Into Studio Products
- Open Cloud API for external tooling integration
- HTTP Service for calling external AI APIs from game servers
- Architecture: server-side AI calls → validated results → replicate to clients

---

### 6. Game Dev Architecture (Roblox-Specific)

> These concepts differ fundamentally from full-stack SDLC — the core mental model shift for backend developers.

#### 6.1 Game Loop vs Request-Response
- No HTTP request-response cycle — everything runs in a continuous simulation tick
- RunService Heartbeat replaces "handlers" — logic is tick-driven, not event-triggered from outside
- Time is first-class: delta time, frame budget, physics steps all matter
- Latency is always present — design for it, not around it

#### 6.2 State Machine Architecture
- Fundamental pattern for game flow: rounds, lobbies, matchmaking, character states
- States: `Waiting → Loading → Playing → Results → Reset`
- Per-entity states (character: `Idle → Running → Attacking → Dead`)
- Prevents invalid transitions; organizes branching game logic

#### 6.3 Client-Server Authority Model
- Server = single source of truth; clients send inputs only
- Client prediction for responsiveness (engine auto-predicts physics near player)
- Rollback on misprediction (resimulation)
- Hybrid pattern: server authoritative for logic, client authoritative for UX feel

#### 6.4 RemoteEvent/RemoteFunction Patterns (Your RPC Layer)
- `RemoteEvent` — async, fire-and-forget (equivalent to pub/sub or one-way messaging)
- `RemoteFunction` — synchronous RPC, yields until response (use sparingly — latency + hang risk)
- `UnreliableRemoteEvent` — UDP-like, for high-frequency data where packet loss is acceptable (position updates)
- **Critical rule:** Never RemoteFunction server → client (exploiters can hang server)
- Rate limit: ~500 requests/sec/client shared across RemoteEvents

#### 6.5 Physics Ownership & Network Ownership
- Physics distributed between server and clients to reduce server CPU
- Network owner = entity responsible for computing physics of a part
- Client ownership: low latency, exploit surface; Server ownership: authoritative, higher CPU
- Anchored parts always server-owned
- Anti-cheat consideration: server must validate client-owned physics changes

#### 6.6 Anti-Cheat Architecture
- Server validates all state changes — client cannot modify other players' state
- Position/velocity spoofing caught by server validation
- RemoteFunction exploits: client can hang server by not responding (avoid server → client RF)
- Sensitive objects (bosses, game-critical props) should be server-owned

#### 6.7 Live Ops & Game Updates
- No downtime deploys: Roblox rolls out updates per-server; existing servers run old version
- Players joining new servers get updated version; old servers drain
- DataStore schema migrations must be backward-compatible
- No "maintenance mode" concept — design for rolling updates

#### 6.8 Matchmaking & Lobby Systems
- No built-in matchmaking — custom implementation via TeleportService + MemoryStoreService
- Lobby place → game place pattern
- MemoryStoreService for real-time ephemeral queues (not DataStore)
- Cross-server communication via MessagingService

---

### 7. Frameworks & Design Patterns

#### 7.1 Knit — Service/Controller Pattern
- Eliminates RemoteEvent/Function boilerplate via automatic networking
- Services (server) + Controllers (client) architecture
- **No longer actively maintained** (stopped updates) — battle-tested but static
- Good for: mid-scale games, teams that want simple structure without TypeScript

#### 7.2 Flamework — TypeScript + DI
- TypeScript-first: decorators, dependency injection, modular architecture
- Components (instance-attached, lifecycle hooks), Services (DI singletons), Controllers
- Requires build step (roblox-ts compiler)
- Good for: TypeScript teams, enterprise-scale codebases

#### 7.3 Matter ECS — Entity-Component-System
- Data (components) separated from behavior (systems)
- Entities = IDs with attached components; systems batch-process component queries
- Chunk-based storage, dirty-flag optimization, cache-friendly iteration
- Actively maintained (new org, fork of original)
- Good for: complex game state (100s of enemies, abilities, status effects)

#### 7.4 Custom Module Architecture (No Framework)
- Common in larger studios: custom service layer with manual RemoteEvent management
- ModuleScript-based singleton services
- Lazy-loading and dependency ordering patterns

---

### 8. Data Persistence

#### 8.1 DataStore API
- Key-value store, 4MB max per key
- Rate limits (2026): `250 + (CCU × 40)` requests/min standard; `10 + (CCU × 2)` list ops/min
- Storage cap: `100MB + 1MB per lifetime player` per experience
- Per-experience quotas (shift from per-server) — beneficial for most games

#### 8.2 Session Locking & Player Data Libraries
- **ProfileService** — widely used, session locking, but partially outdated
- **DataKeep** — Promise-based, API-compatible ProfileService migration path
- **ProfileStore** — emerging modern alternative
- Session locking: prevents data loss when player on multiple servers simultaneously

#### 8.3 Cross-Place Data Flow
- DataStore persists across places in same universe
- Session handoff pattern on teleport: save → teleport → load on new place
- MemoryStoreService for ephemeral cross-server state (queues, party state)
- MessagingService for cross-server pub/sub

#### 8.4 Data Schema Design
- No schema enforcement — structure entirely up to developer
- Versioning strategy for schema migrations (backward-compatible required)
- Nested tables as values; no joins, no relational model

---

### 9. Performance & Optimization

#### 9.1 Memory Budget
- Client target: <1000MB
- Frame budget: <20ms/frame (CPU + GPU combined)
- Monitor via Developer Console (F9) and Studio Profiler

#### 9.2 Object Pooling
- Pre-allocate bullets, effects, projectiles — reposition/enable vs spawn/destroy
- Reduces GC pressure and allocation cost
- Essential for high-frequency gameplay (shooting, particles)

#### 9.3 Event Connection Management
- Store connection objects; call `Disconnect()` when done
- Orphaned event listeners persist in memory and keep processing
- Common memory leak source in complex games

#### 9.4 Network Replication Optimization
- BetterReplication framework (V7, April 2025) — 40% packet size reduction
- Custom buffer reader/writer for character replication
- NPC replication: state compression, update throttling, selective replication
- Attribute limits shape what you can sync cheaply

#### 9.5 StreamingEnabled
- Selectively loads/unloads content by player proximity
- Essential for large maps (>600×600 studs) or mobile targets
- Poorly documented behavior; requires game-specific tuning
- Not recommended as default for small, contained experiences

---

### 10. Asset Pipeline (Non-Code)

#### 10.1 Asset Types
- Models (MeshParts, Unions), Animations (AnimationController), Audio, Images/Textures, Decals
- All uploaded to Roblox asset servers; referenced by AssetId
- Creator Store: Roblox's marketplace for assets

#### 10.2 Asset Moderation
- All uploaded assets go through automated (and sometimes manual) moderation
- Can be rejected or taken down post-publish; studio must have fallback assets
- UGC items follow stricter moderation pipeline

#### 10.3 Texture & Mesh Optimization
- Downsize textures to minimum acceptable resolution
- LOD (Level of Detail) for distant meshes
- Asset streaming reduces initial load time (~35% improvement at scale)

---

### 11. Platform APIs & Open Cloud

#### 11.1 In-Game Service APIs
- `DataStoreService` — persistence
- `MemoryStoreService` — ephemeral real-time state
- `MessagingService` — cross-server pub/sub
- `TeleportService` — place-to-place navigation
- `HttpService` — outbound HTTP calls (not inbound)
- `MarketplaceService` — game passes, developer products

#### 11.2 Open Cloud API (External Tooling)
- External REST API for interacting with Roblox data outside of game servers
- DataStore API, Inventory API, Groups API, Engine API (update scripts programmatically)
- Developer Products & Game Passes CRUD
- **Critical limitation:** Open Cloud messages don't reach Studio — only live game servers
- Use cases: studio dashboards, CI/CD tooling, external admin panels

#### 11.3 Cross-Server Communication
- MessagingService: pub/sub across servers in same universe
- MemoryStoreService: shared queues and sorted maps across servers
- No direct server-to-server RPC — message-passing only

---

### 12. Monetization & Economy

#### 12.1 Revenue Mechanisms
- **Game Passes** — one-time permanent purchase; 70% revenue share after fees
- **Developer Products** — repeatable purchases (currency, boosts, consumables)
- **Creator Rewards** (prev. Engagement-Based Payouts) — passive from Premium subscribers
- **UGC / Avatar Marketplace** — clothing, accessories, avatar items

#### 12.2 DevEx (Developer Exchange)
- Convert earned Robux to real USD
- Minimum threshold applies; Roblox takes platform cut
- Requires DevEx eligibility (account age, earnings threshold)

#### 12.3 Economy Design Principles
- Game Passes: permanent value perks (VIP, double XP)
- Developer Products: consumable loop drivers (currency packs, revives)
- Blend monetization naturally into game loop — avoid pay-to-win perception
- Analytics dashboard for monitoring conversion, retention vs spend correlation

---

### 13. Game Dev Concepts That Differ from Full-Stack SDLC

> Mental model shifts specifically for backend/systems developers entering game dev.

#### 13.1 Tick-Based Simulation vs Request-Response
- No idle state — server ticks continuously even with no player input
- CPU cost is always-on, not request-proportional
- Design for frame budget, not throughput

#### 13.2 Spatial Programming
- Objects exist in 3D space; position, rotation, size are first-class
- Collision detection, raycasting, spatial queries replace database queries in many contexts
- Physics as a system — not a library call, but a continuous simulation

#### 13.3 Game Feel vs Correctness
- Perceived latency matters as much as actual latency
- Client prediction / lag compensation are standard, not optional
- Visual and audio feedback loops are part of architecture, not afterthought

#### 13.4 Hitbox Design & Lag Compensation
- Hitbox = collision volume for damage/interaction detection
- Lag compensation: server rewinds positions to client's perceived time to validate hits
- Roblox provides basic tools; complex games implement custom hitbox systems

#### 13.5 No Horizontal Scaling Control
- You don't provision servers — Roblox does
- Scale = more players joining = Roblox spins new server instances
- No load balancing config, no server affinity, no custom routing

#### 13.6 Versioned Live Game (No Downtime)
- Updates publish to new servers; old servers drain naturally
- No rollback mechanism — must forward-fix
- Feature flags via DataStore/RemoteConfig patterns for safe rollout

---

## Sources Summary

- Roblox Creator Docs: https://create.roblox.com/docs
- Roblox DevForum: https://devforum.roblox.com
- Luau Language: https://luau.org
- Rojo: https://rojo.space/docs/v7/
- Wally: https://wally.run
- Selene: https://kampfkarren.github.io/selene/
- StyLua: https://github.com/JohnnyMorganz/StyLua
- Luau LSP: https://github.com/JohnnyMorganz/luau-lsp
- Matter ECS: https://matter-ecs.github.io/matter/
- Flamework: https://flamework.fireboltofdeath.dev/
- Knit: https://sleitnick.github.io/Knit/
- Open Cloud API: https://create.roblox.com/docs/cloud
- BetterReplication: https://github.com/PogoDigitalism/BetterReplication
- Claude Code + Roblox: https://roxlit.dev/blog/how-to-use-claude-code-with-roblox
- Roblox MCP Server: https://devforum.roblox.com/t/introducing-the-open-source-studio-mcp-server/3649365
