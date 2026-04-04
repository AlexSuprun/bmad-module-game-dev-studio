# Story 1.2: Create Roblox Engine Knowledge File

## Story

**As a** game architect using the BMGD architecture workflow,
**I want** a comprehensive Roblox engine knowledge file loaded when Roblox is selected,
**So that** I receive expert-level Roblox architecture guidance including Instance-Service model, Luau best practices, security patterns, and platform caveats.

## Status

ready-for-dev

## Acceptance Criteria

**Given** `roblox-engine.md` is created at `src/workflows/3-technical/gds-game-architecture/knowledge/`
**When** the architecture workflow loads it for a Roblox project
**Then** the file contains all 12 sections matching the engine template structure:

1. Overview (prose paragraph: Roblox platform, Luau, client-server, managed hosting)
2. Architecture Fundamentals (Instance-Service model, DataModel hierarchy diagram, client-server split, ModuleScript architecture, Rojo project structure)
3. Luau Scripting Guide (type system, `--!strict` mode, `export type`, `task` library, string interpolation, generalized iteration)
4. Coding Best Practices (service access caching, event-driven vs polling, RemoteEvent patterns, `task` library usage, naming conventions, project structure)
5. Performance Optimization (general, rendering/StreamingEnabled, physics, memory/networking, Luau tips)
6. Popular Third-Party Packages (tables: Essential Development, Data Persistence, Gameplay Systems, Networking, Build Tools — with ProfileStore as primary data recommendation, React-Lua not Roact, ByteNet not BridgeNet2)
7. Common Architectural Patterns (Service-Controller/Knit, Bootstrap Pattern, Object Pool — with full Luau code examples)
8. Security Architecture (never trust client, RemoteEvent validation pattern, common exploit vectors, mitigation patterns)
9. Licensing and Cost (free Studio, free hosting, 30% marketplace fee, DevEx $0.0038/Robux)
10. When NOT to Use Roblox (7+ scenarios: platform lock-in, no custom shaders, no offline, limited physics, content restrictions, monetization constraints, no self-hosting)
11. Platform-Specific Caveats (Mobile 60%+, Xbox, PlayStation, Meta Quest, PC/Mac, cross-platform UI)
12. Architecture Decision Checklist (13 checkbox items)

**And** code examples use Luau with `--!strict` annotations and PREFERRED/AVOID comments
**And** no deprecated libraries appear as primary recommendations
**And** the file has no YAML frontmatter and no cross-references to other BMGD files
**And** the file passes `npm run lint:md`

## Tasks/Subtasks

- [ ] Task 1: Create `src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md`
  - [ ] Section 1: Overview (3-5 sentence prose paragraph)
  - [ ] Section 2: Architecture Fundamentals (Instance-Service model, DataModel hierarchy, client-server, ModuleScript, Rojo)
  - [ ] Section 3: Luau Scripting Guide (type system, `--!strict`, `export type`, `task` library)
  - [ ] Section 4: Coding Best Practices (6 H3 subsections with PREFERRED/AVOID code blocks)
  - [ ] Section 5: Performance Optimization (5 H3 subsections: General, Rendering, Physics, Memory/Networking, Luau tips)
  - [ ] Section 6: Popular Third-Party Packages (5 table categories with updated library status)
  - [ ] Section 7: Common Architectural Patterns (3 patterns: Knit service-controller, bootstrap, object pool)
  - [ ] Section 8: Security Architecture (client trust model, validation pattern, exploit vectors, mitigations)
  - [ ] Section 9: Licensing and Cost (DevEx $0.0038/Robux, 30% marketplace fee)
  - [ ] Section 10: When NOT to Use Roblox (7 bullet scenarios)
  - [ ] Section 11: Platform-Specific Caveats (6 platforms)
  - [ ] Section 12: Architecture Decision Checklist (13 items)
- [ ] Task 2: Verify `npm run lint:md` passes with zero errors

## Dev Notes

### Target File

```
src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md
```

This directory already contains: `godot-engine.md`, `phaser-engine.md`, `unity-engine.md`, `unreal-engine.md`. The new file must match their format exactly.

### Existing Engine File Format — MUST FOLLOW

All four existing engine knowledge files share these structural rules (verified by analyzing all four):

