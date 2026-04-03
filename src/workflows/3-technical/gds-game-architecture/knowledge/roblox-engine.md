# Roblox Engine Architecture Knowledge

## Overview

Roblox is a managed cloud gaming platform where all experiences run on Roblox-hosted servers, built on an Instance-Service hierarchy and scripted in Luau — a fast, optionally-typed dialect of Lua 5.1 with gradual typing and a JIT compiler. The platform enforces a mandatory client-server architecture (Filtering Enabled) with built-in physics, rendering, networking, and data persistence, eliminating infrastructure concerns entirely. Roblox Studio serves as the primary IDE, while Rojo enables filesystem-based Git workflows. Experiences deploy automatically to PC, Mac, iOS, Android, Xbox, PlayStation, and Meta Quest with no additional build steps.

## Architecture Fundamentals

### Instance-Service Model

**Key concepts:**

- **Instance** — Base class for all DataModel objects. Has `Name`, `Parent`, `Children`. Supports `FindFirstChild`, `GetChildren`, `GetDescendants`
- **Service** — Top-level singleton containers accessed via `game:GetService("ServiceName")`
- **Script** — Server-side execution context (runs in ServerScriptService or Workspace)
- **LocalScript** — Client-side execution context (runs in StarterPlayerScripts, StarterGui, StarterCharacterScripts)
- **ModuleScript** — Shared code module returning a single value via `require()`. Runs in the context of the requiring script

### DataModel Hierarchy

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

### Client-Server Architecture (Filtering Enabled)

- All experiences use a mandatory server-authoritative model
- Server changes replicate to all clients automatically
- Client changes do NOT replicate to the server (security model)
- Communication via `RemoteEvent` (async one-way) and `RemoteFunction` (sync two-way)
- Scripts in ServerScriptService run ONLY on the server
- LocalScripts in StarterPlayerScripts/StarterGui run ONLY on the client
- ModuleScripts in ReplicatedStorage run in the context of whoever requires them

### ModuleScript Architecture

```luau
--!strict
-- shared/modules/CombatService.luau

export type WeaponConfig = {
	damage: number,
	cooldown: number,
	range: number,
}

export type AttackResult = {
	hit: boolean,
	damageDealt: number,
}

local CombatService = {}

function CombatService.calculateDamage(config: WeaponConfig, multiplier: number): number
	return config.damage * multiplier
end

function CombatService.canAttack(lastAttackTime: number, config: WeaponConfig): boolean
	return (os.clock() - lastAttackTime) >= config.cooldown
end

return CombatService
```

### Rojo Project Structure

```
src/
├── server/                       -- → ServerScriptService
│   ├── init.server.luau          -- Server entry point (Script)
│   └── services/                 -- Server service modules
├── client/                       -- → StarterPlayerScripts
│   ├── init.client.luau          -- Client entry point (LocalScript)
│   └── controllers/              -- Client controller modules
├── shared/                       -- → ReplicatedStorage
│   ├── modules/                  -- Shared business logic
│   └── types/                    -- Shared type definitions
└── assets/                       -- Non-script assets
    └── ui/                       -- UI templates
```

File naming: `init.server.luau` → Script, `init.client.luau` → LocalScript, `init.luau` / `ModuleName.luau` → ModuleScript

## Luau Scripting Guide

Luau is Roblox's typed dialect of Lua 5.1 with gradual typing, string interpolation, generalized iteration, and a native code generation backend. Mode annotation `--!strict` at the top of a file enables compile-time type checking and also allows the Luau JIT to skip runtime tag checks for a measurable performance benefit.

```luau
--!strict

-- export type makes types available to any script that requires this module
export type PlayerData = {
	userId: number,
	coins: number,
	level: number,
	joinTime: number,
}

export type WeaponConfig = {
	name: string,
	damage: number,
	fireRate: number,
}

local DataModule = {}

-- Typed function signature
function DataModule.createPlayerData(userId: number): PlayerData
	return {
		userId = userId,
		coins = 0,
		level = 1,
		joinTime = os.time(),
	}
end

-- String interpolation (backtick syntax)
function DataModule.formatDisplay(data: PlayerData): string
	return `Player {data.userId} — Level {data.level} — {data.coins} coins`
end

-- Generalized iteration (no ipairs/pairs needed in modern Luau)
function DataModule.sumCoins(allData: { PlayerData }): number
	local total = 0
	for _, data in allData do
		total += data.coins
	end
	return total
end

return DataModule
```

