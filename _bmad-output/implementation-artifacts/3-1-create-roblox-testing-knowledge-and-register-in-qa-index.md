# Story 3.1: Create Roblox Testing Knowledge and Register in QA Index

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **QA engineer using the BMGD testing workflow for a Roblox project**,
I want **Roblox-specific testing knowledge loaded by the QA agent**,
so that **I receive expert guidance on TestEZ, Studio play mode testing, MCP-based automation, DataStore mocking, and CI/CD pipelines for Roblox**.

## Acceptance Criteria

1. **Given** `roblox-testing.md` is created at `src/gametest/knowledge/`, **When** the QA agent loads testing knowledge for a Roblox project, **Then** the file contains all 11 sections matching the testing template structure (Overview, Test Framework Setup, Unit Tests, Integration Tests, Studio Testing Tools, Test Utilities, CI Integration, Performance Testing, Best Practices, Troubleshooting, End-to-End Testing)

2. **Given** the file exists, **When** reviewed, **Then** code examples use Luau with `--!strict` type annotations and game-domain relevant tests (damage, inventory, player data)

3. **Given** the file exists, **When** reviewed, **Then** no deprecated libraries appear (no `wait()` — use `task.wait()`, no Lemur as primary — use Lune, no Foreman — use Rokit, no Nexus Unit Testing)

4. **Given** `qa-index.csv` is updated, **When** the QA agent indexes testing knowledge, **Then** a new row exists: `roblox-testing,Roblox TestEZ Testing,"Roblox testing with TestEZ, Studio play mode, MCP automation, and CI pipelines","roblox,testez,unit-tests,luau",knowledge/roblox-testing.md`

5. The CSV maintains consistent 5-column format with existing rows

6. The markdown file passes `npm run lint:md` with zero errors

## Tasks / Subtasks

- [x] Task 1: Verify `roblox-testing.md` does not already exist (AC: all)
  - [x] Run `ls src/gametest/knowledge/ | grep roblox`
  - [x] If exists and complete → skip Task 2, go to Task 3
  - [x] If absent → proceed with Task 2