- **No YAML frontmatter** — first line is `# [Engine Name] Engine Architecture Knowledge` (H1)
- **No cross-references** to other BMGD files — self-contained reference docs
- **No imports, includes, or links** to other BMGD documents (external links are fine)
- **12 top-level H2 sections** in this order (H2 = `##`)
- **H3 subsections** (`###`) within each H2
- **Code blocks** use language tags (` ```luau ` for Luau code, plain ` ``` ` for directory trees)
- **Tables** use `| Column | Column | Column |` markdown format with `|---|---|---|` separator row
- **Checkboxes** are `- [ ]` format (last section)
- Approximately **550-650 lines** total

### The 12 Required Sections (Exact H2 Titles)

Use these EXACT H2 headings in this EXACT order:

```
## Overview
## Architecture Fundamentals
## Luau Scripting Guide
## Coding Best Practices
## Performance Optimization
## Popular Third-Party Packages
## Common Architectural Patterns
## Security Architecture
## Licensing and Cost
## When NOT to Use Roblox
## Platform-Specific Caveats
## Architecture Decision Checklist
```

### Section-by-Section Content Guide

#### Section 1: Overview

Single prose paragraph (3-5 sentences). Pattern from other files:
> "[Engine] is a [license/type] [engine/platform] using [core architecture] with [language]. [2-3 more sentences about key features, use cases, and platform scope]."

**Content to cover:** Roblox is a managed cloud gaming platform, Instance-Service hierarchy, Luau scripting, mandatory client-server architecture, all games ("experiences") run on Roblox-hosted servers, built-in physics/rendering/networking/data persistence, cross-platform deployment (PC, Mac, iOS, Android, Xbox, PlayStation, Meta Quest), Roblox Studio as IDE, Rojo for filesystem-based workflows.

#### Section 2: Architecture Fundamentals

Required H3 subsections in order:

**`### Instance-Service Model`** — Key concepts bullet list with `**bold term**` — explanation:
- **Instance** — Base class for all DataModel objects. Has Name, Parent, Children. Supports `FindFirstChild`, `GetChildren`, `GetDescendants`
- **Service** — Top-level singleton containers accessed via `game:GetService("ServiceName")`
- **Script** — Server-side execution context (runs in ServerScriptService or Workspace)
- **LocalScript** — Client-side execution context (runs in StarterPlayerScripts, StarterGui, StarterCharacterScripts)
- **ModuleScript** — Shared code module returning a single value via `require()`. Runs in context of requiring script

**`### DataModel Hierarchy`** — ASCII tree diagram in plain code fence:
```
game (DataModel)
├── Workspace                     -- 3D world, physics, characters
│   ├── Terrain
│   ├── SpawnLocation
│   └── [Game objects, NPCs, etc.]
├── ServerScriptService           -- Server Scripts (server-only)
├── ServerStorage                 -- Server-only assets and modules
├── ReplicatedStorage             -- Shared modules and assets (server + client)
├── ReplicatedFirst               -- Client assets that load first
├── StarterGui                    -- UI templates cloned to PlayerGui
├── StarterPlayerScripts          -- LocalScripts cloned to each player
├── StarterCharacterScripts       -- Scripts cloned to each character
├── StarterPack                   -- Tools given to each player
├── Players                       -- Connected player objects
│   └── Player
│       ├── PlayerGui             -- Active UI instances
│       ├── PlayerScripts         -- Running LocalScripts
│       ├── Backpack              -- Tools
│       └── leaderstats           -- Leaderboard values
├── Lighting                      -- Lighting and atmosphere
├── SoundService                  -- Global audio settings
└── Chat                          -- Chat system
```

**`### Client-Server Architecture (Filtering Enabled)`** — bullet list:
- All experiences use mandatory server-authoritative model
- Server changes replicate to all clients automatically
- Client changes do NOT replicate to server (security model)
- Communication via RemoteEvents (async one-way) and RemoteFunctions (sync two-way)
- Scripts in ServerScriptService run ONLY on server
- LocalScripts in StarterPlayerScripts/StarterGui run ONLY on client
- ModuleScripts in ReplicatedStorage run in the context of whoever requires them

**`### ModuleScript Architecture`** — Luau code block showing a ModuleScript returning a typed table module (see code in research). Show `export type` usage, `local ModuleName = {}`, function definitions, `return ModuleName` pattern.