**task library** — replaces all deprecated scheduling globals:

- `task.spawn(fn, ...)` — immediate new coroutine
- `task.defer(fn, ...)` — deferred to end of current resumption cycle
- `task.delay(seconds, fn, ...)` — scheduled future execution
- `task.wait(seconds)` — yield for duration (returns actual elapsed time)
- `task.cancel(thread)` — cancel a scheduled task

**Native code generation** — annotate performance-critical ModuleScripts with `--!native` to compile to machine code.

## Coding Best Practices

### Service Access and References

```luau
--!strict
-- PREFERRED: cache services at module top level
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- PREFERRED: WaitForChild on client for replicated assets
local remotes = ReplicatedStorage:WaitForChild("Remotes")

-- AVOID: service access inside loops or functions
-- AVOID: game.Workspace shorthand (use workspace global or GetService)
-- AVOID: assuming children exist on the client without WaitForChild
```

### Event-Driven vs Polling

```luau
--!strict
local Players = game:GetService("Players")

-- PREFERRED: connect to events
Players.PlayerAdded:Connect(function(player: Player)
	-- handle player join
end)

local part = workspace:FindFirstChild("Gate") :: BasePart
part:GetPropertyChangedSignal("CFrame"):Connect(function()
	-- react to change
end)

-- AVOID: polling loops
-- while true do
--     task.wait(0.1)
--     if someCondition then ... end
-- end
```

### RemoteEvent Patterns

```luau
--!strict
-- Server-side handler — always validate before executing
local Players = game:GetService("Players")

local function onPurchaseItem(player: Player, itemId: unknown, quantity: unknown)
	-- 1. Type check all arguments
	if typeof(itemId) ~= "string" or typeof(quantity) ~= "number" then return end

	-- 2. Rate limit (track per-player timestamps externally)
	if not rateLimiter:check(player) then return end

	-- 3. Sanity check (is the action physically plausible?)
	if quantity <= 0 or quantity > 99 then return end

	-- 4. State validation (does the player have resources?)
	local data = PlayerDataStore:get(player)
	if not data or data.coins < getItemCost(itemId) * quantity then return end

	-- 5. Execute on server
	executeItemPurchase(player, itemId, quantity)
end

purchaseRemote.OnServerEvent:Connect(onPurchaseItem)
```

### task Library Usage

```luau
--!strict
-- PREFERRED: task.* library
task.spawn(function()
	-- runs immediately in a new coroutine
end)

task.defer(function()
	-- runs after current frame resumes
end)

local thread = task.delay(2, function()
	-- runs 2 seconds from now
end)

task.wait(0.5)     -- yields current thread for 0.5s
task.cancel(thread) -- cancel a pending task

-- AVOID: deprecated globals
-- wait(0.5)    → use task.wait(0.5)
-- spawn(fn)    → use task.spawn(fn)
-- delay(t, fn) → use task.delay(t, fn)
```

### Naming Conventions

- Files/folders (Rojo): `PascalCase` for modules (`CombatService.luau`, `PlayerController.luau`)
- Variables and functions: `camelCase`
- Types: `PascalCase` (`export type WeaponConfig`)
- Constants: `UPPER_SNAKE_CASE`
- Services: `PascalCase` (matches Roblox API: `Players`, `RunService`)
- Private members: `_` prefix (`self._internalState`)
- RemoteEvents/Functions: `PascalCase` matching action (`PurchaseItem`, `RequestSpawn`)

### Project Structure Conventions

```
ServerScriptService/
├── init.server.luau              -- Bootstrap: load all services, start framework
└── Services/                     -- Server-side service ModuleScripts

ReplicatedStorage/
├── Remotes/                      -- RemoteEvent and RemoteFunction instances
├── Modules/                      -- Shared game logic ModuleScripts
└── Types/                        -- Shared type definition ModuleScripts

StarterPlayerScripts/
└── init.client.luau              -- Bootstrap: load all controllers

StarterGui/
└── ScreenGui/                    -- UI layout templates
```

## Performance Optimization

### General Guidelines

- Replace deprecated `while wait() do` loops with event-driven signals or `RunService.Heartbeat`
- Use `task.spawn`/`task.defer` for non-blocking operations
- Object-pool frequently spawned instances (projectiles, effects, pickups)
- Use `Debris:AddItem(instance, lifetime)` for automatic timed cleanup
- Cache `GetService()` calls at module top level, never inside loops or functions
- Use `CollectionService` tags for batch processing of similar objects

