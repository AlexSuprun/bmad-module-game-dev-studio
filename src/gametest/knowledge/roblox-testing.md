# Roblox TestEZ Testing Guide

## Overview

TestEZ is the primary BDD-style testing framework for Roblox (describe/it/expect syntax, inspired by RSpec/Mocha/Chai). Roblox Studio provides built-in play mode testing that simulates the client-server split, and the built-in MCP Server (Studio 2026+) enables automated play mode testing via `run_script_in_play_mode`. For pure logic tests outside Studio, Lune (standalone Luau runtime) is the modern headless alternative.

## Test Framework Setup

### Installation via Rokit and Wally

`rokit.toml` (Rokit replaces Foreman — use `rokit.toml`, NOT `foreman.toml`):

```toml
[tools]
rojo = { github = "rojo-rbx/rojo", version = "7.4" }
wally = { github = "UpliftGames/wally", version = "0.3" }
selene = { github = "Kampfkarren/selene", version = "0.27" }
stylua = { github = "JohnnyMorganz/StyLua", version = "0.20" }
lune = { github = "lune-org/lune", version = "0.8" }
```

`wally.toml` dev dependency:

```toml
[dev-dependencies]
TestEZ = "roblox/testez@0.4.1"
MockDataStoreService = "buildthomas/mockdatastoreservice@2.9.5"
```

Run `wally install` to pull packages into the `Packages/` directory.

### Project Structure

```
src/
├── server/
│   ├── Services/
│   │   └── CombatService.luau
│   └── init.server.luau
├── client/
│   ├── Controllers/
│   │   └── InputController.luau
│   └── init.client.luau
├── shared/
│   ├── Modules/
│   │   └── DamageCalculator.luau
│   └── init.luau
└── tests/
    ├── unit/
    │   └── DamageCalculator.spec.luau
    ├── integration/
    │   └── CombatService.spec.luau
    ├── shared/
    │   ├── GameAssert.luau
    │   └── TestFactory.luau
    └── init.server.luau
```

### Test Runner Bootstrap

```luau
-- src/tests/init.server.luau
local TestEZ = require(game:GetService("ReplicatedStorage").Packages.TestEZ)
local testRoot = script.Parent

-- Pass specific subdirectories to avoid scanning shared/ utility modules
-- (utility modules must return a function to be treated as spec files)
TestEZ.TestBootstrap:run({ testRoot.unit, testRoot.integration }, TestEZ.Reporters.TextReporter)
```

## Unit Tests

### Basic Test Structure

```luau
--!strict
-- src/tests/unit/DamageCalculator.spec.luau
local DamageCalculator = require(game:GetService("ReplicatedStorage").Shared.Modules.DamageCalculator)

return function()
    describe("DamageCalculator", function()
        local calculator: DamageCalculator.Type

        beforeEach(function()
            calculator = DamageCalculator.new()
        end)

        it("should calculate base damage correctly", function()
            local result = calculator:calculate(100, 1.0)
            expect(result).to.equal(100)
        end)

        it("should double damage on critical hit", function()
            local result = calculator:calculate(100, 2.0)
            expect(result).to.equal(200)
        end)

        it("should return zero for zero multiplier", function()
            local result = calculator:calculate(100, 0)
            expect(result).to.equal(0)
        end)

        it("should throw on negative base damage", function()
            expect(function()
                calculator:calculate(-10, 1.0)
            end).to.throw()
        end)
    end)
end
```

TestEZ assertion API:

- `expect(value).to.equal(other)` — strict equality
- `expect(value).to.be.ok()` — truthy
- `expect(value).to.be.a("string")` — type check
- `expect(value).never.to.equal(other)` — negation
- `expect(function() ... end).to.throw()` — error throwing

### Parameterized Tests