**`### Rojo Project Structure`** — plain code fence showing `src/` directory tree with `server/`, `client/`, `shared/`, and `assets/` subdirectories, mapping to DataModel locations. Include:
- `src/server/` → ServerScriptService
- `src/client/` → StarterPlayerScripts
- `src/shared/` → ReplicatedStorage
- Convention: `init.server.luau` → Script, `init.client.luau` → LocalScript, `init.luau` → ModuleScript

#### Section 3: Luau Scripting Guide

This replaces the "GDScript vs C# Decision Guide" from Godot or "Blueprint vs C++" from Unreal. Roblox has only Luau.

Cover as H3 subsections or a unified guide:
- Gradual type system: optional annotations with `:` separator
- `--!strict` mode for compile-time type checking (also a performance benefit: Luau JIT uses type info to skip tag checks)
- `export type` for cross-module type sharing
- String interpolation: `` `Hello {playerName}` ``
- Generalized iteration: `for k, v in table do` (no `ipairs`/`pairs` needed in modern Luau)
- `task` library overview: `task.spawn`, `task.defer`, `task.delay`, `task.wait`, `task.cancel`
- Native code generation for performance-critical paths

**Code block required:** Show `--!strict` at top, `export type` for two types (e.g., `WeaponConfig`, `PlayerData`), and a typed function. About 30 lines.

#### Section 4: Coding Best Practices

Six H3 subsections, each with a Luau code block using `-- PREFERRED:` / `-- AVOID:` comments:

**`### Service Access and References`**
- Cache `game:GetService()` calls at top of script, never inside loops
- Use `WaitForChild` for client-side references
- AVOID: `game.Workspace` shorthand, assuming children exist on client without `WaitForChild`

**`### Event-Driven vs Polling`**
- PREFERRED: `.Changed` signals, `GetPropertyChangedSignal`, event connections
- AVOID: `while true do` polling loops

**`### RemoteEvent Patterns`**
- Server ALWAYS validates: type check args → rate limit → sanity check → state validation → execute
- Show the validation pattern code block (5-step server handler)

**`### task Library Usage`**
- `task.spawn` — immediate new thread
- `task.defer` — end of current resumption cycle
- `task.delay` — scheduled future execution
- `task.wait` — yield for duration
- AVOID: deprecated `wait()`, `spawn()`, `delay()`

**`### Naming Conventions`** — bullet list (no code block needed):
- Files/folders (Rojo): `PascalCase` for modules (`CombatService.luau`)
- Variables and functions: `camelCase`
- Types: `PascalCase` (`export type WeaponConfig`)
- Constants: `UPPER_SNAKE_CASE`
- Services: `PascalCase` (matches Roblox API)
- Private members: `_` prefix (`self._internalState`)
- RemoteEvents/Functions: `PascalCase` matching action (`PurchaseItem`, `RequestSpawn`)

**`### Project Structure Conventions`** — Studio-only workflow tree in plain code fence showing ServerScriptService, ReplicatedStorage, StarterPlayerScripts, StarterGui with typical children

#### Section 5: Performance Optimization

Five H3 subsections (bullet lists, 1-2 code examples):

**`### General Guidelines`**
- Replace `while wait() do` loops with event-driven signals
- Use `task.spawn`/`task.defer` for non-blocking operations
- Object pool frequently spawned instances (projectiles, effects, coins)
- Use `Debris:AddItem(instance, lifetime)` for timed cleanup
- Limit per-frame computation; throttle with heartbeat budgets
- Cache `GetService()` at script top, never in loops
- Use `CollectionService` tags for batch processing similar objects

**`### Rendering Performance`**
- **StreamingEnabled** — loads only nearby content, reduces initial load ~35%; essential for mobile
- **LOD** — reduce mesh complexity at distance (up to 50% FPS improvement)
- Limit models to ~500 parts for optimal performance
- Use `MeshPart` over unions of `Part` instances
- Texture atlasing and mesh simplification reduce GPU load ~30%
- Reduce particle emitter counts and rates on mobile
- Lighting modes: **Future** for best quality, **ShadowMap** for mid-range, **Compatibility** for low-end

**`### Physics Performance`**
- Anchor static parts (anchored parts skip physics simulation entirely)
- Simplify collision geometry
- Use `CanCollide = false` + `CanQuery = true` for trigger volumes instead of physics bodies
- Manage network ownership carefully — `SetNetworkOwner(nil)` for server-controlled physics
- Use constraints (AlignPosition, AlignOrientation) instead of CFrame manipulation for smooth movement

