# Roblox Testing Knowledge Research

**Date:** 2026-04-02
**Author:** Alex Suprun (via research agent)
**Purpose:** Deep dive into testing knowledge patterns and Roblox-specific testing content to inform creation of `roblox-testing.md`

---

## Testing File Template Analysis

Analysis of `src/gametest/knowledge/unity-testing.md` (398 lines, the primary template):

### Section-by-Section Breakdown

| # | Section | Heading Level | Lines | Content Type | Code Examples |
|---|---------|--------------|-------|--------------|---------------|
| 1 | **Unity Test Framework Guide** | H1 | 1 | Title only | None |
| 2 | **Overview** | H2 | 3 (lines 3-5) | Prose paragraph | None |
| 3 | **Test Framework Setup** | H2 | 29 (lines 7-57) | 3 subsections (H3): Package Installation (JSON code), Project Structure (tree diagram), Assembly Definitions (JSON code) | 3 code blocks |
| 4 | **Edit Mode Tests** | H2 | 43 (lines 59-102) | Prose intro + 2 subsections (H3): Basic Test Structure, Testing ScriptableObjects | 2 C# code blocks (NUnit test class, ScriptableObject test) |
| 5 | **Play Mode Tests** | H2 | 61 (lines 122-223) | 4 subsections (H3): Basic Play Mode Test, Testing Coroutines, Scene Testing | 4 C# code blocks (IEnumerator patterns, WaitForSeconds, scene loading) |
| 6 | **Integration Test Patterns** | H2 | 44 (lines 225-268) | 2 subsections (H3): Prefab Testing, Input System Testing | 2 C# code blocks |
| 7 | **Test Utilities** | H2 | 52 (lines 270-324) | 2 subsections (H3): Custom Assertions, Test Fixtures | 2 C# code blocks (static assertion helpers, IDisposable fixture) |
| 8 | **CI Integration** | H2 | 23 (lines 326-353) | 2 subsections (H3): Command Line Execution, GitHub Actions | 2 code blocks (bash, YAML) |
| 9 | **Best Practices** | H2 | 16 (lines 355-373) | 2 subsections (H3): DO (6 bullets), DON'T (5 bullets) | None |
| 10 | **Troubleshooting** | H2 | 9 (lines 375-384) | Table with 4 rows (Issue / Cause / Fix) | None |
| 11 | **End-to-End Testing** | H2 | 14 (lines 386-398) | Cross-reference to e2e-testing.md + engine-specific E2E checklist | None (checklist only) |

### Key Patterns Observed

- **H1**: Single title line, engine-specific name
- **H2**: Major test categories (setup, test types, utilities, CI, practices, troubleshooting, E2E)
- **H3**: Subtopics within each category
- **Code blocks**: Always include language identifier (`csharp`, `json`, `bash`, `yaml`)
- **Code examples test**: Damage calculation, player movement, attack cooldown, prefab spawning, input actions -- all game-domain relevant
- **Cross-references**: Only one -- to `knowledge/e2e-testing.md` in the final section
- **No frontmatter**: Files start directly with H1

### Secondary Template Comparison

**godot-testing.md** (876 lines) follows the same pattern but is significantly longer due to:
- C# testing section (lines 378-548) -- Godot supports dual languages
- Full E2E infrastructure code (GameE2ETestFixture, ScenarioBuilder, InputSimulator, AsyncAssert) inlined rather than referenced
- More subsections under each H2

**unreal-testing.md** (~1515 lines) is the longest due to:
- Gauntlet framework section (separate H2)
- Blueprint testing section (separate H2)
- Performance testing section with frame time and memory tracking
- Full E2E infrastructure inlined (GameE2ETestBase, ScenarioBuilder, InputSimulator, AsyncTestHelpers in C++)