### Rendering Performance

- **StreamingEnabled** — loads only nearby content; reduces initial load ~35%; essential for mobile
- **LOD** — reduce mesh complexity at distance for up to 50% FPS improvement
- Limit models to ~500 parts; use `MeshPart` over part unions
- Texture atlasing and mesh simplification reduce GPU load ~30%
- Lighting technology: **Future** for best quality, **ShadowMap** for mid-range, **Compatibility** for low-end/mobile
- Reduce particle emitter counts and rates on mobile targets

### Physics Performance

- Anchor all static parts — anchored parts are skipped by the physics simulation entirely
- Simplify collision geometry to primitives where possible
- Use `CanCollide = false` + `CanQuery = true` for trigger volumes instead of full physics bodies
- Manage network ownership carefully — call `part:SetNetworkOwner(nil)` for server-controlled physics
- Prefer constraints (`AlignPosition`, `AlignOrientation`) over per-frame `CFrame` manipulation

### Memory and Networking

- Minimize `RemoteEvent` fire frequency — batch updates where possible
- Use `UnreliableRemoteEvent` for high-frequency non-critical data (positions, animations)
- Clean up event connections with `:Disconnect()` to prevent memory leaks
- Call `:Destroy()` on instances when done (not just re-parenting to `nil`)
- Profile with **MicroProfiler** (`Ctrl+F6`) for per-frame CPU breakdown
- Use **Developer Console** (`F9`) for memory, network, and script performance stats

### Luau Performance Tips

- Use `--!strict` — type annotations let the JIT skip runtime tag checks
- Avoid creating tables in hot loops — pre-allocate and reuse
- Use `table.create(n, value)` for pre-sized arrays
- `string.format` is faster than concatenation in loops
- Local variables are faster than global or upvalue access in tight inner loops
- Generalized `for i, v in array do` replaces `ipairs` in modern Luau

## Popular Third-Party Packages

### Essential Development

| Package | Purpose | Source |
|---|---|---|
| **Fusion** | Reactive UI library with state management | Wally (`dphfox/fusion`) |
| **react-lua** | React port for Luau (replaces deprecated Roact) | Wally (`jsdotlua/react`) |
| **Promise** | Async promise implementation for Luau | Wally (`evaera/promise`) |
| **TestEZ** | BDD-style testing framework (describe/it syntax) | Wally (`roblox/testez`) |
| **Knit** | Service-Controller framework — no longer actively maintained, widely used | Wally (`sleitnick/knit`) |

### Data Persistence

| Package | Purpose | Source |
|---|---|---|
| **ProfileStore** | Robust player data with session locking — recommended successor to ProfileService | Wally / GitHub (MadStudioRoblox) |
| **ProfileService** | Player data (legacy, widely used, unmaintained — not recommended for new projects) | Wally (`madstudioroblox/profileservice`) |
| **Lapis** | DataStore abstraction with schema validation (not yet battle-tested at scale) | Wally (`nezuo/lapis`) |
| **DataStoreService** | Built-in native DataStore (no external dependency) | Roblox built-in |

### Gameplay Systems

| Package | Purpose | Source |
|---|---|---|
| **Jecs** | Fast ECS (800K entities at 60fps, entity relationships) | Wally (`ukendio/jecs`) |
| **Matter** | ECS with hot reloading and debugging | Wally (`matter-ecs/matter`) |
| **SimplePath** | Pathfinding wrapper for NPC navigation | Wally / GitHub |
| **TopbarPlus** | Custom topbar icon creation | Wally (`1foreverhd/topbarplus`) |

### Networking

| Package | Purpose | Source |
|---|---|---|
| **ByteNet** | Performant networking with strict type safety (replaces archived BridgeNet2) | Wally (`ffrostflame/bytenet`) |
| **Red** | Networking library with middleware support | Wally |
| **Comm** | RemoteEvent/Function wrapper with type safety (by Knit author) | Wally (`sleitnick/comm`) |

### Build and Quality Tools