**`### Memory and Networking`**
- Minimize RemoteEvent frequency — batch updates where possible
- Use `UnreliableRemoteEvent` for high-frequency non-critical data (positions, animations)
- Clean up connections with `:Disconnect()` to prevent memory leaks
- Use `:Destroy()` on instances when done (not just parenting to nil)
- Profile with **MicroProfiler** (Ctrl+F6) for per-frame CPU breakdown
- Use **Developer Console** (F9) for memory, network, and script stats

**`### Luau Performance Tips`**
- Use `--!strict` — type annotations enable JIT optimizations (avoids tag checks)
- Avoid creating tables in hot loops (pre-allocate and reuse)
- Use `table.create(n, value)` for pre-sized arrays
- `string.format` is faster than concatenation in loops
- Local variables are faster than global/upvalue access in tight loops
- `ipairs` is unnecessary in modern Luau — use generalized `for i, v in array do`

#### Section 6: Popular Third-Party Packages

Five H3 categories, each with a Markdown table (`| Package | Purpose | Source |`).

**CRITICAL LIBRARY STATUS — DO NOT GET THESE WRONG:**

| Library | Status | Use Instead |
|---|---|---|
| ProfileService | **Legacy/unmaintained** | ProfileStore (same author, successor) |
| Roact | **Officially deprecated** | react-lua (`jsdotlua/react`) |
| BridgeNet2 | **Archived** | ByteNet |
| Knit | **No longer actively maintained** | Still functional, note status; not in default stacks |

**`### Essential Development`**

| Package | Purpose | Source |
|---|---|---|
| **Knit** | Service-Controller framework (server services + client controllers) — no longer actively maintained | Wally (`sleitnick/knit`) |
| **Fusion** | Reactive UI library with state management | Wally (`dphfox/fusion`) |
| **react-lua** | React port for Luau (replaces deprecated Roact) | Wally (`jsdotlua/react`) |
| **TestEZ** | BDD-style testing framework (describe/it syntax) | Wally (`roblox/testez`) |
| **Promise** | Async promise implementation for Luau | Wally (`evaera/promise`) |

**`### Data Persistence`**

| Package | Purpose | Source |
|---|---|---|
| **ProfileStore** | Robust player data with session locking — recommended successor to ProfileService | Wally / GitHub (MadStudioRoblox) |
| **ProfileService** | Player data (legacy, widely used, unmaintained) | Wally (`madstudioroblox/profileservice`) |
| **Lapis** | DataStore abstraction with schema validation (not yet battle-tested in large production) | Wally (`nezuo/lapis`) |
| **DataStoreService** | Built-in native DataStore (no external dependency) | Roblox built-in |

**`### Gameplay Systems`**

| Package | Purpose | Source |
|---|---|---|
| **Jecs** | Fast ECS (800K entities at 60fps, entity relationships) | Wally (`ukendio/jecs`) |
| **Matter** | ECS with hot reloading and debugging | Wally (`matter-ecs/matter`) |
| **SimplePath** | Pathfinding wrapper for NPC navigation | Wally / GitHub |
| **TopbarPlus** | Custom topbar icon creation | Wally (`1foreverhd/topbarplus`) |

**`### Networking`**

| Package | Purpose | Source |
|---|---|---|
| **ByteNet** | Performant networking with strict type safety (replaces archived BridgeNet2) | Wally |
| **Red** | Networking library with middleware support | Wally |
| **Comm** | RemoteEvent/Function wrapper with type safety (by Knit author) | Wally (`sleitnick/comm`) |

**`### Build and Quality Tools`**

| Package | Purpose | Source |
|---|---|---|
| **Rojo** | Filesystem-to-Studio sync, enables Git workflows | CLI / Rokit |
| **Wally** | Package manager for Roblox libraries | CLI / Rokit |
| **Selene** | Luau linter for code quality | CLI / Rokit |
| **StyLua** | Luau code formatter | CLI / Rokit |
| **Lune** | Standalone Luau runtime for scripts and automation | CLI / Rokit |
| **Rokit** | Toolchain manager (manages Rojo, Wally, Selene, StyLua, Lune) | GitHub |

#### Section 7: Common Architectural Patterns

Three H3 patterns, each with full Luau code implementation (~20-40 lines):

**`### Service-Controller Pattern (Knit)`** — Show both server service (`CoinService.luau`) and client controller (`CoinController.luau`). Full Knit API usage: `Knit.CreateService`, `Knit.CreateSignal`, `KnitStart`, `Client` table for exposed methods, `Knit.CreateController`, `Knit.GetService`.