**Common structure across all three:**
1. Overview (H2)
2. Framework Setup (H2)
3. Unit/Basic Tests (H2)
4. Advanced/Integration Tests (H2)
5. Test Utilities (H2)
6. CI Integration (H2)
7. Best Practices (H2, DO/DON'T format)
8. Troubleshooting (H2, table format)
9. E2E Testing (H2, cross-reference + checklist)

---

## QA Index CSV Format

**File:** `src/gametest/qa-index.csv`

### Exact Format

```
id,name,description,tags,fragment_file
```

- **Delimiter:** Comma
- **Quoting:** Descriptions and tags are quoted with double quotes
- **5 columns:** `id`, `name`, `description`, `tags`, `fragment_file`
- **18 rows** (header + 17 data rows)
- **Fragment file paths:** Relative to `src/gametest/` (e.g., `knowledge/unity-testing.md`)

### Engine-Specific Entries

Engine-specific rows follow this pattern:

| id | name | tags | fragment_file |
|----|------|------|---------------|
| `unity-testing` | `Unity Test Framework` | `"unity,unit-tests,integration"` | `knowledge/unity-testing.md` |
| `unreal-testing` | `Unreal Automation` | `"unreal,automation,gauntlet"` | `knowledge/unreal-testing.md` |
| `godot-testing` | `Godot GUT Testing` | `"godot,gut,unit-tests"` | `knowledge/godot-testing.md` |

### Roblox Row to Add

```csv
roblox-testing,Roblox TestEZ Testing,"Roblox testing with TestEZ, Studio play mode, MCP automation, and CI pipelines","roblox,testez,unit-tests,luau",knowledge/roblox-testing.md
```

---

## QA Agent Integration

**File:** `src/agents/gds-agent-game-qa/SKILL.md`

### How the QA Agent Loads Testing Knowledge

1. **Index lookup:** Agent consults `{project-root}/_bmad/gds/gametest/qa-index.csv` to find relevant knowledge fragments
2. **Fragment loading:** Loads files from `{project-root}/_bmad/gds/gametest/knowledge/` matching the task context
3. **E2E priority:** For E2E testing requests, always loads `knowledge/e2e-testing.md` first
4. **Test type distinction:** Agent distinguishes between unit, integration, and E2E test needs
5. **Engine matching:** The agent identifies the engine from `project-context.md` and loads the corresponding `{engine}-testing.md` file

### Current Engine Support in Agent

The agent SKILL.md currently lists expertise in "Unity, Unreal, and Godot" (line 14). The Capabilities table includes engine-neutral codes (TF, TD, TA, ES, PP, PT, TR). No code changes needed in capabilities -- only the identity text needs "Roblox" added.

### Integration Requirements for Roblox

1. Add `roblox-testing` row to `qa-index.csv`
2. Create `knowledge/roblox-testing.md`
3. Update SKILL.md identity to include "Roblox" in engine list

---

## Roblox Testing Content per Section

### Section 1: Overview
**Template requirement:** 2-3 sentence overview of the engine's testing capabilities and frameworks.
**Roblox content:**
- TestEZ is the primary BDD-style testing framework for Roblox (describe/it/expect syntax, inspired by RSpec/Mocha/Chai)
- Roblox Studio provides built-in play mode testing with client-server split simulation
- MCP Server enables automated testing via `run_script_in_play_mode`
- Emerging alternatives: Specium (lightweight, no dependencies), Lune (standalone Luau runtime for CI)

**Code examples needed:** None
**Sources:**
- [TestEZ Documentation](https://roblox.github.io/testez/)
- [TestEZ GitHub](https://github.com/Roblox/testez)
- [Studio Testing Modes](https://create.roblox.com/docs/studio/testing-modes)
**Gaps:** None -- content is solid

### Section 2: Test Framework Setup
**Template requirement:** Package installation, project structure tree, configuration files. Unity uses asmdef, Godot uses gut_config.json.
**Roblox content:**

**Installation via Wally:**
```toml
# wally.toml
[dev-dependencies]
TestEZ = "roblox/testez@0.4.1"
```
Then `wally install` to pull into `Packages/` directory.

**Project Structure (Rojo-based):**
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
    └── init.server.luau  (test runner bootstrap)
```

**Test Runner Bootstrap:**
```luau
-- src/tests/init.server.luau
local TestEZ = require(game.ReplicatedStorage.Packages.TestEZ)
local testRoot = script.Parent

TestEZ.TestBootstrap:run({ testRoot }, TestEZ.Reporters.TextReporter)
```

**Code examples needed:** Wally.toml, project tree, test runner bootstrap script
**Sources:**
- [TestEZ Installation](https://roblox.github.io/testez/getting-started/installation/)
- [TestEZ wally.toml](https://github.com/Roblox/testez/blob/master/wally.toml)
- [Wally Package Manager](https://wally.run/)
- [CI/CD Setup with Rojo + Wally + TestEZ](https://devforum.roblox.com/t/cicd-setup-with-rojo-wally-testez/3610450)
**Gaps:** None

### Section 3: Unit Tests (Edit Mode equivalent)
**Template requirement:** Basic test structure, parameterized tests, testing engine-specific objects (ScriptableObjects in Unity, Resources in Godot). Game-domain examples (damage calculation, weapon stats).
**Roblox content:**

**Basic Test Structure (TestEZ describe/it/expect):**
```luau
-- tests/unit/DamageCalculator.spec.luau
return function()
    local DamageCalculator = require(game.ReplicatedStorage.Shared.Modules.DamageCalculator)

    describe("DamageCalculator", function()
        describe("calculate", function()
            it("should return base damage with 1x multiplier", function()
                local result = DamageCalculator.calculate(100, 1)
                expect(result).to.equal(100)
            end)

            it("should double damage on critical hit", function()
                local result = DamageCalculator.calculate(100, 2)
                expect(result).to.equal(200)
            end)

            it("should handle zero multiplier", function()
                local result = DamageCalculator.calculate(100, 0)
                expect(result).to.equal(0)
            end)
        end)
    end)
end
```

**Parameterized Tests:**
```luau
return function()
    local DamageCalculator = require(game.ReplicatedStorage.Shared.Modules.DamageCalculator)

    describe("DamageCalculator", function()
        local scenarios = {
            { base = 100, mult = 1, expected = 100 },
            { base = 100, mult = 2, expected = 200 },
            { base = 50, mult = 1.5, expected = 75 },
            { base = 0, mult = 2, expected = 0 },
        }

        for _, scenario in scenarios do
            it(string.format("should calculate %d * %s = %d",
                scenario.base, tostring(scenario.mult), scenario.expected), function()
                local result = DamageCalculator.calculate(scenario.base, scenario.mult)
                expect(result).to.equal(scenario.expected)
            end)
        end
    end)
end
```

**Testing ModuleScripts (Roblox equivalent of ScriptableObjects/Resources):**
```luau
-- tests/unit/WeaponConfig.spec.luau
return function()
    local WeaponConfig = require(game.ReplicatedStorage.Shared.Modules.WeaponConfig)

    describe("WeaponConfig", function()
        it("should calculate DPS correctly", function()
            local weapon = WeaponConfig.new({
                baseDamage = 10,
                attacksPerSecond = 2,
            })
            expect(weapon:getDPS()).to.equal(20)
        end)

        it("should clamp negative damage to zero", function()
            local weapon = WeaponConfig.new({ baseDamage = -5, attacksPerSecond = 1 })
            expect(weapon:getDPS()).to.equal(0)
        end)
    end)
end
```

**TestEZ Assertion API:**
- `expect(value).to.equal(other)` -- strict equality
- `expect(value).to.be.ok()` -- truthy
- `expect(value).to.be.a("string")` -- type check
- `expect(value).never.to.equal(other)` -- negation
- `expect(function() ... end).to.throw()` -- error throwing

**Code examples needed:** 3 code blocks (basic test, parameterized, ModuleScript testing)
**Sources:**
- [Writing Tests - TestEZ](https://roblox.github.io/testez/getting-started/writing-tests/)
- [Using .spec files with TestEZ](https://devforum.roblox.com/t/using-spec-files-with-testez-debug-and-test-your-code-like-roblox-does/1984749)
**Gaps:** None

### Section 4: Play Mode / Integration Tests
**Template requirement:** Tests requiring runtime/engine simulation. Unity uses `[UnityTest]` + coroutines. Tests involving physics, scene loading, component interaction.
**Roblox content:**

**Service Interaction Testing:**
```luau
-- tests/integration/CombatService.spec.luau
return function()
    local CombatService = require(game.ServerScriptService.Server.Services.CombatService)

    describe("CombatService", function()
        local mockPlayer, mockEnemy

        beforeEach(function()
            mockPlayer = {
                Health = 100,
                Character = { Humanoid = { Health = 100 } },
            }
            mockEnemy = {
                Health = 50,
                TakeDamage = function(self, amount)
                    self.Health = self.Health - amount
                end,
            }
        end)

        it("should deal damage to enemy", function()
            CombatService.attack(mockPlayer, mockEnemy, 25)
            expect(mockEnemy.Health).to.equal(25)
        end)

        it("should not allow attacking dead enemies", function()
            mockEnemy.Health = 0
            CombatService.attack(mockPlayer, mockEnemy, 25)
            expect(mockEnemy.Health).to.equal(0)
        end)
    end)
end
```

**RemoteEvent Testing (via local server):**
RemoteEvent and RemoteFunction testing requires Studio's local server mode (Test > Start > select player count). This spawns separate server and client windows. Manual verification or MCP-based automation is needed.

**DataStore Mocking:**
```luau
-- tests/integration/DataPersistence.spec.luau
return function()
    local MockDataStoreService = require(game.ReplicatedStorage.Packages.MockDataStoreService)

    describe("Player Data Persistence", function()
        local dataStore

        beforeEach(function()
            MockDataStoreService.ResetData()
            dataStore = MockDataStoreService:GetDataStore("PlayerData")
        end)

        it("should save and load player data", function()
            local data = { level = 5, gold = 1000 }
            dataStore:SetAsync("player_123", data)

            local loaded = dataStore:GetAsync("player_123")
            expect(loaded.level).to.equal(5)
            expect(loaded.gold).to.equal(1000)
        end)

        it("should handle missing keys gracefully", function()
            local loaded = dataStore:GetAsync("nonexistent")
            expect(loaded).to.equal(nil)
        end)
    end)
end
```

**MCP-Based Play Mode Testing:**
```luau
-- Script executed via MCP run_script_in_play_mode
-- Validates that a player spawns correctly
local Players = game:GetService("Players")

task.wait(2) -- Wait for character to load

local player = Players:GetPlayers()[1]
assert(player, "No player found in game")
assert(player.Character, "Player has no character")
assert(player.Character:FindFirstChild("Humanoid"), "Character missing Humanoid")

local humanoid = player.Character.Humanoid
assert(humanoid.Health == humanoid.MaxHealth, "Player should spawn at full health")

print("PASS: Player spawn validation complete")
```

**Code examples needed:** 4 code blocks (service mock, DataStore mock, RemoteEvent explanation, MCP test script)
**Sources:**
- [MockDataStoreService](https://github.com/buildthomas/MockDataStoreService)
- [MockDataStoreService DevForum](https://devforum.roblox.com/t/mockdatastoreservice-seamless-local-development-testing-with-datastores/140654)
- [Roblox MCP Server](https://github.com/Roblox/studio-rust-mcp-server)
- [Studio Built-in MCP Server](https://devforum.roblox.com/t/assistant-updates-studio-built-in-mcp-server-and-playtest-automation/4474643)
**Gaps:** None

### Section 5: Studio Testing Tools (Roblox-specific section, no direct Unity equivalent)
**Template requirement:** This is unique to Roblox. Maps loosely to Unity's Play Mode but warrants its own section due to the client-server split.
**Roblox content:**

**Play Mode Options:**
- **Play (F5):** Loads the game as a player, single client+server in one window
- **Play Here (F6):** Spawns character at camera location
- **Run (F8):** Runs scripts without spawning a character (useful for server-only testing)

**Local Server Testing (multiplayer):**
- Test > Start with 1-8 simulated players
- Opens separate windows: 1 server + N client windows
- Essential for testing RemoteEvents, replication, client-server split logic
- Can switch between any client or server window

**Device Emulation:**
- Player Emulator for testing localization and content policies
- Device-specific resolution testing

**Party Simulator (Beta, 2025):**
- Studio Party Simulator for testing party-based logic

**Code examples needed:** None (prose + bullet lists)
**Sources:**
- [Studio Testing Modes](https://create.roblox.com/docs/studio/testing-modes)
- [How to Test Your Roblox Game](https://metablox.gg/wiki/robloxstudio/gametest/)
- [Studio Party Simulator Beta](https://devforum.roblox.com/t/beta-studio-party-simulator-play-test-your-party-based-logic/4037049)
**Gaps:** None

### Section 6: Test Utilities
**Template requirement:** Custom assertions, test fixtures/helpers. Unity has `GameAssert`, `TestScene`.
**Roblox content:**

**Custom Assertion Helpers:**
```luau
-- tests/helpers/GameAssert.luau
local GameAssert = {}

function GameAssert.nearlyEqual(actual: number, expected: number, tolerance: number?)
    tolerance = tolerance or 0.001
    local diff = math.abs(actual - expected)
    assert(diff < tolerance,
        string.format("Expected ~%f, got %f (diff: %f, tolerance: %f)",
            expected, actual, diff, tolerance))
end

function GameAssert.withinRange(value: number, min: number, max: number)
    assert(value >= min and value <= max,
        string.format("Expected %f to be within [%f, %f]", value, min, max))
end

function GameAssert.hasComponent(instance: Instance, className: string)
    assert(instance:FindFirstChildOfClass(className),
        string.format("Expected %s to have child of class %s",
            instance:GetFullName(), className))
end

return GameAssert
```

**Test Data Factory:**
```luau
-- tests/helpers/TestFactory.luau
local TestFactory = {}

function TestFactory.createMockPlayer(overrides: { [string]: any }?)
    local defaults = {
        Name = "TestPlayer",
        UserId = 12345,
        Health = 100,
        Inventory = {},
    }
    if overrides then
        for key, value in overrides do
            defaults[key] = value
        end
    end
    return defaults
end

function TestFactory.createMockWeapon(overrides: { [string]: any }?)
    local defaults = {
        Name = "TestSword",
        Damage = 10,
        AttackSpeed = 1.5,
        Range = 5,
    }
    if overrides then
        for key, value in overrides do
            defaults[key] = value
        end
    end
    return defaults
end

return TestFactory
```

**Code examples needed:** 2 code blocks (assertion helpers, test factory)
**Sources:** Based on TestEZ patterns and common Luau conventions
**Gaps:** None

### Section 7: CI Integration
**Template requirement:** Command-line test execution, GitHub Actions workflow YAML. Unity uses game-ci action, Godot uses headless GUT.
**Roblox content:**

**Linting (Selene + StyLua):**
```bash
# Install via Foreman (toolchain manager)
foreman install  # reads foreman.toml

# Or direct install
cargo install selene
cargo install stylua

# Run linter
selene src/

# Run formatter check
stylua --check src/
```

**Foreman Configuration:**
```toml
# foreman.toml
[tools]
rojo = { github = "rojo-rbx/rojo", version = "7.4" }
selene = { github = "Kampfkarren/selene", version = "0.27" }
stylua = { github = "JohnnyMorganz/StyLua", version = "0.20" }
wally = { github = "UpliftGames/wally", version = "0.3" }
```

**Headless Testing (Lemur):**
TestEZ can run inside Lemur (a Roblox API emulation layer) paired with a Lua 5.1 interpreter for CI execution without Roblox Studio. This is the established approach for running tests in GitHub Actions.

**Headless Testing (Lune -- modern alternative):**
Lune is a standalone Luau runtime (~5MB) that can execute Luau code outside Roblox. For pure logic tests that don't depend on Roblox services, Lune offers faster CI execution. The `testez-luau` fork provides a Roblox-independent TestEZ variant.

**Open Cloud Luau Execution API:**
Roblox provides the ability to headlessly run Luau scripts in a published Roblox place via the Open Cloud Engine API, enabling server-side integration tests in CI.

**GitHub Actions Workflow:**
```yaml
name: CI
on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: Roblox/setup-foreman@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

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

      - uses: Roblox/setup-foreman@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Install packages
        run: wally install

      - name: Build place
        run: rojo build -o game.rbxl

  deploy:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - name: Publish to Roblox
        run: |
          # Use SaveToRoblox or Open Cloud API
          # rojo build -o game.rbxl
          # Upload via Open Cloud Place API
```

**Code examples needed:** 4 code blocks (foreman.toml, selene/stylua commands, GitHub Actions YAML, Rojo build)
**Sources:**
- [Roblox Place CI/CD Demo](https://github.com/Roblox/place-ci-cd-demo)
- [Selene, StyLua, and Roblox LSP](https://devforum.roblox.com/t/selene-stylua-and-roblox-lsp-what-they-do-why-you-should-use-them/1977666)
- [Roblox Foreman](https://github.com/Roblox/foreman)
- [setup-foreman Action](https://github.com/Roblox/setup-foreman)
- [Lune Runtime](https://github.com/lune-org/lune)
- [testez-luau fork](https://github.com/lrockreal/testez-luau)
- [Automate Place Publishing with Rojo](https://devforum.roblox.com/t/how-to-automate-place-publishing-with-partially-managed-rojo/2443196)
**Gaps:** Lemur + TestEZ CI example is dated -- Lune may be the better modern recommendation but lacks a one-line CI setup.

### Section 8: Performance Testing
**Template requirement:** Unreal has a dedicated Performance Testing section (frame time, memory tracking). Unity mentions performance briefly. Roblox warrants coverage due to MicroProfiler importance.
**Roblox content:**

**MicroProfiler:**
- Access via Ctrl+F6 in running game
- Shows timeline of every frame broken by task: rendering, physics, scripts
- Frame budget: 17ms for 60 FPS target
- Recent improvements (2024-2025): X-Ray mode for memory allocations, Summary FlameGraphs, Diff FlameGraphs for comparing dumps

**Performance Budgets:**
- Script computation: aim for < 5ms per frame
- Network: keep RemoteEvent payloads small, batch where possible
- Memory: monitor via MicroProfiler X-Ray mode
- Instances: avoid creating thousands of transient instances

**Programmatic Performance Testing:**
```luau
-- Performance benchmark utility
local function benchmark(name: string, iterations: number, fn: () -> ())
    local start = os.clock()
    for _ = 1, iterations do
        fn()
    end
    local elapsed = os.clock() - start
    local perCall = elapsed / iterations * 1000 -- ms

    print(string.format("[PERF] %s: %.3fms avg (%d iterations, %.3fs total)",
        name, perCall, iterations, elapsed))

    return { name = name, avgMs = perCall, totalMs = elapsed * 1000 }
end
```

**Code examples needed:** 1 code block (benchmark utility)
**Sources:**
- [MicroProfiler Documentation](https://create.roblox.com/docs/performance-optimization/microprofiler)
- [MicroProfiler Memory Profiling Update](https://devforum.roblox.com/t/introducing-microprofiler-memory-profiling-flame-graphs-diffs-and-much-more/3226910)
- [Roblox Performance Optimization Guide](https://github.com/Roblox/creator-docs/blob/main/content/en-us/performance-optimization/identify.md)
**Gaps:** None

### Section 9: Best Practices
**Template requirement:** DO/DON'T bullet lists. Unity has 6 DOs and 5 DON'Ts.
**Roblox content:**

**DO:**
- Test shared modules (ReplicatedStorage) in isolation -- they're the easiest to unit test
- Use TestEZ describe/it blocks to organize tests by feature
- Mock services (DataStoreService, HttpService) instead of calling real APIs in tests
- Test server-side logic separately from client-side logic
- Use `.spec.luau` file naming convention for test discovery
- Run Selene + StyLua before every commit

**DON'T:**
- Don't test Roblox engine built-in behavior (physics, rendering)
- Don't depend on specific execution order between test files
- Don't use `wait()` in tests (use `task.wait()` with explicit durations)
- Don't test with real DataStores in CI (use MockDataStoreService)
- Don't leave test Instances in the DataModel (clean up in `afterEach`)

**Code examples needed:** None
**Sources:** Synthesized from community best practices
**Gaps:** None

### Section 10: Troubleshooting
**Template requirement:** Table with Issue / Cause / Fix columns. Unity has 4 rows.
**Roblox content:**

| Issue | Cause | Fix |
|-------|-------|-----|
| Tests not found | Wrong file suffix | Use `.spec.luau` or `.spec.lua` suffix |
| `require` fails in test | Module path incorrect | Use relative `script.Parent` paths or absolute game tree paths |
| DataStore errors in Studio | Studio API access disabled | Enable "Enable Studio Access to API Services" in Game Settings |
| Tests hang | Yielding function in test | Avoid `task.wait()` in unit tests; use for integration only |
| Inconsistent test results | Service initialization order | Use `beforeEach` to reset state; don't share state between `it` blocks |
| MCP tests fail silently | Play mode not started | Ensure `start_stop_play` is called before `run_script_in_play_mode` |

**Code examples needed:** None
**Sources:** Synthesized from DevForum discussions
**Gaps:** None

### Section 11: End-to-End Testing
**Template requirement:** Cross-reference to e2e-testing.md + engine-specific quick checklist.
**Roblox content:**

Cross-reference: `knowledge/e2e-testing.md` (same as other engines)

**Quick E2E Checklist for Roblox:**
- [ ] Create test runner Script under ServerScriptService
- [ ] Implement `ScenarioBuilder` for your game's domain (Luau ModuleScript)
- [ ] Create `InputSimulator` wrapping ContextActionService/UserInputService
- [ ] Add async assertion utilities using `task.wait()` polling
- [ ] Organize E2E tests under `tests/e2e/`
- [ ] Use MCP `run_script_in_play_mode` for automated E2E execution
- [ ] Configure Studio local server for multiplayer E2E scenarios

**Code examples needed:** None (checklist only, matching template pattern)
**Sources:** Derived from template pattern + Roblox tooling
**Gaps:** E2E infrastructure code (GameE2ETestFixture, ScenarioBuilder, InputSimulator, AsyncAssert in Luau) should be inlined if following the Godot/Unreal pattern rather than the minimal Unity pattern. Recommend following the longer pattern for Roblox since MCP automation is unique.

---

## Content Readiness Assessment

| Section | Status | Notes |
|---------|--------|-------|
| Overview | **Ready** | All content sourced and verified |
| Test Framework Setup | **Ready** | Wally + TestEZ + Rojo structure well-documented |
| Unit Tests | **Ready** | TestEZ syntax fully documented with game-domain examples |
| Integration Tests | **Ready** | Service mocking, DataStore mocking, RemoteEvent patterns |
| Studio Testing Tools | **Ready** | Play mode, local server, device emulation documented |
| Test Utilities | **Ready** | Custom assertions and test factories in Luau |
| CI Integration | **Ready** | Foreman + Selene + StyLua + Rojo build + GitHub Actions |
| Performance Testing | **Ready** | MicroProfiler, frame budgets, benchmark utility |
| Best Practices | **Ready** | DO/DON'T lists synthesized from community patterns |
| Troubleshooting | **Ready** | Common issues table populated |
| E2E Testing | **Partial** | Checklist ready; full E2E infrastructure code (ScenarioBuilder, InputSimulator in Luau) would benefit from more detail if following the Godot/Unreal-length approach |

**Overall Assessment: Ready for file creation.** All 11 sections have sufficient content. The file will be approximately 300-400 lines following the Unity template length, or 500-600 lines if inlining full E2E infrastructure code like Godot/Unreal.

---

## Key Findings from Web Research

### TestEZ Framework Status
- **Version:** 0.4.1 (latest on Wally)
- **Status:** Still the standard. Roblox uses it internally for Roact, Rodux, and core scripts
- **Maintenance:** GitHub repo active, Wally manifest added. No signs of deprecation
- **Syntax:** BDD-style -- `describe`/`it`/`expect`, inspired by RSpec/Mocha/Chai
- **Installation:** Via Wally (`roblox/testez@0.4.1`) or Git submodule
- **CI Support:** Runs in Lemur (headless Lua 5.1 emulation) or Lune (standalone Luau runtime)

### Emerging Alternatives
- **Specium** -- Lightweight, zero-dependency test framework. Newer entrant (2025-2026 DevForum post). Good for developers who find TestEZ too heavy
- **EzSpec** -- Minimalistic alternative that removes TestEZ's function environment manipulation
- **Nexus Unit Testing** -- Archived March 2025. Not recommended
- **testez-luau** -- Roblox-independent fork of TestEZ for use with Lune/standalone Luau

### MCP-Based Testing (New Paradigm)
- Roblox is bringing MCP Server natively into Studio (announced 2026)
- `run_script_in_play_mode` enables automated play-mode testing from Claude Code
- This is a unique testing capability that no other engine (Unity/Unreal/Godot) has in BMGD
- The built-in MCP server is now the recommended integration path

### CI/CD Pipeline Maturity
- **Established:** Rojo build + Selene lint + StyLua format + GitHub Actions
- **Modern:** Lune as standalone Luau runtime for CI test execution
- **Roblox official:** `place-ci-cd-demo` repo with GitHub Actions reference implementation
- **Foreman:** Toolchain manager (`setup-foreman` GitHub Action) for consistent tool versions
- **Open Cloud Engine API:** Headless Luau execution on published places (beta)

### Performance Testing Tools
- **MicroProfiler** received major update (2024-2025): X-Ray memory mode, FlameGraphs, Diff FlameGraphs
- Frame budget: 17ms for 60 FPS
- No equivalent of Unity's Test Runner performance tests -- must use custom benchmarking

### Multiplayer Testing
- Studio local server mode: 1-8 simulated players
- Server Authority testing (announced RDC 2025, in early testing)
- Party Simulator (beta 2025) for party-based logic
- Team Test mode exists but has debugging limitations

### Deprecated/Avoid
- **Nexus Unit Testing** -- Archived March 2025
- **`wait()` function** -- Deprecated; use `task.wait()`
- **Lemur** -- Still works but aging; Lune is the modern alternative for headless execution

---

## Recommendation for roblox-testing.md Structure

Based on template analysis and content readiness:

```
# Roblox TestEZ Testing Guide                    (~1 line)

## Overview                                       (~5 lines)
## Test Framework Setup                           (~50 lines)
  ### Installation via Wally
  ### Project Structure
  ### Test Runner Bootstrap
## Unit Tests                                     (~60 lines)
  ### Basic Test Structure
  ### Parameterized Tests
  ### Testing ModuleScripts
## Integration Tests                              (~70 lines)
  ### Service Mocking
  ### DataStore Testing
  ### RemoteEvent Testing
  ### MCP Play Mode Testing
## Studio Testing Tools                           (~30 lines)
  ### Play Mode Options
  ### Local Server Testing
  ### Device Emulation
## Test Utilities                                 (~40 lines)
  ### Custom Assertions
  ### Test Data Factories
## CI Integration                                 (~50 lines)
  ### Toolchain Setup (Foreman)
  ### Linting and Formatting
  ### GitHub Actions
## Performance Testing                            (~30 lines)
  ### MicroProfiler
  ### Benchmark Utilities
## Best Practices                                 (~20 lines)
  ### DO
  ### DON'T
## Troubleshooting                                (~15 lines)
## End-to-End Testing                             (~20 lines)
  ### Quick E2E Checklist for Roblox
```

**Estimated total: ~390 lines** (in line with unity-testing.md at 398 lines)

---

## Sources

- [TestEZ Documentation](https://roblox.github.io/testez/)
- [TestEZ GitHub](https://github.com/Roblox/testez)
- [Writing Tests - TestEZ](https://roblox.github.io/testez/getting-started/writing-tests/)
- [Running Tests - TestEZ](https://roblox.github.io/testez/getting-started/running-tests/)
- [TestEZ wally.toml](https://github.com/Roblox/testez/blob/master/wally.toml)
- [testez-luau (Roblox-independent fork)](https://github.com/lrockreal/testez-luau)
- [Studio Testing Modes](https://create.roblox.com/docs/studio/testing-modes)
- [Studio Built-in MCP Server Announcement](https://devforum.roblox.com/t/assistant-updates-studio-built-in-mcp-server-and-playtest-automation/4474643)
- [Official Roblox MCP Server](https://github.com/Roblox/studio-rust-mcp-server)
- [MockDataStoreService](https://github.com/buildthomas/MockDataStoreService)
- [MockDataStoreService DevForum](https://devforum.roblox.com/t/mockdatastoreservice-seamless-local-development-testing-with-datastores/140654)
- [Roblox Place CI/CD Demo](https://github.com/Roblox/place-ci-cd-demo)
- [Selene, StyLua, and Roblox LSP](https://devforum.roblox.com/t/selene-stylua-and-roblox-lsp-what-they-do-why-you-should-use-them/1977666)
- [Roblox Foreman](https://github.com/Roblox/foreman)
- [Wally Package Manager](https://wally.run/)
- [Lune Standalone Luau Runtime](https://github.com/lune-org/lune)
- [Lune Documentation](https://lune-org.github.io/docs/)
- [MicroProfiler Documentation](https://create.roblox.com/docs/performance-optimization/microprofiler)
- [MicroProfiler Memory Profiling Update](https://devforum.roblox.com/t/introducing-microprofiler-memory-profiling-flame-graphs-diffs-and-much-more/3226910)
- [Roblox Performance Optimization](https://github.com/Roblox/creator-docs/blob/main/content/en-us/performance-optimization/identify.md)
- [Specium Testing Framework](https://devforum.roblox.com/t/specium-a-testing-framework-for-roblox/4529036)
- [Roblox Dev Tools 2025 Review](https://robloxdb.com/blog/post/roblox-development-tools-2025-review)
- [Kampfkarren's Luau Guidelines](https://github.com/Kampfkarren/kampfkarren-luau-guidelines)
- [CI/CD Setup with Rojo + Wally + TestEZ](https://devforum.roblox.com/t/cicd-setup-with-rojo-wally-testez/3610450)
- [Studio Party Simulator Beta](https://devforum.roblox.com/t/beta-studio-party-simulator-play-test-your-party-based-logic/4037049)
- [Server Authority Testing](https://devforum.roblox.com/t/guide-on-getting-server-authority-test-for-testing-your-games-with-it/3928990)
- [Roblox Community MCP for Claude Code](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)
- [How to Test Your Roblox Game](https://metablox.gg/wiki/robloxstudio/gametest/)