| Package | Purpose | Source |
|---|---|---|
| **Rojo** | Filesystem-to-Studio sync, enables Git workflows | CLI / Rokit |
| **Wally** | Package manager for Roblox libraries | CLI / Rokit |
| **Selene** | Luau linter for code quality | CLI / Rokit |
| **StyLua** | Luau code formatter | CLI / Rokit |
| **Lune** | Standalone Luau runtime for scripts and automation | CLI / Rokit |
| **Rokit** | Toolchain manager (manages Rojo, Wally, Selene, StyLua, Lune) | GitHub |

## Common Architectural Patterns

### Service-Controller Pattern (Knit — widely adopted but no longer actively maintained)

```luau
--!strict
-- server/Services/CoinService.luau
local Knit = require(game:GetService("ReplicatedStorage").Packages.Knit)

local CoinService = Knit.CreateService({
	Name = "CoinService",
	Client = {
		CoinsChanged = Knit.CreateSignal(),
	},
})

function CoinService:KnitStart()
	-- runs after all services have been initialized
end

function CoinService:AddCoins(player: Player, amount: number)
	-- server-side logic
	self.Client.CoinsChanged:Fire(player, amount)
end

function CoinService.Client:GetCoins(player: Player): number
	return CoinService:GetCoins(player)
end

return CoinService
```

```luau
--!strict
-- client/Controllers/CoinController.luau
local Knit = require(game:GetService("ReplicatedStorage").Packages.Knit)

local CoinController = Knit.CreateController({ Name = "CoinController" })

function CoinController:KnitStart()
	local CoinService = Knit.GetService("CoinService")
	CoinService.CoinsChanged:Connect(function(amount: number)
		-- update UI
	end)
end

return CoinController
```

### Bootstrap Pattern (Single Entry Point)

```luau
--!strict
-- server/init.server.luau
local Knit = require(game:GetService("ReplicatedStorage").Packages.Knit)
local ServerScriptService = game:GetService("ServerScriptService")

-- Load all services from the Services folder
for _, module in ServerScriptService.Services:GetChildren() do
	if module:IsA("ModuleScript") then
		require(module)
	end
end

-- Start Knit — initializes all services in dependency order
Knit.Start():andThen(function()
	print("Server started")
end):catch(warn)
```

### Object Pool Pattern

```luau
--!strict
-- shared/modules/ObjectPool.luau
local ObjectPool = {}
ObjectPool.__index = ObjectPool

export type Pool = typeof(setmetatable({} :: {
	_template: Instance,
	_available: { Instance },
}, ObjectPool))

function ObjectPool.new(template: Instance, initialSize: number): Pool
	local self = setmetatable({
		_template = template,
		_available = {} :: { Instance },
	}, ObjectPool)
	for _ = 1, initialSize do
		table.insert(self._available, template:Clone())
	end
	return self
end

function ObjectPool:Get(): Instance
	if #self._available > 0 then
		return table.remove(self._available) :: Instance
	end
	return self._template:Clone()
end

function ObjectPool:Return(obj: Instance)
	obj.Parent = nil
	table.insert(self._available, obj)
end

return ObjectPool
```

## Security Architecture

### Core Principle — Never Trust the Client

- All game-critical logic (damage, currency, inventory, progression) MUST run server-side
- The client predicts for responsiveness but the server validates and reconciles
- Exploiters can modify any client-side value, fire any RemoteEvent with arbitrary arguments, and read all client-visible code

### RemoteEvent Validation Pattern

```luau
--!strict
-- Five-step server handler — apply to every OnServerEvent handler
local function onPlayerAction(player: Player, rawTarget: unknown, rawAmount: unknown)
	-- Step 1: Type check all arguments
	if typeof(rawTarget) ~= "string" or typeof(rawAmount) ~= "number" then
		return
	end
	local target: string = rawTarget
	local amount: number = rawAmount

	-- Step 2: Rate limit
	if not RateLimiter:check(player, "playerAction") then return end

	-- Step 3: Sanity check (is this physically possible?)
	if amount <= 0 or amount > MAX_AMOUNT then return end

	-- Step 4: State validation (does the player have what is required?)
	local playerData = DataStore:get(player)
	if not playerData or playerData.resources < amount then return end

	-- Step 5: Execute on server
	executeAction(player, target, amount)
end

actionRemote.OnServerEvent:Connect(onPlayerAction)
```

### Common Exploit Vectors