```luau
--!strict
return function()
    describe("DamageCalculator parameterized", function()
        local DamageCalculator = require(game:GetService("ReplicatedStorage").Shared.Modules.DamageCalculator)
        local calculator = DamageCalculator.new()

        local scenarios = {
            { base = 100, mult = 1.0, expected = 100 },
            { base = 100, mult = 2.0, expected = 200 },
            { base = 50,  mult = 1.5, expected = 75  },
            { base = 0,   mult = 2.0, expected = 0   },
        }

        for _, scenario in scenarios do
            it(
                string.format("base %d * %.1f = %d", scenario.base, scenario.mult, scenario.expected),
                function()
                    local result = calculator:calculate(scenario.base, scenario.mult)
                    expect(result).to.equal(scenario.expected)
                end
            )
        end
    end)
end
```

### Testing ModuleScripts

```luau
--!strict
-- src/tests/unit/WeaponConfig.spec.luau
-- WeaponConfig is the Roblox equivalent of Godot Resources / Unity ScriptableObjects
local WeaponConfig = require(game:GetService("ReplicatedStorage").Shared.Modules.WeaponConfig)

return function()
    describe("WeaponConfig", function()
        it("should calculate DPS from damage and attack speed", function()
            local config = WeaponConfig.new({
                baseDamage = 10,
                attackSpeed = 2.0,
            })
            expect(config:getDPS()).to.equal(20)
        end)

        it("should return weapon name as a string", function()
            local config = WeaponConfig.new({ name = "Sword" })
            expect(config.name).to.be.a("string")
            expect(config.name).to.equal("Sword")
        end)

        it("should default attack speed to 1 when not provided", function()
            local config = WeaponConfig.new({ baseDamage = 15 })
            expect(config.attackSpeed).to.equal(1)
        end)
    end)
end
```

## Integration Tests

### Service Mocking

```luau
--!strict
-- src/tests/integration/CombatService.spec.luau
local CombatService = require(game:GetService("ServerScriptService").Services.CombatService)

return function()
    describe("CombatService", function()
        local mockPlayer: { Name: string, Health: number }
        local mockEnemy: { Name: string, Health: number, MaxHealth: number }

        beforeEach(function()
            mockPlayer = { Name = "TestPlayer", Health = 100 }
            mockEnemy = { Name = "TestEnemy", Health = 100, MaxHealth = 100 }
        end)

        it("should reduce enemy health on attack", function()
            CombatService.attack(mockPlayer, mockEnemy, 30)
            expect(mockEnemy.Health).to.equal(70)
        end)

        it("should not reduce enemy health below zero", function()
            CombatService.attack(mockPlayer, mockEnemy, 200)
            expect(mockEnemy.Health).to.equal(0)
        end)

        it("should return true when enemy is defeated", function()
            local defeated = CombatService.attack(mockPlayer, mockEnemy, 100)
            expect(defeated).to.equal(true)
        end)
    end)
end
```

### DataStore Testing

```luau
--!strict
-- src/tests/integration/PlayerData.spec.luau
local MockDataStoreService = require(game:GetService("ReplicatedStorage").Packages.MockDataStoreService)
local PlayerDataService = require(game:GetService("ServerScriptService").Services.PlayerDataService)

return function()
    describe("PlayerDataService", function()
        beforeEach(function()
            -- Swap service before resetting so the correct instance is cleared
            PlayerDataService.setDataStoreService(MockDataStoreService)
            MockDataStoreService.ResetData()
        end)

        it("should save and load player inventory", function()
            local userId = 12345
            PlayerDataService.save(userId, { gold = 500, level = 3 })

            local loaded = PlayerDataService.load(userId)
            expect(loaded.gold).to.equal(500)
            expect(loaded.level).to.equal(3)
        end)

        it("should return default data for new player", function()
            local data = PlayerDataService.load(99999)
            expect(data.gold).to.equal(0)
            expect(data.level).to.equal(1)
        end)
    end)
end
```

### RemoteEvent Testing

RemoteEvent and RemoteFunction testing requires Studio Local Server mode — there is no headless option for replication testing. Workflow:

1. Open **Test > Start** with at least 1 simulated player
2. Studio opens a server window and one or more client windows
3. Place your test Script under `ServerScriptService`; it fires when Local Server starts
4. Use `RemoteEvent:FireClient(player, ...)` on the server and assert `OnClientEvent` receipt in client test scripts
5. Shut down the local server to clean up

### MCP Play Mode Testing

