# Roblox Engine Knowledge File Research

**Date:** 2026-04-02
**Purpose:** Deep analysis of existing engine knowledge file templates + Roblox-specific content gathered through web research, to inform creation of `roblox-engine.md`.

---

## Template Analysis

### Common Structure Across All Engine Files

All four engine knowledge files follow a consistent template with the same top-level sections, adapted per engine. Below is the canonical section structure derived from analyzing `unity-engine.md` (573 lines), `godot-engine.md` (505 lines), `unreal-engine.md` (537 lines), and `phaser-engine.md` (653 lines).

| # | Section (H2) | Content Type | Avg Lines | Present In |
|---|---|---|---|---|
| 1 | **Overview** | Prose paragraph (3-5 sentences) | 5-8 | All 4 |
| 2 | **Architecture Fundamentals** | H3 subsections, bullet lists, code blocks, hierarchy diagrams, tables | 100-160 | All 4 |
| 3 | **[Language] Decision Guide** (optional) | Comparison table | 20-30 | Godot (GDScript vs C#), Unreal (BP vs C++), Phaser (TS vs JS) |
| 4 | **Coding Best Practices** | H3 subsections, code blocks with comments, naming conventions, project structure tree | 100-150 | All 4 |
| 5 | **Performance Optimization** | H3 subsections (General, Rendering, Physics, Memory, Language-specific tips), bullet lists, occasional code | 60-100 | All 4 |
| 6 | **Popular Third-Party [Packages/Plugins]** | H3 categories, Markdown tables (Name / Purpose / Source) | 50-80 | All 4 |
| 7 | **Common Architectural Patterns** | H3 per pattern, code blocks showing implementation | 60-120 | All 4 |
| 8 | **[Engine-Specific Section]** (optional) | Varies | 20-40 | Unity: Rendering Pipeline Selection; Godot: Godot 4 vs 3; Unreal: GAS Overview; Phaser: Browser Considerations, Perf Ceiling |
| 9 | **Licensing and Cost** | Prose + bullet list | 15-25 | All 4 |
| 10 | **When NOT to Use [Engine]** | Bullet list with bold lead + explanation | 15-25 | All 4 |
| 11 | **Platform-Specific Caveats** | Bullet list per platform | 15-30 | All 4 (Godot adds "Console Export Reality" H2) |
| 12 | **Architecture Decision Checklist** | Checkbox markdown list | 10-15 | All 4 |

### Detailed Section-by-Section Breakdown

#### 1. Overview (H2)
- **Content type:** Single prose paragraph
- **Pattern:** "[Engine] is a [license type] [engine/framework] using [core architecture pattern] with [language]. [2-3 more sentences about key features and use cases]."
- **Line count:** 3-5 lines
- **No code, no tables, no bullets**

#### 2. Architecture Fundamentals (H2)
- **Content type:** Multiple H3 subsections
- **Typical H3s:**
  - Core model explanation (GameObject-Component / Scene Tree / Actor-Component / Scene-Based)
  - Key concepts bullet list with **bold term** + dash + explanation
  - Hierarchy/tree diagram in code fence (ASCII art)
  - Lifecycle callbacks (code fence with arrow annotations)
  - Rules/best practices as bullet list
  - Data architecture pattern (ScriptableObject / Resource / DataTable / Registry)
  - Code examples: 1-3 substantial blocks (20-40 lines each) in the engine's primary language
- **Line count:** 100-160 lines
- **Largest section in every file**

#### 3. Language/Scripting Decision Guide (H2, optional)
- **Content type:** Comparison table (7-10 rows) + recommendation prose + code example
- **Present in:** Godot (GDScript vs C#), Unreal (Blueprint vs C++), Phaser (TypeScript vs JavaScript)
- **Unity omits this** (C# only)
- **Line count:** 20-40 lines

#### 4. Coding Best Practices (H2)
- **Content type:** Multiple H3 subsections with code blocks
- **Typical H3s:**
  - Component/node communication patterns (with PREFERRED/AVOID comments)
  - Serialization/export patterns
  - Async patterns (coroutines, async/await, task library)
  - Dependency injection / service locator
  - Project structure conventions (directory tree in code fence)
  - Naming conventions (bullet list)
- **Code examples:** 4-8 blocks, each 10-25 lines, heavily commented
- **Line count:** 100-150 lines

#### 5. Performance Optimization (H2)
- **Content type:** H3 subsections with bullet lists
- **Standard H3s across all files:**
  - General Guidelines (bullet list, 8-12 items)
  - Rendering Performance (bullet list)
  - Physics Performance (bullet list)
  - Memory and Loading (bullet list + 1 code example)
  - [Language] Performance Tips (bullet list)
- **Code examples:** 1-2 blocks (async loading pattern)
- **Line count:** 60-100 lines

#### 6. Popular Third-Party Packages/Plugins (H2)
- **Content type:** H3 categories + Markdown tables
- **Table columns:** Always `| Package/Plugin | Purpose | Source |`
- **Standard H3 categories:**
  - Essential Development
  - Gameplay Systems
  - Visual and Audio
  - Networking
  - Infrastructure / Tools (varies)
- **Each table:** 4-6 rows
- **Line count:** 50-80 lines

#### 7. Common Architectural Patterns (H2)
- **Content type:** H3 per pattern with code block
- **Typical patterns:** 2-4 patterns per engine
  - Unity: Singleton, Object Pool, Bootstrap Scene
  - Godot: Component Composition, Event Bus, Scene Transition
  - Unreal: Subsystem, Data Tables, GAS Overview
  - Phaser: Custom GameObject, Scene Communication, State Machine, Responsive Scaling
- **Code examples:** Full implementation blocks (20-40 lines each)
- **Line count:** 60-120 lines

#### 8. Engine-Specific Sections (H2, optional)
- Varies per engine. Examples:
  - Unity: "Rendering Pipeline Selection" (table)
  - Godot: "Godot 4 vs Godot 3 Key Differences" (migration table)
  - Unreal: GAS Overview integrated into patterns
  - Phaser: "Browser and Platform Considerations", "Performance Ceiling and WebAssembly"
- **Line count:** 20-60 lines

#### 9. Licensing and Cost (H2)
- **Content type:** Prose + bullet list
- **Pattern:** License model name in bold, then bullets for tiers/costs
- **Always includes:** Revenue thresholds, per-seat fees (or lack thereof), cost implications bullets
- **Line count:** 15-25 lines

#### 10. When NOT to Use [Engine] (H2)
- **Content type:** Bullet list
- **Pattern:** `- **Bold scenario** -- explanation of why engine is wrong choice`
- **Typically 5-7 bullets**
- **Line count:** 15-25 lines

#### 11. Platform-Specific Caveats (H2)
- **Content type:** Bullet list per platform
- **Pattern:** `- **Platform:** caveat details`
- **Godot adds a separate "Console Export Reality" H2 section**
- **Line count:** 15-30 lines

#### 12. Architecture Decision Checklist (H2)
- **Content type:** Markdown checkbox list
- **Pattern:** `- [ ] Decision item`
- **Always the final section**
- **10-12 items typical**
- **Line count:** 12-15 lines

### Cross-References to Other BMGD Files
- Engine knowledge files do **not** cross-reference other BMGD files directly
- They are self-contained reference documents
- No YAML frontmatter in any engine file
- No imports, includes, or links to other BMGD documents
- The files are loaded dynamically by the architecture workflow based on engine selection in `project-context.md`

### Code Example Patterns
- **Unity:** C# (PascalCase, attributes, generics) -- 10+ code blocks
- **Godot:** GDScript (snake_case, @export, signals) -- 10+ code blocks
- **Unreal:** C++ (UPROPERTY macros, UFUNCTION, templates) + C# (Build.cs) -- 8+ code blocks
- **Phaser:** TypeScript (class-based, Phaser types, config objects) -- 8+ code blocks
- **Complexity:** Ranges from simple 5-line snippets to full 40-line class implementations
- **Style:** Always includes inline comments explaining WHY, not just WHAT
- **PREFERRED/AVOID pattern:** Coding best practices sections use explicit PREFERRED/AVOID comments to guide AI

---

## Roblox Content per Section

### Section 1: Overview

**Template requirement:** 3-5 sentence prose paragraph describing engine, core architecture, language, key features.

**Roblox content:**

> Roblox is a managed cloud gaming platform using an Instance-Service hierarchy with Luau scripting and a mandatory client-server architecture. All games ("experiences") run on Roblox-hosted servers with automatic replication to connected clients. The engine provides built-in physics, rendering (Future/ShadowMap/Compatibility lighting), networking, UI, data persistence (DataStoreService), and cross-platform deployment to PC, Mac, iOS, Android, Xbox, PlayStation, and Meta Quest from a single codebase. Development uses Roblox Studio as the primary IDE, with Rojo enabling filesystem-based workflows compatible with external editors and Git version control.

**Sources:**
- [Roblox Creator Hub - Data Model](https://create.roblox.com/docs/projects/data-model)
- [Rojo Documentation](https://rojo.space/docs/v7/)

**Gaps:** None. Ready to write.

**Readiness: READY**

---

### Section 2: Architecture Fundamentals

**Template requirement:** Multiple H3 subsections covering: core model (key concepts bullet list), hierarchy diagram, lifecycle/execution model, module/code organization, data architecture pattern. 100-160 lines with 1-3 substantial code blocks.

**Roblox content:**

**H3: Instance-Service Model**
- **Instance** -- Base class for all objects in the DataModel tree. Has Name, Parent, Children. Supports hierarchical queries (FindFirstChild, GetChildren, GetDescendants)
- **Service** -- Top-level singleton containers (Workspace, ReplicatedStorage, ServerScriptService, etc.). Accessed via `game:GetService("ServiceName")`
- **Script** -- Server-side Luau execution context. Runs in ServerScriptService or Workspace
- **LocalScript** -- Client-side Luau execution context. Runs in StarterPlayerScripts, StarterGui, StarterCharacterScripts
- **ModuleScript** -- Shared code module. Returns a single value via `require()`. Runs in the context of the requiring script

**DataModel hierarchy diagram:**
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
├── Chat                          -- Chat system
└── [Other Services]              -- TweenService, RunService, etc.
```

**H3: Client-Server Architecture (Filtering Enabled)**
- All Roblox experiences use a mandatory server-authoritative model
- Server changes replicate to all clients automatically
- Client changes do NOT replicate to server (security model)
- Communication via RemoteEvents (async one-way) and RemoteFunctions (sync two-way)
- Scripts in ServerScriptService run ONLY on server
- LocalScripts in StarterPlayerScripts/StarterGui run ONLY on client
- ModuleScripts in ReplicatedStorage run in the context of whoever requires them

**H3: ModuleScript Architecture**

Code example -- ModuleScript pattern:
```luau
-- ReplicatedStorage/shared/modules/CombatModule.luau
local CombatModule = {}

export type AttackData = {
    damage: number,
    attackType: string,
    sourcePlayer: Player?,
}

function CombatModule.calculateDamage(baseDamage: number, multiplier: number): number
    return math.floor(baseDamage * multiplier)
end

function CombatModule.isValidAttack(attacker: Model, target: Model, maxRange: number): boolean
    local distance = (attacker.PrimaryPart.Position - target.PrimaryPart.Position).Magnitude
    return distance <= maxRange
end

return CombatModule
```

**H3: Rojo Project Structure**
- `default.project.json` maps filesystem to DataModel
- Directories become Folder instances
- `init.server.luau` -> Script, `init.client.luau` -> LocalScript, `init.luau` -> ModuleScript
- Enables Git version control, external editors, CI/CD

Code example -- Rojo project structure:
```
src/
├── server/                       -> ServerScriptService
│   ├── services/                 -> Knit services or game services
│   │   ├── DataService.luau
│   │   ├── CombatService.luau
│   │   └── MatchmakingService.luau
│   └── init.server.luau          -> Server bootstrap script
├── client/                       -> StarterPlayerScripts
│   ├── controllers/              -> Knit controllers or client systems
│   │   ├── InputController.luau
│   │   ├── UIController.luau
│   │   └── CameraController.luau
│   └── init.client.luau          -> Client bootstrap script
├── shared/                       -> ReplicatedStorage
│   ├── modules/                  -> Shared ModuleScripts
│   │   ├── Types.luau
│   │   ├── Constants.luau
│   │   └── Utils.luau
│   ├── config/                   -> Game configuration data
│   │   ├── WeaponConfig.luau
│   │   └── LevelConfig.luau
│   └── packages/                 -> Wally-installed packages
└── assets/                       -> ReplicatedStorage/Assets
```

**Sources:**
- [Roblox Data Model](https://create.roblox.com/docs/projects/data-model)
- [Instance Documentation](https://create.roblox.com/docs/reference/engine/classes/Instance)
- [Rojo Sync Details](https://rojo.space/docs/v7/sync-details/)
- [Roblox Client-Server Model](https://create.roblox.com/docs/scripting/events/remote)

**Gaps:** None. The prior research docs + web research provide complete coverage.

**Readiness: READY**

---

### Section 3: Luau Scripting Guide (Engine-Specific Language Section)

**Template requirement:** Comparison table (if multiple language options) or language guide. Roblox has only Luau, so this becomes a Luau features/typing guide similar to Godot's GDScript Static Typing section.

**Roblox content:**

Luau is the sole scripting language. Key features to cover:
- Gradual type system with optional annotations (`:` separator)
- `--!strict` mode for compile-time type checking
- `export type` for cross-module type sharing
- Type functions and read-only table properties (new solver, beta as of late 2024)
- String interpolation: `` `Hello {playerName}` ``
- Generalized iteration (`for k, v in table do`)
- `task` library (`task.spawn`, `task.defer`, `task.delay`, `task.wait`, `task.cancel`)
- Native code generation for performance-critical paths
- JIT optimizations benefit from type annotations

Code example -- Luau type annotations:
```luau
--!strict

export type WeaponConfig = {
    name: string,
    damage: number,
    fireRate: number,
    range: number,
    ammoCapacity: number,
}

export type PlayerData = {
    level: number,
    experience: number,
    coins: number,
    inventory: { string },
    settings: {
        musicVolume: number,
        sfxVolume: number,
    },
}

local function calculateDPS(weapon: WeaponConfig): number
    return weapon.damage * weapon.fireRate
end
```

**Sources:**
- [Luau Type System](https://luau.org/types/)
- [Luau Syntax](https://luau.org/syntax/)
- [Type Annotations Guide - DevForum](https://devforum.roblox.com/t/type-annotations-a-guide-to-writing-luau-code-that-is-actually-good/2843221)
- [Luau 2025 Runtime Recap](https://luau.org/news/2025-12-19-luau-recap-runtime-2025/)

**Gaps:** None.

**Readiness: READY**

---

### Section 4: Coding Best Practices

**Template requirement:** Multiple H3 subsections with code blocks showing PREFERRED/AVOID patterns. Includes: communication patterns, serialization, async, dependency management, project structure, naming conventions. 100-150 lines with 4-8 code blocks.

**Roblox content:**

**H3: Service Access and References**
```luau
-- PREFERRED: Cache service references at top of script
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

-- PREFERRED: Use WaitForChild for client-side references (content may not be replicated yet)
local modules = ReplicatedStorage:WaitForChild("modules")
local CombatModule = require(modules:WaitForChild("CombatModule"))

-- AVOID: game.Workspace (use game:GetService("Workspace") or workspace global)
-- AVOID: Assuming children exist without WaitForChild on client
```

**H3: Event-Driven vs Polling**
```luau
-- PREFERRED: Event-driven with signals
local part = workspace.MyPart
part:GetPropertyChangedSignal("Position"):Connect(function()
    print("Part moved to", part.Position)
end)

-- AVOID: Polling loops
while true do
    task.wait(0.1)
    if part.Position.Y < 0 then  -- Wasteful CPU cycles
        handleFall()
    end
end
```

**H3: RemoteEvent Patterns**
```luau
-- Server: Always validate client input
local remoteEvent = ReplicatedStorage.Remotes.PurchaseItem

remoteEvent.OnServerEvent:Connect(function(player: Player, itemId: string)
    -- Type check
    if typeof(itemId) ~= "string" then return end
    -- Bounds check
    if not ItemConfig[itemId] then return end
    -- State validation
    local coins = DataService:GetCoins(player)
    local price = ItemConfig[itemId].price
    if coins < price then return end
    -- Execute
    DataService:DeductCoins(player, price)
    InventoryService:AddItem(player, itemId)
end)
```

**H3: task Library Usage**
```luau
-- task.spawn: Run function immediately in new thread
task.spawn(function()
    local data = DataStoreService:GetAsync(key)
    processData(data)
end)

-- task.defer: Run function at end of current resumption cycle
task.defer(function()
    -- Good for operations that shouldn't happen mid-frame
    updateUI()
end)

-- task.delay: Run function after delay (replaces deprecated delay())
task.delay(5, function()
    cleanupTemporaryEffects()
end)

-- task.wait: Yield for duration (replaces deprecated wait())
task.wait(1) -- yields for 1 second

-- AVOID: wait(), spawn(), delay() (deprecated legacy functions)
```

**H3: Naming Conventions**
- Files and folders (Rojo): `PascalCase` for modules (`CombatService.luau`), `camelCase` acceptable for config files
- Variables and functions: `camelCase`
- Types: `PascalCase` (`export type WeaponConfig`)
- Constants: `UPPER_SNAKE_CASE`
- Services: `PascalCase` (matches Roblox API)
- Private members: prefix with `_` (`self._internalState`)
- RemoteEvents/Functions: `PascalCase` matching their action (`PurchaseItem`, `RequestSpawn`)
- Modules return tables, not classes (Luau convention)

**H3: Project Structure Conventions (Non-Rojo)**
For Studio-only workflow:
```
ServerScriptService/
├── GameManager (Script)
├── Services/
│   ├── DataService (ModuleScript)
│   ├── CombatService (ModuleScript)
│   └── ShopService (ModuleScript)
ReplicatedStorage/
├── Modules/
│   ├── Types (ModuleScript)
│   ├── Constants (ModuleScript)
│   └── Utils (ModuleScript)
├── Remotes/ (Folder of RemoteEvents/Functions)
├── Assets/ (Models, particles, sounds)
StarterPlayerScripts/
├── ClientManager (LocalScript)
├── Controllers/
│   ├── InputController (ModuleScript)
│   └── UIController (ModuleScript)
StarterGui/
├── HUD (ScreenGui)
└── Menus (ScreenGui)
```

**Sources:**
- [Best Practices Handbook - DevForum](https://devforum.roblox.com/t/best-practices-handbook/2593598)
- [Single Script Architecture - DevForum](https://devforum.roblox.com/t/single-script-architecture-and-modular-programming/2432662)
- [task Library Usage - DevForum](https://devforum.roblox.com/t/when-should-you-actually-use-taskspawn-delay-and-defer/3277550)
- [Require by String Announcement](https://devforum.roblox.com/t/introducing-require-by-string/3405078)

**Gaps:** None.

**Readiness: READY**

---

### Section 5: Performance Optimization

**Template requirement:** H3 subsections for General, Rendering, Physics, Memory/Loading, Language tips. Bullet lists + 1-2 code examples. 60-100 lines.

**Roblox content:**

**H3: General Guidelines**
- Replace `while wait() do` loops with event-driven signals (`.Changed`, `RunService.Heartbeat`)
- Use `task.spawn`/`task.defer` for non-blocking operations
- Object pool frequently spawned instances (projectiles, effects, coins) -- never spam `Instance.new()` + `Destroy()` in gameplay
- Use `Debris:AddItem(instance, lifetime)` for timed cleanup
- Limit per-frame computation; throttle heavy work with heartbeat budgets
- Cache `GetService()` calls at script top, never inside loops
- Use `CollectionService` tags for batch processing similar objects

**H3: Rendering Performance**
- **StreamingEnabled** -- loads only nearby content, reduces initial load by ~35%; essential for mobile
- **LOD (Level of Detail)** -- reduce mesh complexity at distance (up to 50% FPS improvement)
- Limit models to ~500 parts for optimal performance
- Use `MeshPart` over unions of `Part` instances where possible
- Texture atlasing and mesh simplification reduce GPU load ~30%
- Reduce particle emitter counts and rates on mobile
- Use **Future Lighting** for best quality, **ShadowMap** for mid-range, **Compatibility** for low-end

**H3: Physics Performance**
- Simplify collision geometry -- reduce collision mesh complexity by 30% for 25% frame stability improvement
- Anchor static parts (anchored parts skip physics simulation entirely)
- Use `CanCollide = false` + `CanQuery = true` for trigger volumes instead of physics bodies
- Manage network ownership carefully -- `SetNetworkOwner(nil)` for server-controlled physics
- Use constraints (AlignPosition, AlignOrientation) instead of CFrame manipulation for smooth movement

**H3: Memory and Networking**
- Minimize RemoteEvent frequency -- batch updates where possible
- Use `UnreliableRemoteEvent` for non-critical high-frequency data (positions, animations)
- Clean up connections with `:Disconnect()` to prevent memory leaks
- Use `:Destroy()` on instances when done (not just parenting to nil)
- Profile with **MicroProfiler** (Ctrl+F6) for per-frame CPU breakdown
- Use **Developer Console** (F9) for memory, network, and script stats
- Monitor with **Performance Dashboard** in Creator Hub analytics

**H3: Luau Performance Tips**
- Use `--!strict` type annotations -- typed code enables JIT optimizations
- Avoid creating tables in hot loops (pre-allocate and reuse)
- Use `table.create(n, value)` for pre-sized arrays
- `string.format` is faster than concatenation for building strings in loops
- Local variables are faster than global/upvalue access in tight loops
- `ipairs` is unnecessary in modern Luau -- use generalized `for i, v in array do`
- For heavy computation, consider `task.defer` to spread across frames

**Sources:**
- [Roblox Performance Optimization](https://create.roblox.com/docs/performance-optimization)
- [MicroProfiler Documentation](https://create.roblox.com/docs/performance-optimization/microprofiler)
- [Hitchhiker's Guide to Optimization - DevForum](https://devforum.roblox.com/t/the-hitchhikers-guide-to-optimization-optimize-your-game-using-just-one-post-instead-of-many/3358345)
- [Luau Performance](https://luau.org/performance/)

**Gaps:** None.

**Readiness: READY**

---

### Section 6: Popular Third-Party Packages

**Template requirement:** H3 categories with Markdown tables (Package | Purpose | Source). 4-6 rows per table, 3-5 categories. 50-80 lines.

**Roblox content:**

**Key finding from web research:** Several libraries have shifted status since the prior research doc. ProfileService has been superseded by **ProfileStore** (same author, improved session locking). Roact is deprecated in favor of **react-lua** (Roblox's internal React port). Knit is no longer actively maintained but remains functional and widely used. **Fusion** v0.3-beta released August 2024, still actively developed.

**H3: Essential Development**

| Package | Purpose | Source |
|---|---|---|
| **Knit** | Service-Controller game framework (server services + client controllers) | Wally (`sleitnick/knit`) |
| **Fusion** | Reactive UI library with state management | Wally (`dphfox/fusion`) |
| **react-lua** | React port for Luau (replaces deprecated Roact) | Wally (`jsdotlua/react`) |
| **TestEZ** | BDD-style testing framework (describe/it syntax) | Wally (`roblox/testez`) |
| **Promise** | Async promise implementation for Luau | Wally (`evaera/promise`) |

**H3: Data Persistence**

| Package | Purpose | Source |
|---|---|---|
| **ProfileStore** | Robust player data with session locking (successor to ProfileService) | Wally / GitHub (MadStudioRoblox) |
| **ProfileService** | Player data management (widely used, legacy) | Wally (`madstudioroblox/profileservice`) |
| **Lapis** | DataStore abstraction with schema validation and migrations | Wally (`nezuo/lapis`) |
| **DataStore2** | Ordered backups, berezaa-style data caching | GitHub |

**H3: Gameplay Systems**

| Package | Purpose | Source |
|---|---|---|
| **Jecs** | Fast ECS (800K entities at 60fps, entity relationships) | Wally (`ukendio/jecs`) |
| **Matter** | ECS with hot reloading and debugging | Wally (`matter-ecs/matter`) |
| **SimplePath** | Pathfinding wrapper for NPC navigation | Wally / GitHub |
| **TopbarPlus** | Custom topbar icon creation | Wally (`1foreverhd/topbarplus`) |
| **Iris** | Immediate-mode debug GUI (Dear ImGui style) | Wally |

**H3: Networking and Communication**

| Package | Purpose | Source |
|---|---|---|
| **BridgeNet2** | Optimized networking with identifier compression | Wally |
| **Red** | Networking library with middleware support | Wally |
| **Comm** | RemoteEvent/Function wrapper with type safety (by Knit author) | Wally (`sleitnick/comm`) |

**H3: Build and Quality Tools**

| Package | Purpose | Source |
|---|---|---|
| **Rojo** | Filesystem-to-Studio sync, enables Git workflows | CLI / aftman |
| **Wally** | Package manager for Roblox libraries | CLI / aftman |
| **Selene** | Luau linter for code quality | CLI / aftman |
| **StyLua** | Luau code formatter | CLI / aftman |
| **Lune** | Standalone Luau runtime for scripts and automation | CLI / aftman |
| **Darklua** | Luau code bundler and minifier | CLI / aftman |

**Sources:**
- [Wally Package Registry](https://wally.run/)
- [Knit GitHub](https://github.com/Sleitnick/Knit)
- [Jecs GitHub](https://github.com/Ukendio/jecs)
- [Fusion GitHub](https://github.com/dphfox/Fusion)
- [ProfileStore Docs](https://madstudioroblox.github.io/ProfileStore/)
- [Lapis GitHub](https://github.com/nezuo/lapis)
- [Matter ECS](https://eryn.io/matter/)
- [Is Knit Still Reliable? - DevForum](https://devforum.roblox.com/t/is-the-knit-framework-still-reliable/3386821)
- [Data Storing in 2025 - DevForum](https://devforum.roblox.com/t/data-storing-in-2025/3425924)

**Gaps:** None. Updated status of all libraries verified.

**Readiness: READY**

---

### Section 7: Common Architectural Patterns

**Template requirement:** H3 per pattern with full code implementation (20-40 lines each). 2-4 patterns. 60-120 lines.

**Roblox content:**

**H3: Service-Controller Pattern (Knit)**

```luau
-- server/services/CoinService.luau
local Knit = require(game:GetService("ReplicatedStorage").Packages.Knit)

local CoinService = Knit.CreateService({
    Name = "CoinService",
    Client = {
        CoinsChanged = Knit.CreateSignal(),
    },
})

local playerCoins: { [Player]: number } = {}

function CoinService:KnitStart()
    game:GetService("Players").PlayerAdded:Connect(function(player)
        playerCoins[player] = 0
    end)
end

function CoinService:AddCoins(player: Player, amount: number)
    playerCoins[player] = (playerCoins[player] or 0) + amount
    self.Client.CoinsChanged:Fire(player, playerCoins[player])
end

function CoinService.Client:GetCoins(player: Player): number
    return playerCoins[player] or 0
end

return CoinService
```

```luau
-- client/controllers/CoinController.luau
local Knit = require(game:GetService("ReplicatedStorage").Packages.Knit)

local CoinController = Knit.CreateController({ Name = "CoinController" })

function CoinController:KnitStart()
    local coinService = Knit.GetService("CoinService")
    coinService.CoinsChanged:Connect(function(newAmount: number)
        -- Update UI
        print("Coins:", newAmount)
    end)
end

return CoinController
```

**H3: Bootstrap Pattern (Single Entry Point)**

```luau
-- server/init.server.luau (Server bootstrap)
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local ServerScriptService = game:GetService("ServerScriptService")
local Knit = require(ReplicatedStorage.Packages.Knit)

-- Load all services (Knit discovers them)
for _, module in ServerScriptService.services:GetChildren() do
    if module:IsA("ModuleScript") then
        require(module)
    end
end

Knit.Start():andThen(function()
    print("[Server] All services started")
end):catch(warn)
```

**H3: Object Pool Pattern**

```luau
-- shared/modules/ObjectPool.luau
local ObjectPool = {}
ObjectPool.__index = ObjectPool

function ObjectPool.new(template: Instance, initialSize: number): ObjectPool
    local self = setmetatable({
        _template = template,
        _available = {} :: { Instance },
        _inUse = {} :: { [Instance]: boolean },
    }, ObjectPool)

    for _ = 1, initialSize do
        local clone = template:Clone()
        clone.Parent = workspace
        clone:SetAttribute("Pooled", true)
        table.insert(self._available, clone)
    end
    return self
end

function ObjectPool:Get(): Instance
    local obj = table.remove(self._available)
    if not obj then
        obj = self._template:Clone()
        obj.Parent = workspace
    end
    obj:SetAttribute("Pooled", false)
    self._inUse[obj] = true
    return obj
end

function ObjectPool:Return(obj: Instance)
    self._inUse[obj] = nil
    obj:SetAttribute("Pooled", true)
    -- Reset position, state, etc.
    if obj:IsA("BasePart") then
        obj.CFrame = CFrame.new(0, -1000, 0) -- Move out of play area
    end
    table.insert(self._available, obj)
end

return ObjectPool
```

**Sources:**
- [Knit Framework Docs](https://sleitnick.github.io/Knit/)
- [MVC in Roblox - DevForum](https://devforum.roblox.com/t/mvc-a-practical-approach-towards-developing-games-and-how-to-stop-confusing-yourself-with-ecs-vs-oop/3026159)
- [Object Pooling - DevForum](https://devforum.roblox.com/t/how-would-you-correctly-implement-object-pooling/547038)
- [PoolerPlus - DevForum](https://devforum.roblox.com/t/v14-poolerplus-advanced-object-pooling/3817379)

**Gaps:** None.

**Readiness: READY**

---

### Section 8: Security Architecture (Roblox-Specific Section)

**Template requirement:** This is an engine-specific section. Other engines don't have a dedicated security section because they don't have a mandatory server-authoritative model with public client access. This is critical for Roblox and should be its own H2.

**Roblox content:**

**H3: Core Principle -- Never Trust the Client**
- All game-critical logic (damage, currency, progression) MUST run server-side
- Client predicts for responsiveness but server validates and reconciles
- Exploiters can modify any client-side value, fire any RemoteEvent, read any client code

**H3: RemoteEvent Validation Pattern**
```luau
-- PREFERRED: Full validation on every RemoteEvent handler
remoteEvent.OnServerEvent:Connect(function(player: Player, ...)
    local args = {...}
    -- 1. Type check all arguments
    if typeof(args[1]) ~= "string" then return end
    -- 2. Rate limit (track per-player timestamps)
    if not RateLimiter:Allow(player, "Purchase", 10) then return end
    -- 3. Sanity check (is action physically possible?)
    if not canPlayerReach(player, targetPosition) then return end
    -- 4. State validation (does player have required resources?)
    if not hasEnoughCoins(player, cost) then return end
    -- 5. Execute on server
    processAction(player, args[1])
end)
```

**H3: Common Exploit Vectors**
- Input manipulation (altered health, currency, inventory)
- RemoteEvent spam (fire events thousands of times per second)
- Network ownership abuse (teleportation via physics ownership)
- Memory reading (all client-side code and data is visible to exploiters)

**H3: Mitigation Patterns**
- Rate limiting on all RemoteEvent handlers
- Server-side cooldowns for player actions
- Distance/physics sanity checks before processing actions
- Never store authoritative game state on client
- Never expose debug/admin RemoteEvents in production
- Use `SetNetworkOwner(nil)` for server-controlled physics objects

**Sources:**
- [Security Tactics - Roblox Creator Hub](https://create.roblox.com/docs/scripting/security/security-tactics)
- [Securing RemoteEvents - DevForum](https://devforum.roblox.com/t/how-to-secure-your-remoteevent-and-remotefunction/3345363)
- [Keeping Your Game Secure - DevForum](https://devforum.roblox.com/t/keeping-your-game-secure-part-1-protecting-remotes/2788472)
- [Anti-Exploit Patterns - DevForum](https://devforum.roblox.com/t/client-vs-server-authoritative-timing-anti-exploit/4077454)

**Gaps:** None. Comprehensive coverage from multiple DevForum sources.

**Readiness: READY**

---

### Section 9: Licensing and Cost

**Template requirement:** License model + cost bullets. 15-25 lines.

**Roblox content:**

Roblox uses a **free-to-develop, revenue-share model**:

- **Roblox Studio:** Free, no per-seat fees, no revenue thresholds for development
- **Game hosting:** Free -- Roblox provides all server infrastructure, CDN, and scaling at no cost
- **Publishing:** Free -- publish directly from Studio or via Open Cloud API
- **Marketplace fee:** 30% on all in-game transactions (GamePasses, Developer Products, Subscriptions); sellers receive 70%
- **Developer Exchange (DevEx):** Cash out earned Robux at $0.0038 USD per Robux (rate increased 8.6% in September 2025, first increase since 2017). Minimum 30,000 earned Robux ($114 USD) to be eligible
- **DevEx scale:** Roblox paid out $1B+ to creators between March 2024-2025 to 24,500+ developers
- **No royalty on revenue** (unlike Unreal's 5% model) -- the marketplace fee is the only revenue share
- **Asset costs:** Creator Store has free and paid models/plugins; most community packages (Knit, ProfileService) are free and open source

**Important cost considerations:**
- Roblox controls the economy -- Robux exchange rate and marketplace fee are set by Roblox and can change
- DevEx eligibility requires a Premium subscription ($4.99-$13.99/month as a player) and compliance with DevEx terms
- Verify current DevEx rates and terms before making revenue projections

**Sources:**
- [DevEx Rate Increase Announcement - DevForum](https://devforum.roblox.com/t/increasing-devex-creators-will-now-earn-85-more/3920159)
- [DevEx Help Page](https://en.help.roblox.com/hc/en-us/articles/13061189551124-Developer-Exchange-Help-and-Information-Page)
- [Roblox DevEx Calculator 2026](https://devex.gg/)

**Gaps:** None. DevEx rate freshly verified.

**Readiness: READY**

---

### Section 10: When NOT to Use Roblox

**Template requirement:** 5-7 bullets with bold scenario + explanation. 15-25 lines.

**Roblox content:**

- **Non-Roblox platform targets** -- Roblox games only run on the Roblox platform. You cannot export to Steam, App Store independently, consoles outside Roblox, or the open web. If your game needs to exist outside the Roblox ecosystem, use another engine
- **Custom rendering or visual fidelity beyond Roblox's limits** -- Roblox controls the rendering pipeline; you cannot write custom shaders, use ray tracing, or achieve photorealistic graphics. For cutting-edge visuals, use Unreal or Unity
- **Offline or local-only games** -- Roblox requires an internet connection and Roblox account. Single-player offline experiences are not supported
- **Precise physics simulation** -- Roblox physics is adequate for gameplay but not suitable for physics-based puzzle games requiring deterministic simulation or complex joint systems
- **Mature or graphic content** -- Roblox's content policies prohibit violence gore, sexual content, gambling, and other mature themes. If your game design requires these, Roblox is not viable
- **Full code ownership and control** -- Roblox hosts and runs your game on their servers. You cannot self-host, mod your own server binary, or access the engine source. If infrastructure control matters, use an open-source engine
- **Monetization outside Roblox's economy** -- You cannot integrate Stripe, PayPal, or custom payment processing. All monetization goes through Robux with Roblox's 30% marketplace fee and DevEx conversion rate

**Sources:**
- Prior research documents
- [Roblox Community Standards](https://en.help.roblox.com/hc/en-us/articles/203313410-Roblox-Community-Standards)

**Gaps:** None.

**Readiness: READY**

---

### Section 11: Platform-Specific Caveats

**Template requirement:** Bullet list per platform. 15-30 lines.

**Roblox content:**

- **Mobile (iOS/Android):** ~60%+ of Roblox players are on mobile. Touch input is primary; design UI for thumbs. Screen real estate is limited; use `AutomaticSize` and `UIScale`. Performance is constrained -- target 30fps on low-end devices. `StreamingEnabled` is essential for mobile load times. Test on actual devices, not just Studio emulation
- **Xbox:** Gamepad-only input required; no mouse/keyboard. Must pass Xbox UX certification. Use `ContextActionService` for gamepad bindings. Navigation must be fully accessible via D-pad/stick. Roblox handles Xbox submission but games must meet Roblox's console UX guidelines
- **PlayStation (PS4/PS5):** Similar to Xbox constraints. Gamepad-only. PS-specific button prompts (cross/circle swap in some regions). Roblox launched on PlayStation in October 2024
- **Meta Quest (VR):** VR input via controllers and head tracking. UI must be rendered in 3D space (ScreenGui still works but needs VR-aware sizing). Performance is critical -- Quest hardware is mobile-class. Roblox VR is available for users 13+ on Meta Quest
- **PC/Mac:** Most capable platform. Keyboard + mouse input. Highest performance budget. Still design for the lowest common denominator to avoid alienating mobile majority
- **Cross-platform UI:** Use `UserInputService:GetPlatform()` and `UserInputService.TouchEnabled` to detect device. Scale UI with `UIScale` and `AutomaticSize`. Show appropriate button prompts per platform. Test all input methods (touch, keyboard, gamepad, VR controllers)

**Sources:**
- [Cross-Platform Tutorial - DevForum](https://devforum.roblox.com/t/make-your-game-support-all-platforms-tutorial/4352175)
- [Xbox Development - DevForum](https://devforum.roblox.com/t/how-do-i-go-about-cross-platform-xbox-development/1831464)
- [Roblox on Meta Quest - DevForum](https://devforum.roblox.com/t/roblox-is-coming-to-meta-quest/2465291)
- [Cross-Platform Gaming Guide 2026](https://box-creative.co.uk/reads/2026/03/21/is-roblox-cross-platform-your-usa-gaming-guide-2026.html)

**Gaps:** None.

**Readiness: READY**

---

### Section 12: Architecture Decision Checklist

**Template requirement:** Markdown checkbox list, 10-12 items. Final section. 12-15 lines.

**Roblox content:**

```markdown
- [ ] Client-server split planned: which logic runs server-side vs client-side
- [ ] Data persistence approach selected: ProfileStore, Lapis, or raw DataStoreService
- [ ] Networking pattern: RemoteEvents with validation, or framework (Knit, BridgeNet2)
- [ ] UI approach: Native ScreenGui, Fusion (reactive), or react-lua (React-style)
- [ ] Framework decision: Knit (service-controller), ECS (Jecs/Matter), or custom
- [ ] Development workflow: Rojo + Git (recommended) or Studio-only
- [ ] Package management: Wally configured with required dependencies
- [ ] Lighting technology selected: Future, ShadowMap, or Compatibility
- [ ] Cross-platform input strategy: touch, keyboard, gamepad, VR
- [ ] Monetization plan: GamePasses, Developer Products, Subscriptions
- [ ] Security architecture: server validation, rate limiting, anti-exploit patterns
- [ ] Testing approach: TestEZ, MCP-based testing, or manual playtesting
- [ ] CI/CD pipeline: Selene + StyLua + rojo build + Open Cloud publishing
```

**Sources:** Synthesized from all sections above.

**Gaps:** None.

**Readiness: READY**

---

## Content Readiness Assessment

| Section | Readiness | Notes |
|---|---|---|
| 1. Overview | **READY** | Complete prose paragraph drafted |
| 2. Architecture Fundamentals | **READY** | All H3s identified, code examples drafted |
| 3. Luau Scripting Guide | **READY** | Type system, task library, string interpolation covered |
| 4. Coding Best Practices | **READY** | 6 H3 subsections with code blocks, naming conventions |
| 5. Performance Optimization | **READY** | All 5 standard H3 categories filled |
| 6. Popular Third-Party Packages | **READY** | 5 table categories, library statuses verified via web search |
| 7. Common Architectural Patterns | **READY** | 3 patterns with full code implementations |
| 8. Security Architecture | **READY** | Roblox-specific section with validation patterns |
| 9. Licensing and Cost | **READY** | DevEx rate verified current as of Sept 2025 increase |
| 10. When NOT to Use Roblox | **READY** | 7 bullets covering key scenarios |
| 11. Platform-Specific Caveats | **READY** | 6 platforms covered with specific guidance |
| 12. Architecture Decision Checklist | **READY** | 13 items drafted |

**Overall Assessment: ALL SECTIONS READY FOR WRITING**

Estimated final file: **550-650 lines** (consistent with Unity at 573 and Phaser at 653).

---

## Key Findings from Web Research

These findings are NEW information not present in the two prior research documents:

### 1. ProfileStore Supersedes ProfileService
ProfileStore (by the same author, MadStudioRoblox) is the recommended successor to ProfileService. It uses improved session locking and is backwards-compatible with ProfileService data. The prior research docs mention ProfileService as current; this should be updated.
- Source: [ProfileStore Docs](https://madstudioroblox.github.io/ProfileStore/)
- Source: [Data Storing in 2025 - DevForum](https://devforum.roblox.com/t/data-storing-in-2025/3425924)

### 2. Knit Is No Longer Actively Maintained
Knit has stopped receiving updates. It remains functional and widely used, but developers should be aware it is in maintenance-only status. The prior research presents Knit as actively maintained.
- Source: [Is Knit Still Reliable? - DevForum](https://devforum.roblox.com/t/is-the-knit-framework-still-reliable/3386821)

### 3. Roact Is Officially Deprecated
Roact has been superseded by react-lua (Roblox's internal React port to Luau). New projects should use react-lua, not Roact. The prior research mentions both as current options.
- Source: [Roact Deprecated - DevForum](https://devforum.roblox.com/t/roact-ui-framework-crash-course-deprecated/796618)

### 4. DevEx Rate Increased 8.6% in September 2025
The exchange rate went from $0.0035 to $0.0038 per Robux -- the first increase since 2017. This was not in the prior research (dated April 2026 but may not have caught this).
- Source: [DevEx Rate Increase - DevForum](https://devforum.roblox.com/t/increasing-devex-creators-will-now-earn-85-more/3920159)

### 5. Require-by-String Is Now Available
Luau now supports `require("./relative/path")` syntax (require by string), making module imports cleaner and more familiar to developers from other ecosystems. This is a significant developer experience improvement for Rojo-based workflows.
- Source: [Require by String - DevForum](https://devforum.roblox.com/t/introducing-require-by-string/3405078)

### 6. Fusion v0.3-beta Released (August 2024)
Fusion continues active development with v0.3-beta adding improvements. The GitHub repo has 721+ stars and 117+ forks. It remains the most actively developed reactive UI library for Roblox.
- Source: [Fusion GitHub Releases](https://github.com/dphfox/Fusion/releases)

### 7. Luau JIT Leverages Type Annotations for Performance
The Luau runtime uses type annotations to specialize JIT code paths. Annotating function arguments that are not modified inside the function allows the JIT to skip tag checks. This makes `--!strict` mode a genuine performance recommendation, not just a code quality one.
- Source: [Luau 2025 Runtime Recap](https://luau.org/news/2025-12-19-luau-recap-runtime-2025/)

### 8. Roblox PlayStation Launch (October 2024)
Roblox launched on PlayStation (PS4/PS5) in October 2024, expanding the cross-platform reach. The prior research mentions PlayStation support but not the specific launch date.
- Source: [Roblox Cross-Platform Guide 2026](https://box-creative.co.uk/reads/2026/03/21/is-roblox-cross-platform-your-usa-gaming-guide-2026.html)

### 9. PoolerPlus and EasyPool - Object Pooling Libraries
Community object pooling libraries exist (PoolerPlus v1.4, EasyPool) as alternatives to writing custom pooling. These could be mentioned in the packages section.
- Source: [PoolerPlus - DevForum](https://devforum.roblox.com/t/v14-poolerplus-advanced-object-pooling/3817379)
- Source: [EasyPool - DevForum](https://devforum.roblox.com/t/easypool-a-simple-to-use-system-meant-to-make-object-pooling-easy/3584280)

### 10. Lapis Has Not Been Battle-Tested in Large Production
While Lapis is mentioned in the prior research as a DataStore alternative, web research reveals it has not yet been used in a large production game. ProfileStore is the safer choice for production use.
- Source: [Best DataStore Module 2025 - DevForum](https://devforum.roblox.com/t/what-is-the-best-datastore-module-to-use-currently/3598021)

---

## Sources

- [Roblox Creator Hub - Data Model](https://create.roblox.com/docs/projects/data-model)
- [DataModel Documentation](https://create.roblox.com/docs/reference/engine/classes/DataModel)
- [Instance Documentation](https://create.roblox.com/docs/reference/engine/classes/Instance)
- [Roblox Performance Optimization](https://create.roblox.com/docs/performance-optimization)
- [MicroProfiler Documentation](https://create.roblox.com/docs/performance-optimization/microprofiler)
- [Security Tactics](https://create.roblox.com/docs/scripting/security/security-tactics)
- [RemoteEvent Documentation](https://create.roblox.com/docs/reference/engine/classes/RemoteEvent)
- [Luau Official](https://luau.org/)
- [Luau Type System](https://luau.org/types/)
- [Luau 2025 Runtime Recap](https://luau.org/news/2025-12-19-luau-recap-runtime-2025/)
- [Rojo Documentation](https://rojo.space/docs/v7/)
- [Wally Package Registry](https://wally.run/)
- [Knit GitHub](https://github.com/Sleitnick/Knit)
- [Fusion GitHub](https://github.com/dphfox/Fusion)
- [Jecs GitHub](https://github.com/Ukendio/jecs)
- [Matter ECS](https://eryn.io/matter/)
- [ProfileStore Docs](https://madstudioroblox.github.io/ProfileStore/)
- [Lapis GitHub](https://github.com/nezuo/lapis)
- [TestEZ GitHub](https://github.com/Roblox/testez)
- [DevEx Rate Increase - DevForum](https://devforum.roblox.com/t/increasing-devex-creators-will-now-earn-85-more/3920159)
- [DevEx Help Page](https://en.help.roblox.com/hc/en-us/articles/13061189551124-Developer-Exchange-Help-and-Information-Page)
- [Type Annotations Guide - DevForum](https://devforum.roblox.com/t/type-annotations-a-guide-to-writing-luau-code-that-is-actually-good/2843221)
- [Best Practices Handbook - DevForum](https://devforum.roblox.com/t/best-practices-handbook/2593598)
- [Securing RemoteEvents - DevForum](https://devforum.roblox.com/t/how-to-secure-your-remoteevent-and-remotefunction/3345363)
- [Cross-Platform Tutorial - DevForum](https://devforum.roblox.com/t/make-your-game-support-all-platforms-tutorial/4352175)
- [Require by String - DevForum](https://devforum.roblox.com/t/introducing-require-by-string/3405078)
- [Is Knit Still Reliable? - DevForum](https://devforum.roblox.com/t/is-the-knit-framework-still-reliable/3386821)
- [Data Storing in 2025 - DevForum](https://devforum.roblox.com/t/data-storing-in-2025/3425924)
- [Roblox Cross-Platform Guide 2026](https://box-creative.co.uk/reads/2026/03/21/is-roblox-cross-platform-your-usa-gaming-guide-2026.html)