**`### Bootstrap Pattern (Single Entry Point)`** — Show `server/init.server.luau` that loads all services from a folder and calls `Knit.Start()`. This is the standard "single script architecture" pattern that prevents script execution order issues.

**`### Object Pool Pattern`** — Show `shared/modules/ObjectPool.luau` with `ObjectPool.new(template, initialSize)`, `ObjectPool:Get()`, and `ObjectPool:Return(obj)` methods. Use `setmetatable` with `__index`. Show `table.remove`/`table.insert` for the pool queue.

#### Section 8: Security Architecture

This is the Roblox-specific section replacing "Godot 4 vs 3" or "Blueprint vs C++". Security is uniquely critical in Roblox because exploiters can run arbitrary code on the client.

Required H3 subsections:

**`### Core Principle — Never Trust the Client`** — bullets:
- All game-critical logic (damage, currency, progression) MUST run server-side
- Client predicts for responsiveness but server validates and reconciles
- Exploiters can modify any client-side value, fire any RemoteEvent, read any client code

**`### RemoteEvent Validation Pattern`** — code block showing 5-step server handler:
1. Type check all arguments
2. Rate limit (track per-player timestamps)
3. Sanity check (is action physically possible?)
4. State validation (does player have required resources?)
5. Execute on server

**`### Common Exploit Vectors`** — bullets:
- Input manipulation (altered health, currency, inventory)
- RemoteEvent spam (thousands of fires per second)
- Network ownership abuse (teleportation via physics ownership)
- Memory reading (all client-side code and data is visible)

**`### Mitigation Patterns`** — bullets:
- Rate limiting on all RemoteEvent handlers
- Server-side cooldowns for player actions
- Distance/physics sanity checks
- Never store authoritative game state on client
- Never expose debug/admin RemoteEvents in production
- Use `SetNetworkOwner(nil)` for server-controlled physics objects

#### Section 9: Licensing and Cost

Prose + bullet list. Lead with model name in bold:

Roblox uses a **free-to-develop, revenue-share model**:
- Roblox Studio: free, no per-seat fees
- Game hosting: free (Roblox provides all server infrastructure)
- Publishing: free via Studio or Open Cloud API
- Marketplace fee: 30% on all in-game transactions (GamePasses, Developer Products, Subscriptions)
- Developer Exchange (DevEx): cash out at **$0.0038 USD per Robux** (increased 8.6% September 2025, first increase since 2017)
- DevEx minimum: 30,000 earned Robux ($114 USD)
- No royalty on revenue (unlike Unreal's 5% model)

Important caveats:
- Roblox controls the economy — rates can change
- DevEx requires Premium subscription and compliance with DevEx terms
- Verify current rates before making revenue projections

#### Section 10: When NOT to Use Roblox

7 bullets using `- **Bold scenario** — explanation` pattern:
1. **Non-Roblox platform targets** — games only run on Roblox platform; no Steam, independent App Store, or open web export
2. **Custom rendering or visual fidelity beyond Roblox's limits** — cannot write custom shaders, use ray tracing, or achieve photorealistic graphics
3. **Offline or local-only games** — requires internet connection and Roblox account; no offline single-player
4. **Precise physics simulation** — adequate for gameplay, not suitable for physics-puzzle games requiring deterministic simulation
5. **Mature or graphic content** — content policies prohibit violence gore, sexual content, gambling, mature themes
6. **Full code ownership and control** — cannot self-host, mod server binary, or access engine source
7. **Monetization outside Roblox's economy** — cannot integrate Stripe, PayPal, or custom payment; all monetization through Robux

#### Section 11: Platform-Specific Caveats

6 platforms using `- **Platform:** caveat details` pattern:
1. **Mobile (iOS/Android):** ~60%+ of players; touch-primary UI design; target 30fps on low-end; StreamingEnabled essential; test on real devices
2. **Xbox:** Gamepad-only input; must pass Xbox UX certification; use ContextActionService for gamepad bindings; full D-pad navigation required
3. **PlayStation (PS4/PS5):** Launched October 2024; gamepad-only; PS-specific button prompts (cross/circle swap in some regions)
4. **Meta Quest (VR):** Controller + head tracking input; UI must handle 3D space; Quest is mobile-class hardware; available for users 13+
5. **PC/Mac:** Most capable platform; keyboard + mouse; highest performance budget; still design for mobile lowest common denominator
6. **Cross-platform UI:** Use `UserInputService:GetPlatform()` and `UserInputService.TouchEnabled`; scale UI with `UIScale` and `AutomaticSize`; show appropriate button prompts per platform

#### Section 12: Architecture Decision Checklist

13 checkbox items using `- [ ]` format. This MUST be the FINAL section:
1. Client-server split planned: which logic runs server-side vs client-side
2. Data persistence approach selected: ProfileStore, Lapis, or raw DataStoreService
3. Networking pattern: RemoteEvents with validation, or framework (Knit, ByteNet)
4. UI approach: Native ScreenGui, Fusion (reactive), or react-lua (React-style)
5. Framework decision: Knit (service-controller), ECS (Jecs/Matter), or custom
6. Development workflow: Rojo + Git (recommended) or Studio-only
7. Package management: Wally configured with required dependencies
8. Lighting technology selected: Future, ShadowMap, or Compatibility
9. Cross-platform input strategy: touch, keyboard, gamepad, VR
10. Monetization plan: GamePasses, Developer Products, Subscriptions
11. Security architecture: server validation, rate limiting, anti-exploit patterns
12. Testing approach: TestEZ, MCP-based testing, or manual playtesting
13. CI/CD pipeline: Selene + StyLua + rojo build + Open Cloud publishing

### Code Style Requirements

- All Luau code blocks must start with `--!strict` at the top (or show it where relevant)
- Use PREFERRED/AVOID comments in Best Practices section
- `export type` for cross-module type definitions
- Use `task.*` library, never deprecated `wait()`, `spawn()`, `delay()`
- Variable names: `camelCase` for locals, `PascalCase` for types and modules
- No Roblox-deprecated patterns: no `game.Workspace`, no legacy `wait()`, no `:findFirstChild()` (old API)

### Lint Requirements

```bash
npm run lint:md
```

Must pass with zero errors. Check existing files to understand what rules apply — the key ones from `.markdownlint.json` in this project:
- No trailing spaces
- Blank line required before/after headings
- Consistent list indentation
- Code fences must have language specified (use `luau` for Luau code, leave blank for directory trees)
- Max heading level increment of 1

### Anti-Patterns to Avoid

**DO NOT include:**
- YAML frontmatter (no `---` at top of file)
- Links to other BMGD files (no `../`, no `[see architecture.md]`)
- BridgeNet2 as primary networking recommendation (it's archived — use ByteNet)
- Roact as UI recommendation (deprecated — use react-lua)
- ProfileService as primary data recommendation (legacy — use ProfileStore)
- Knit as a default stack recommendation (no longer actively maintained)
- `wait()`, `spawn()`, `delay()` in code examples (deprecated — use `task.*`)
- `game.Workspace` shorthand (use `game:GetService("Workspace")` or `workspace` global)

**DO include as options (but note status):**
- Knit — list it (widely used, functional) but note it's no longer actively maintained
- ProfileService — list it under Data Persistence as legacy option
- DataStoreService — list it as built-in option alongside ProfileStore

### Previous Story Learnings (Story 1.1)

Story 1.1 was a simple single-file YAML change. Key learnings applicable here:
- `npm run lint` (JS/YAML) and `npm run lint:md` (markdown) are separate commands
- `npm run lint:md` is the relevant linter for this story
- The project uses eslint-plugin-yml for YAML lint — not relevant here
- No test files needed for knowledge files (no executable logic)
- Story 1.1 had a deferred review finding noting "knowledge files absent for roblox" — this story directly resolves that gap

### Git Context

Recent commits confirm:
- `a7c86e0` — `src/module.yaml` updated with roblox option (Story 1.1 complete)
- `428c528` — Research documents added to `_bmad-output/planning-artifacts/research/`
- The worktree is on `feat/roblox-support` branch

### File Size Target

~550-650 lines (consistent with `godot-engine.md` at 505 lines and `phaser-engine.md` at 653 lines). The Security Architecture section is unique to Roblox and adds ~40 lines beyond other engine files.

## Dev Agent Record

### Implementation Plan

Create `src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md` with all 12 sections following the exact structure of existing engine files. No other files need modification. Validate with `npm run lint:md`.

### Debug Log

_Empty_

### Completion Notes

_Empty_

## File List

- src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md

## Change Log

_Empty_