```luau
--!strict
-- Script executed via run_script_in_play_mode MCP tool
-- Validates that a player spawns correctly after play mode starts
local Players = game:GetService("Players")

local function waitForPlayer(timeout: number): Player?
    local deadline = os.clock() + timeout
    while os.clock() < deadline do
        local player = Players:GetPlayers()[1]
        if player then
            return player
        end
        task.wait(0.1)
    end
    return nil
end

local player = waitForPlayer(10)
assert(player ~= nil, "No player spawned within 10 seconds")
local char = player.Character
assert(char ~= nil, "Player character did not load")
assert(
    char:FindFirstChild("HumanoidRootPart") ~= nil,
    "HumanoidRootPart missing from character"
)

print("MCP E2E PASS: Player spawned successfully")
```

MCP tool sequence: call `start_stop_play` to enter play mode first, then call `run_script_in_play_mode` with the script above.

## Studio Testing Tools

### Play Mode Options

- **Play (F5):** Loads game as a player; single client + server run in one window; fastest for quick iteration
- **Play Here (F6):** Spawns character at the current camera location; useful for testing specific map areas
- **Run (F8):** Runs server scripts only, no character spawned; useful for server-only and DataStore logic testing

### Local Server Testing

- **Test > Start** with 1–8 simulated players
- Opens separate windows: 1 server window + N client windows
- Essential for testing RemoteEvent replication and client-server split logic
- Switch focus between any window to inspect state on either side
- Required for RemoteEvent/RemoteFunction integration tests

### Device Emulation

- **Player Emulator** panel for localization locale switching and content policy testing
- Device-specific resolution and safe area testing via the emulator dropdown
- **Party Simulator** for testing party-based game logic with multiple simulated party members

## Test Utilities

### Custom Assertions

```luau
--!strict
-- src/tests/shared/GameAssert.luau
local GameAssert = {}

function GameAssert.nearlyEqual(a: number, b: number, tolerance: number?): ()
    local tol = tolerance or 0.001
    if math.abs(a - b) > tol then
        error(string.format(
            "Expected %f to nearly equal %f (tolerance %f)",
            a, b, tol
        ), 2)
    end
end

function GameAssert.withinRange(value: number, min: number, max: number): ()
    if value < min or value > max then
        error(string.format(
            "Expected %f to be within [%f, %f]",
            value, min, max
        ), 2)
    end
end

function GameAssert.hasComponent(instance: Instance, className: string): ()
    if not instance:FindFirstChildOfClass(className) then
        error(string.format(
            "Expected instance '%s' to have component '%s'",
            instance.Name, className
        ), 2)
    end
end

return GameAssert
```

### Test Data Factories

```luau
--!strict
-- src/tests/shared/TestFactory.luau
local TestFactory = {}

export type MockPlayer = {
    Name: string,
    UserId: number,
    Character: Model?,
    Health: number,
    leaderstats: { Gold: IntValue?, Level: IntValue? },
}

export type MockWeapon = {
    Name: string,
    baseDamage: number,
    attackSpeed: number,
    weaponType: string,
}

function TestFactory.createMockPlayer(overrides: { [string]: any }?): MockPlayer
    local defaults: MockPlayer = {
        Name = "TestPlayer",
        UserId = 12345,
        Character = nil,
        Health = 100,
        leaderstats = {},
    }
    if overrides then
        for k, v in overrides do
            (defaults :: any)[k] = v
        end
    end
    return defaults
end

function TestFactory.createMockWeapon(overrides: { [string]: any }?): MockWeapon
    local defaults: MockWeapon = {
        Name = "TestSword",
        baseDamage = 25,
        attackSpeed = 1.5,
        weaponType = "melee",
    }
    if overrides then
        for k, v in overrides do
            (defaults :: any)[k] = v
        end
    end
    return defaults
end

return TestFactory
```

## CI Integration

### Toolchain Setup (Rokit)

```bash
# Install Rokit (https://github.com/rojo-rbx/rokit)
curl -sSf https://raw.githubusercontent.com/rojo-rbx/rokit/main/scripts/install.sh | sh

# Trust and install project tools from rokit.toml
rokit trust
rokit install

# Install Wally packages
wally install
```