- **Input manipulation** — altered health, currency, or inventory values sent via RemoteEvents
- **RemoteEvent spam** — thousands of fires per second to overwhelm server logic
- **Network ownership abuse** — teleportation by exploiting physics ownership
- **Memory reading** — all client-side code and ReplicatedStorage data is visible to exploiters

### Mitigation Patterns

- Rate limit every `OnServerEvent` handler — track per-player timestamps
- Server-side cooldowns for all player actions (attacks, purchases, teleports)
- Distance and physics sanity checks on any spatial action
- Never store authoritative game state (currency, health, inventory) on the client
- Never expose debug or admin `RemoteEvent` instances in production builds
- Call `part:SetNetworkOwner(nil)` for server-controlled physics objects

## Licensing and Cost

Roblox uses a **free-to-develop, revenue-share model**:

- Roblox Studio: free, no per-seat fees
- Game hosting: free — Roblox provides all server infrastructure
- Publishing: free via Studio or the Open Cloud API
- Marketplace fee: 30% on all in-game transactions (GamePasses, Developer Products, Subscriptions)
- Developer Exchange (DevEx): cash out at **$0.0038 USD per Robux** (increased 8.6% in September 2025, first increase since 2017)
- DevEx minimum: 30,000 earned Robux ($114 USD)
- No royalty on revenue (unlike Unreal's 5% model)

Important caveats:

- Roblox controls the economy — rates can change at any time
- DevEx requires a Premium subscription and compliance with DevEx terms
- Verify current rates before making revenue projections

## When NOT to Use Roblox

- **Non-Roblox platform targets** — experiences run exclusively on the Roblox platform; no Steam, independent App Store, or open-web export
- **Custom rendering or visual fidelity beyond Roblox's limits** — custom shaders, ray tracing, and photorealistic graphics are not possible
- **Offline or local-only games** — requires an internet connection and a Roblox account; no offline single-player mode
- **Precise physics simulation** — adequate for gameplay, but not suitable for physics-puzzle games requiring deterministic simulation
- **Mature or graphic content** — content policies prohibit graphic violence, sexual content, gambling, and mature themes
- **Full code ownership and control** — cannot self-host, modify the server binary, or access engine source
- **Monetization outside Roblox's economy** — cannot integrate Stripe, PayPal, or custom payment systems; all monetization flows through Robux

## Platform-Specific Caveats

- **Mobile (iOS/Android):** ~60%+ of players; design touch-primary UI; target 30fps on low-end devices; StreamingEnabled is essential; test on real hardware
- **Xbox:** Gamepad-only input; must pass Xbox UX certification; use `ContextActionService` for gamepad bindings; full D-pad navigation required throughout all UI
- **PlayStation (PS4/PS5):** Launched October 2024; gamepad-only; use PS-specific button prompts (cross/circle swap in some regions)
- **Meta Quest (VR):** Controller and head-tracking input; UI must handle 3D space; Quest is mobile-class hardware; available for users 13+
- **PC/Mac:** Most capable platform; keyboard and mouse; highest performance budget; still design to the mobile lowest common denominator
- **Cross-platform UI:** Use `UserInputService.TouchEnabled`, `UserInputService.GamepadEnabled`, and `GuiService:IsTenFootInterface()` to detect context; scale UI with `UIScale` and `AutomaticSize`; show appropriate button prompts per platform

## Architecture Decision Checklist

- [ ] Client-server split planned: which logic runs server-side vs client-side
- [ ] Data persistence approach selected: ProfileStore, Lapis, or raw DataStoreService
- [ ] Networking pattern chosen: RemoteEvents with manual validation, or framework (Knit, ByteNet)
- [ ] UI approach selected: Native ScreenGui, Fusion (reactive), or react-lua (React-style)
- [ ] Framework decision: Knit (service-controller), ECS (Jecs/Matter), or custom architecture
- [ ] Development workflow: Rojo + Git (recommended) or Studio-only
- [ ] Package management: Wally configured with required dependencies
- [ ] Lighting technology selected: Future, ShadowMap, or Compatibility
- [ ] Cross-platform input strategy planned: touch, keyboard, gamepad, VR
- [ ] Monetization plan defined: GamePasses, Developer Products, Subscriptions
- [ ] Security architecture reviewed: server validation, rate limiting, anti-exploit patterns
- [ ] Testing approach chosen: TestEZ, MCP-based testing, or manual playtesting
- [ ] CI/CD pipeline configured: Selene + StyLua + rojo build + Open Cloud publishing