- [x] Task 2: Create `src/gametest/knowledge/roblox-testing.md` (AC: #1, #2, #3)
  - [x] Section 1 (Overview): 2–3 prose sentences — TestEZ as primary BDD framework, Studio play mode, MCP automation, Lune alternative
  - [x] Section 2 (Test Framework Setup): 3 subsections — Installation via Wally (rokit.toml + wally.toml), Project Structure tree, Test Runner Bootstrap
  - [x] Section 3 (Unit Tests): 3 subsections — Basic Test Structure, Parameterized Tests, Testing ModuleScripts — with Luau code examples (damage calc, weapon config)
  - [x] Section 4 (Integration Tests): 4 subsections — Service Mocking, DataStore Testing (MockDataStoreService), RemoteEvent Testing, MCP Play Mode Testing
  - [x] Section 5 (Studio Testing Tools): 3 subsections — Play Mode Options (F5/F6/F8), Local Server Testing, Device Emulation
  - [x] Section 6 (Test Utilities): 2 subsections — Custom Assertions (GameAssert), Test Data Factories (TestFactory)
  - [x] Section 7 (CI Integration): 3 subsections — Toolchain Setup (Rokit), Linting and Formatting (Selene + StyLua), GitHub Actions workflow YAML
  - [x] Section 8 (Performance Testing): 2 subsections — MicroProfiler, Benchmark Utilities
  - [x] Section 9 (Best Practices): DO list (6 bullets) and DON'T list (5 bullets)
  - [x] Section 10 (Troubleshooting): 6-row table (Issue / Cause / Fix)
  - [x] Section 11 (End-to-End Testing): Cross-reference to `knowledge/e2e-testing.md` + Roblox-specific E2E checklist (7 items)

- [x] Task 3: Verify `roblox-testing` row does not already exist in `qa-index.csv` (AC: #4, #5)
  - [x] Run `grep "roblox" src/gametest/qa-index.csv`
  - [x] If row exists and correct → skip subtask 3.2
  - [x] Append new row to `src/gametest/qa-index.csv` (no trailing newline added — match existing file end)

- [x] Task 4: Run `npm run lint:md` and fix any errors (AC: #6)

### Review Findings

- [x] [Review][Decision] Party Simulator labeled as "(beta)" in Device Emulation section — spec anti-patterns call out New Audio API specifically; unclear if Party Simulator has also exited beta as of 2026. If still beta, keep; if released, remove "(beta)" label. [`src/gametest/knowledge/roblox-testing.md`]

- [x] [Review][Patch] MockDataStoreService missing from wally.toml dev-dependencies — DataStore Testing section `require`s it from Packages but it is not declared in the wally.toml example; `wally install` would not install it [`src/gametest/knowledge/roblox-testing.md`]
- [x] [Review][Patch] MCP Play Mode Testing code block missing `--!strict` annotation — AC 2 requires all Luau code examples to use --!strict; this is the only code block without it [`src/gametest/knowledge/roblox-testing.md`]
- [x] [Review][Patch] benchmark: unguarded division by zero when `iterations = 0` — `elapsed / iterations` produces NaN silently; add guard or document minimum value [`src/gametest/knowledge/roblox-testing.md`]
- [x] [Review][Patch] MCP script: `player.Character` accessed without stored reference across consecutive asserts — between the two asserts a context switch can nil the character reference; store as local before asserting [`src/gametest/knowledge/roblox-testing.md`]
- [x] [Review][Patch] CI GitHub Actions YAML missing headless lune test step — prose says "use `lune run tests/runner.luau`" but neither job includes this step; CI passes green with broken unit tests [`src/gametest/knowledge/roblox-testing.md`]
- [x] [Review][Patch] beforeEach: MockDataStoreService.ResetData called before setDataStoreService — if PlayerDataService caches the DataStore ref at require-time, the swap has no effect and tests silently hit a stale (or real) service [`src/gametest/knowledge/roblox-testing.md`]
- [x] [Review][Patch] CSV tags incomplete — `"roblox,testez,unit-tests,luau"` missing `ci`, `integration`, `e2e` coverage described in the entry [`src/gametest/qa-index.csv`]
- [x] [Review][Patch] Project structure diagram omits `tests/shared/` directory — utility module comments reference `src/tests/shared/GameAssert.luau` and `src/tests/shared/TestFactory.luau` but the tree does not show it; also note that TestEZ bootstrap scanning `tests/` root would pick up non-spec utility modules (they return tables, not functions) [`src/gametest/knowledge/roblox-testing.md`]

- [x] [Review][Defer] `rokit trust` security implication undocumented — unconditionally trusts all binaries in rokit.toml; not critical for a knowledge doc but practitioners should understand the implication [deferred, pre-existing]
- [x] [Review][Defer] `ok-nick/setup-rokit@v0.1` not pinned to commit SHA — mutable tag is a supply-chain risk; best practice for CI templates [deferred, pre-existing]
- [x] [Review][Defer] GameAssert.nearlyEqual uses absolute tolerance (0.001) not relative — misleading for large damage values; design choice, add a comment [deferred, pre-existing]
- [x] [Review][Defer] TestFactory `(defaults :: any)[k] = v` undermines `--!strict` — accepted Luau idiom for flexible factories; no alternative without verbose overload signatures [deferred, pre-existing]
- [x] [Review][Defer] CombatService mock passes plain tables — valid only if service uses duck-typed tables; no caveat noted [deferred, pre-existing]

## Dev Notes

### Target Files

| Action | File | Notes |
|--------|------|-------|
| Create | `src/gametest/knowledge/roblox-testing.md` | ~390 lines, no YAML frontmatter |
| Append row | `src/gametest/qa-index.csv` | 1 new row at end of file |

**No other files are touched.** QA agent SKILL.md identity update is Epic 4 (Story 4.1), not this story.

### Template Reference

Study `src/gametest/knowledge/godot-testing.md` and `src/gametest/knowledge/unity-testing.md` for formatting conventions:

- H1: Single title line (`# Roblox TestEZ Testing Guide`)
- H2: Major sections (Overview, Test Framework Setup, Unit Tests, etc.)
- H3: Subsections within each H2
- All code blocks include language identifier: `` ```luau ``, `` ```toml ``, `` ```bash ``, `` ```yaml ``
- No YAML frontmatter — file starts directly with H1
- No cross-references to other BMGD files except the final E2E section which references `knowledge/e2e-testing.md`
- Best Practices: two H3 subsections labeled `### DO` and `### DON'T` with bullet lists (no code blocks)
- Troubleshooting: plain markdown table with columns `| Issue | Cause | Fix |`

### Section-by-Section Content Spec

#### Section 1: Overview

```
TestEZ is the primary BDD-style testing framework for Roblox (describe/it/expect syntax, inspired by RSpec/Mocha/Chai). Roblox Studio provides built-in play mode testing that simulates the client-server split, and the built-in MCP Server (Studio 2026+) enables automated play mode testing via `run_script_in_play_mode`. For pure logic tests outside Studio, Lune (standalone Luau runtime) is the modern headless alternative.
```

No code examples in Overview.

#### Section 2: Test Framework Setup

**H3: Installation via Rokit and Wally**

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
```

Run `wally install` to pull packages into `Packages/` directory.

**H3: Project Structure**

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
    └── init.server.luau
```

**H3: Test Runner Bootstrap**

```luau
-- src/tests/init.server.luau
local TestEZ = require(game.ReplicatedStorage.Packages.TestEZ)
local testRoot = script.Parent

TestEZ.TestBootstrap:run({ testRoot }, TestEZ.Reporters.TextReporter)
```

#### Section 3: Unit Tests

**H3: Basic Test Structure** — `describe`/`it`/`expect`, damage calculation example
**H3: Parameterized Tests** — table-driven with `for _, scenario in scenarios do`
**H3: Testing ModuleScripts** — `WeaponConfig` with `getDPS()` — equivalent of Godot's Resources / Unity's ScriptableObjects

TestEZ assertion API to document:
- `expect(value).to.equal(other)` — strict equality
- `expect(value).to.be.ok()` — truthy
- `expect(value).to.be.a("string")` — type check
- `expect(value).never.to.equal(other)` — negation
- `expect(function() ... end).to.throw()` — error throwing

#### Section 4: Integration Tests

**H3: Service Mocking** — `beforeEach` with mock player/enemy tables; `CombatService.attack()` example
**H3: DataStore Testing** — `MockDataStoreService` (buildthomas/MockDataStoreService from Wally); `MockDataStoreService.ResetData()` in `beforeEach`
**H3: RemoteEvent Testing** — note that RemoteEvent/RemoteFunction testing requires Studio Local Server mode (no headless option); describe the workflow
**H3: MCP Play Mode Testing** — `run_script_in_play_mode` via built-in MCP server; include working assert script that validates player spawn

#### Section 5: Studio Testing Tools

**H3: Play Mode Options**
- **Play (F5):** Loads game as a player; single client + server in one window
- **Play Here (F6):** Spawns character at camera location
- **Run (F8):** Runs scripts only, no character spawned; useful for server-only testing

**H3: Local Server Testing**
- Test > Start with 1–8 simulated players
- Opens separate windows: 1 server window + N client windows
- Essential for RemoteEvent replication, client-server split logic
- Can switch focus between any window

**H3: Device Emulation**
- Player Emulator for localization and content policy testing
- Device-specific resolution testing
- Party Simulator (beta) for party-based logic

No code examples in this section.

#### Section 6: Test Utilities

**H3: Custom Assertions** — `GameAssert` with `nearlyEqual`, `withinRange`, `hasComponent`
**H3: Test Data Factories** — `TestFactory` with `createMockPlayer`, `createMockWeapon`

Both as full Luau code blocks with `--!strict` annotations.

#### Section 7: CI Integration

**H3: Toolchain Setup (Rokit)**

```bash
# Install Rokit (https://github.com/rojo-rbx/rokit)
curl -sSf https://raw.githubusercontent.com/rojo-rbx/rokit/main/scripts/install.sh | sh

# Trust and install project tools from rokit.toml
rokit trust
rokit install

# Install Wally packages
wally install
```

**H3: Linting and Formatting**

```bash
selene src/
stylua --check src/
```

**H3: GitHub Actions**

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
```

Note: Headless unit tests in CI can run via Lune (`lune run tests/runner.luau`) using the `testez-luau` fork. This avoids needing Studio for pure-logic tests. Include brief callout.

#### Section 8: Performance Testing

**H3: MicroProfiler**
- Access: Ctrl+F6 in running game
- Shows timeline of every frame: rendering, physics, scripts
- Frame budget: **17ms for 60 FPS target**
- X-Ray mode for memory allocations; FlameGraphs and Diff FlameGraphs (2024+ update)
- Script computation target: < 5ms per frame

**H3: Benchmark Utilities**

```luau
local function benchmark(name: string, iterations: number, fn: () -> ())
    local start = os.clock()
    for _ = 1, iterations do
        fn()
    end
    local elapsed = os.clock() - start
    local perCall = elapsed / iterations * 1000

    print(string.format("[PERF] %s: %.3fms avg (%d iterations, %.3fs total)",
        name, perCall, iterations, elapsed))

    return { name = name, avgMs = perCall, totalMs = elapsed * 1000 }
end
```

#### Section 9: Best Practices

**DO:**
- Test shared modules (`ReplicatedStorage`) in isolation — they're the easiest to unit test
- Use TestEZ `describe`/`it` blocks to organize tests by feature
- Mock services (`DataStoreService`, `HttpService`) instead of calling real APIs in tests
- Test server-side logic separately from client-side logic
- Use `.spec.luau` file naming convention for test discovery
- Run `selene` + `stylua --check` before every commit

**DON'T:**
- Don't test Roblox engine built-in behavior (physics, rendering)
- Don't rely on execution order between test files
- Don't use `wait()` in tests — use `task.wait()` with explicit durations
- Don't test with real DataStores in CI — use `MockDataStoreService`
- Don't leave test Instances in the DataModel — clean up in `afterEach`

#### Section 10: Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Tests not found | Wrong file suffix | Use `.spec.luau` or `.spec.lua` suffix |
| `require` fails in test | Module path incorrect | Use `game.ReplicatedStorage.Shared.Modules.X` or relative `script.Parent` paths |
| DataStore errors in Studio | API access disabled | Enable "Enable Studio Access to API Services" in Game Settings |
| Tests hang | Yielding function in test | Avoid `task.wait()` in unit tests; reserve for integration tests only |
| Inconsistent test results | Shared state between tests | Use `beforeEach` to reset state; never share mutable state between `it` blocks |
| MCP tests fail silently | Play mode not started | Call `start_stop_play` before `run_script_in_play_mode` |

#### Section 11: End-to-End Testing

Opening paragraph: cross-reference to `knowledge/e2e-testing.md` for shared infrastructure patterns.

**Quick E2E Checklist for Roblox:**
- [ ] Create test runner Script under `ServerScriptService`
- [ ] Implement `ScenarioBuilder` for your game's domain (Luau `ModuleScript`)
- [ ] Create `InputSimulator` wrapping `ContextActionService`/`UserInputService`
- [ ] Add async assertion utilities using `task.wait()` polling
- [ ] Organize E2E tests under `tests/e2e/`
- [ ] Use MCP `run_script_in_play_mode` for automated E2E execution
- [ ] Configure Studio local server for multiplayer E2E scenarios

### CSV Row to Append

Exact row to append to `src/gametest/qa-index.csv` (no blank line before or after, match delimiter style of existing rows):

```
roblox-testing,Roblox TestEZ Testing,"Roblox testing with TestEZ, Studio play mode, MCP automation, and CI pipelines","roblox,testez,unit-tests,luau",knowledge/roblox-testing.md
```

Verify the 5 columns match:
1. `id` = `roblox-testing`
2. `name` = `Roblox TestEZ Testing`
3. `description` = `"Roblox testing with TestEZ, Studio play mode, MCP automation, and CI pipelines"` (quoted because it contains commas)
4. `tags` = `"roblox,testez,unit-tests,luau"` (quoted)
5. `fragment_file` = `knowledge/roblox-testing.md`

### Anti-Patterns to Avoid

| DO NOT | DO INSTEAD | REASON |
|--------|-----------|--------|
| Use `foreman.toml` or `setup-foreman` action | Use `rokit.toml` and `ok-nick/setup-rokit@v0.1` | Rokit is the modern successor to Foreman; roblox-engine.md already uses Rokit |
| Use `wait()` in any code example | Use `task.wait()` | `wait()` is deprecated in Roblox |
| Reference `ProfileService` | Use `ProfileStore` | ProfileService is unmaintained; ProfileStore is the successor |
| Reference Nexus Unit Testing | Mention only TestEZ + Lune alternatives | Nexus Unit Testing was archived March 2025 |
| Reference `BridgeNet2` | Use `ByteNet` | BridgeNet2 is archived |
| Reference `Roact` | Use `React-Lua` | Roact deprecated in favor of React-Lua |
| Label New Audio API as "beta" | Treat as released | New Audio API exited beta as of 2026 |
| Add YAML frontmatter to the file | Start directly with H1 | All existing testing knowledge files have no frontmatter |
| Add cross-references to other BMGD files | Only allowed: `knowledge/e2e-testing.md` in Section 11 | Self-contained files per NFR5 |

### Toolchain Manager Clarification

The project uses **Rokit** (not Foreman). Evidence:
- `src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md` lists Rokit explicitly in the Third-Party Packages table
- `rokit.toml` is the correct config file name (not `foreman.toml`)
- GitHub Actions: `ok-nick/setup-rokit@v0.1` (not `Roblox/setup-foreman@v2`)

### Lint Command

```bash
npm run lint:md   # Markdown linting — use this, NOT npm run lint (that's YAML only)
```

### Previous Story Intelligence

- **Epics 1 and 2** established all Roblox config/knowledge content conventions:
  - No deprecated libraries (ProfileService → ProfileStore, BridgeNet2 → ByteNet, Roact → React-Lua, Foreman → Rokit)
  - Roblox-specific files placed at engine-specific knowledge paths (`src/.../knowledge/`)
  - New Audio API is released (not beta)
  - AvatarAbilities Library is the current physics option for humanoid characters
- **Story 1.2** created `roblox-engine.md` at `src/workflows/3-technical/gds-game-architecture/knowledge/` — same depth pattern applies here for `src/gametest/knowledge/`
- **Story 2.3** (in-progress): Only modifies `architecture-patterns.yaml` — no overlap with this story

### Git Context

Recent commits:
- `e079a2f` — Story 2.2 verification (stacks/templates)
- `2d06401` — Roblox entries to `decision-catalog.yaml` (Story 2.1)
- `176b715` — Roblox MCP entries to `engine-mcps.yaml` (Story 1.3)
- `1d02793` — ECS pattern to `roblox-engine.md` (Story 1.2)

Run `git log --oneline -5` to verify current state before implementing.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story 3.1]
- [Source: _bmad-output/planning-artifacts/research/roblox-testing-knowledge-research.md] — all 11 section specs and code examples
- [Source: src/gametest/knowledge/unity-testing.md] — primary template (398 lines)
- [Source: src/gametest/knowledge/godot-testing.md] — formatting conventions
- [Source: src/gametest/qa-index.csv] — CSV format reference (5-column, comma-delimited)
- [Source: src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md] — Rokit as toolchain manager

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

### Completion Notes List

- Created `src/gametest/knowledge/roblox-testing.md` with all 11 sections matching the testing template structure
- All Luau code examples use `--!strict` type annotations with game-domain relevant tests (damage calc, inventory, player data)
- No deprecated libraries: uses `task.wait()` (not `wait()`), Lune (not Lemur), Rokit (not Foreman), TestEZ (not Nexus Unit Testing)
- Appended `roblox-testing` row to `src/gametest/qa-index.csv` with correct 5-column format
- `npm run lint:md` passed with 0 errors

### File List

- `src/gametest/knowledge/roblox-testing.md` (created)
- `src/gametest/qa-index.csv` (modified — appended roblox-testing row)