### Linting and Formatting

```bash
selene src/
stylua --check src/
```

### GitHub Actions

Use `setup-rokit` action (NOT `setup-foreman`):

```yaml
name: CI
on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ok-nick/setup-rokit@v0.1
      - name: Install packages
        run: wally install
      - name: Check formatting
        run: stylua --check src/
      - name: Lint
        run: selene src/

  build:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v4
      - uses: ok-nick/setup-rokit@v0.1
      - name: Install packages
        run: wally install
      - name: Build place
        run: rojo build -o game.rbxl

  test:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v4
      - uses: ok-nick/setup-rokit@v0.1
      - name: Install packages
        run: wally install
      - name: Run headless unit tests
        run: lune run tests/runner.luau
```

For headless unit tests in CI, use `lune run tests/runner.luau` with the `testez-luau` fork — this avoids needing Studio for pure-logic tests.

## Performance Testing

### MicroProfiler

- Access: **Ctrl+F6** in a running game session
- Shows a timeline of every frame: rendering, physics, scripts
- Frame budget: **16.6ms for 60 FPS target**
- X-Ray mode for memory allocations; FlameGraphs and Diff FlameGraphs (2024+ update)
- Script computation target: **< 5ms per frame**

### Benchmark Utilities

```luau
--!strict
local function benchmark(name: string, iterations: number, fn: () -> ()): {
    name: string,
    avgMs: number,
    totalMs: number,
}
    assert(iterations > 0, "iterations must be greater than 0")
    local start = os.clock()
    for _ = 1, iterations do
        fn()
    end
    local elapsed = os.clock() - start
    local perCall = elapsed / iterations * 1000

    print(string.format(
        "[PERF] %s: %.3fms avg (%d iterations, %.3fs total)",
        name, perCall, iterations, elapsed
    ))

    return { name = name, avgMs = perCall, totalMs = elapsed * 1000 }
end
```

## Best Practices

### DO

- Test shared modules (`ReplicatedStorage`) in isolation — they're the easiest to unit test
- Use TestEZ `describe`/`it` blocks to organize tests by feature
- Mock services (`DataStoreService`, `HttpService`) instead of calling real APIs in tests
- Test server-side logic separately from client-side logic
- Use `.spec.luau` file naming convention for test discovery
- Run `selene` + `stylua --check` before every commit

### DON'T

- Don't test Roblox engine built-in behavior (physics, rendering)
- Don't rely on execution order between test files
- Don't use `wait()` in tests — use `task.wait()` with explicit durations
- Don't test with real DataStores in CI — use `MockDataStoreService`
- Don't leave test Instances in the DataModel — clean up in `afterEach`

## Troubleshooting

| Issue | Cause | Fix |
| ----- | ----- | --- |
| Tests not found | Wrong file suffix | Use `.spec.luau` or `.spec.lua` suffix |
| `require` fails in test | Module path incorrect | Use `game:GetService("ReplicatedStorage").Shared.Modules.X` or relative `script.Parent` paths |
| DataStore errors in Studio | API access disabled | Enable "Enable Studio Access to API Services" in Game Settings |
| Tests hang | Yielding function in test | Avoid `task.wait()` in unit tests; reserve for integration tests only |
| Inconsistent test results | Shared state between tests | Use `beforeEach` to reset state; never share mutable state between `it` blocks |
| MCP tests fail silently | Play mode not started | Call `start_stop_play` before `run_script_in_play_mode` |

## End-to-End Testing

For comprehensive E2E testing patterns, infrastructure scaffolding, and
scenario builders, see **knowledge/e2e-testing.md**.

### Quick E2E Checklist for Roblox

- [ ] Create test runner Script under `ServerScriptService`
- [ ] Implement `ScenarioBuilder` for your game's domain (Luau `ModuleScript`)
- [ ] Create `InputSimulator` wrapping `ContextActionService`/`UserInputService`
- [ ] Add async assertion utilities using `task.wait()` polling
- [ ] Organize E2E tests under `tests/e2e/`
- [ ] Use MCP `run_script_in_play_mode` for automated E2E execution
- [ ] Configure Studio local server for multiplayer E2E scenarios
