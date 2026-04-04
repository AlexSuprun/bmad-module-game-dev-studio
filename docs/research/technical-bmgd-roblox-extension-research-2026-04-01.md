---
stepsCompleted: [1, 2, 3, 4, 5, 6]
inputDocuments:
  - 'bmad-module-game-dev-studio/src/ (BMGD source code)'
  - '_bmad-output/planning-artifacts/research/technical-roblox-game-dev-with-claude-code-research-2026-04-01.md'
workflowType: 'research'
lastStep: 6
research_type: 'technical'
research_topic: 'Extending BMAD Game Dev Studio (BMGD) to support Roblox game development'
research_goals: 'Understand how to add Roblox support to BMGD, check for existing similar extensions, identify integration patterns and architecture for Roblox+BMGD workflow'
user_name: 'Alex Suprun'
date: '2026-04-01'
web_research_enabled: true
source_verification: true
---

# Adding Roblox Engine Support to BMAD Game Dev Studio: Technical Research Report

**Date:** 2026-04-01
**Author:** Alex Suprun
**Research Type:** Technical

---

## Executive Summary

BMAD Game Dev Studio (BMGD) v0.2.2 has first-class support for Unity, Unreal, Godot, and Phaser, but **no Roblox support exists anywhere** -- not in the module, not in community forks, not in the BMAD-FOUNDRY, and not on npm. However, BMGD's architecture is explicitly designed for multi-engine extensibility through a clean knowledge-fragment pattern. Adding Roblox requires creating **3 new files** and modifying **5 existing files**, following well-documented templates.

**Key Findings:**

1. **No existing Roblox extension** -- Zero results across GitHub, npm, BMAD community. This would be entirely new work.
2. **Clean extension architecture** -- BMGD uses swappable knowledge files, decision catalogs, and config-driven workflows. Roblox fits naturally.
3. **9 total file changes** -- 3 new files (roblox-engine.md, roblox-testing.md, setup-roblox.md) + 5 config updates (module.yaml, decision-catalog.yaml, engine-mcps.yaml, architecture-patterns.yaml, qa-index.csv) + agent SKILL.md updates
4. **Recommended strategy** -- Fork BMGD, add files locally first for immediate use, then submit upstream PRs following the CONTRIBUTING.md process (Discord discussion first)
5. **Roblox domain knowledge is ready** -- Prior research doc provides complete Roblox architecture content to populate the knowledge files

---

## Table of Contents

1. [BMGD Engine Support Architecture](#bmgd-engine-support-architecture)
2. [Existing Extensions Search Results](#existing-extensions-search-results)
3. [Integration Blueprint: File-by-File Plan](#integration-blueprint-file-by-file-plan)
4. [Roblox Decision Catalog Mappings](#roblox-decision-catalog-mappings)
5. [Architecture Patterns for Roblox](#architecture-patterns-for-roblox)
6. [Roblox Engine Knowledge File Structure](#roblox-engine-knowledge-file-structure)
7. [Roblox Testing Knowledge File Structure](#roblox-testing-knowledge-file-structure)
8. [MCP Server Integration](#mcp-server-integration)
9. [Agent Updates](#agent-updates)
10. [Contribution Strategy](#contribution-strategy)
11. [Implementation Roadmap](#implementation-roadmap)
12. [Validation Checklist](#validation-checklist)

---

## BMGD Engine Support Architecture

BMGD uses a **modular, knowledge-fragment-based architecture** with five integration layers:

| Layer | Location | Purpose | Current Engines |
|-------|----------|---------|-----------------|
| **Engine Knowledge** | `src/workflows/3-technical/.../knowledge/` | Deep architecture guides per engine | unity, unreal, godot, phaser |
| **Decision Catalog** | `decision-catalog.yaml` | Engine options with `pairs_with` relationships | unity, unreal, godot, phaser, bevy, monogame |
| **Architecture Patterns** | `architecture-patterns.yaml` | Game pattern categories with engine recommendations | Generic (12 patterns) |
| **Engine MCPs** | `engine-mcps.yaml` | AI dev tool recommendations per engine | unity (2), unreal (2), godot (3), general (1) |
| **Testing Knowledge** | `src/gametest/knowledge/` | Engine-specific testing patterns | unity, unreal, godot |
| **Module Config** | `src/module.yaml` | Install-time engine selection | unity, unreal, godot, other |

### How It Works at Runtime

1. User selects engine during `npx bmad-method install` via `module.yaml` multi-select
2. Architecture workflow (step-03) loads `knowledge/{engine}-engine.md` for the selected engine
3. Decision catalog maps each architectural choice to engine-specific `pairs_with` options
4. QA agent loads `knowledge/{engine}-testing.md` via `qa-index.csv` lookup
5. All agents read `project-context.md` (generated output) which contains the selected engine

**Critical insight**: Workflows are engine-agnostic in structure, engine-aware in data loading. No `if engine == "unity"` logic exists -- the system is entirely data-driven.

---

## Existing Extensions Search Results

### Search Summary: No Roblox Extension Exists

| Source | Search Terms | Results |
|--------|-------------|---------|
| GitHub repos | "bmad roblox", "bmgd roblox" | **0 results** |
| GitHub repos | "bmad-module" (all) | **Only official BMGD repo** (130 stars, 13 forks) |
| npm packages | "bmad-game-dev", "bmad-module" | **No Roblox-related packages** |
| BMGD Issues | "roblox" | **0 results** (12 closed issues, all tooling/bugs) |
| BMGD Code | "roblox" | **0 results** |
| BMAD-FOUNDRY | All files | **Completely empty repo** (created Nov 2025, 0 files) |
| BMGD Discussions | (tab disabled) | **Not available** (`has_discussions: false`) |

### Related Findings

- **`notandruu/roblox-coding-agent`** -- Standalone Roblox AI agent (March 2026, 0 stars). Not BMAD-based.
- **BMAD ecosystem** has 6+ official modules (Method, BMGD, Builder, CIS, TEA, WDS) but none target Roblox
- **No other AI game dev frameworks** have structured Roblox support comparable to what BMGD offers for Unity/Unreal/Godot

**Conclusion**: This would be the first Roblox extension for BMGD. No existing work to build upon, but the template pattern from Unity/Unreal/Godot is clear.

---

## Integration Blueprint: File-by-File Plan

### New Files to Create (3)

| # | File | Template Source | Est. Lines | Priority |
|---|------|----------------|-----------|----------|
| 1 | `src/workflows/3-technical/gds-game-architecture/knowledge/roblox-engine.md` | `unity-engine.md` (573 lines) | 500-600 | **Critical** |
| 2 | `src/gametest/knowledge/roblox-testing.md` | `unity-testing.md` (398 lines) | 300-400 | **High** |
| 3 | `docs/how-to/setup-roblox.md` | `docs/how-to/setup-unity.md` | 100-150 | **Medium** |

### Existing Files to Modify (6+)

| # | File | Change | Priority |
|---|------|--------|----------|
| 4 | `src/module.yaml` | Add `"roblox"` to `primary_platform` multi-select | **Critical** |
| 5 | `src/workflows/.../decision-catalog.yaml` | Add roblox options under all 14 categories | **Critical** |
| 6 | `src/workflows/.../engine-mcps.yaml` | Add `roblox:` engine block with MCP entries | **High** |
| 7 | `src/workflows/.../architecture-patterns.yaml` | Add `roblox_experience` and `roblox_multiplayer` patterns | **High** |
| 8 | `src/gametest/qa-index.csv` | Add `roblox-testing` row | **High** |
| 9+ | `src/agents/*/SKILL.md` (2-3 agents) | Add "Roblox" to engine expertise lists | **Medium** |

---

## Roblox Decision Catalog Mappings

Complete mapping of all 14 BMGD decision categories to Roblox-specific options:

### 1. game_engine

```yaml
roblox:
  pairs_with: ["Luau", "Roblox Studio", "Rojo", "Knit", "Wally", "Roblox MCP Server"]
```

### 2. rendering_pipeline

```yaml
roblox_future_lighting:
  pairs_with: ["Roblox", "PBR materials", "SurfaceAppearance", "ShadowMap", "high-end visuals", "StreamingEnabled"]
roblox_shadowmap:
  pairs_with: ["Roblox", "ShadowMap lighting", "mid-range hardware", "mobile-friendly"]
roblox_compatibility:
  pairs_with: ["Roblox", "Compatibility lighting", "low-end devices", "legacy content"]
```

### 3. physics_system

```yaml
roblox_constraints:
  pairs_with: ["Roblox", "AlignPosition", "AlignOrientation", "BallSocketConstraint", "HingeConstraint", "SpringConstraint"]
roblox_humanoid:
  pairs_with: ["Roblox", "Humanoid", "character movement", "WalkSpeed", "JumpPower"]
roblox_custom_controller:
  pairs_with: ["Roblox", "raycasting", "CFrame manipulation", "custom movement"]
```

### 4. audio_system

```yaml
roblox_audio_api:
  pairs_with: ["Roblox", "AudioPlayer", "AudioEmitter", "AudioListener", "Wire-based routing", "spatial audio"]
roblox_soundservice:
  pairs_with: ["Roblox", "SoundService", "Sound", "SoundGroup", "simple audio"]
```

### 5. networking

```yaml
roblox_remotes:
  pairs_with: ["Roblox", "RemoteEvent", "RemoteFunction", "UnreliableRemoteEvent", "server-authoritative"]
knit_networking:
  pairs_with: ["Roblox", "Knit", "Services", "Controllers", "automatic remote creation"]
bridgenet2:
  pairs_with: ["Roblox", "BridgeNet2", "optimized networking", "identifier compression"]
```

### 6. input_system

```yaml
roblox_contextactionservice:
  pairs_with: ["Roblox", "ContextActionService", "action binding", "priority-based", "mobile buttons", "gamepad"]
roblox_userinputservice:
  pairs_with: ["Roblox", "UserInputService", "raw input", "InputBegan", "InputEnded", "device detection"]
roblox_proximityprompts:
  pairs_with: ["Roblox", "ProximityPrompt", "interaction system", "E to interact"]
```

### 7. ai_system

```yaml
roblox_pathfindingservice:
  pairs_with: ["Roblox", "PathfindingService", "NavigationMesh", "PathWaypoints", "agent parameters"]
simplepath:
  pairs_with: ["Roblox", "SimplePath", "wrapper library", "NPC movement"]
state_machines_luau:
  pairs_with: ["Roblox", "Luau state machines", "enemy AI", "NPC behavior"]
```

### 8. save_persistence

```yaml
profileservice:
  pairs_with: ["Roblox", "ProfileService", "session locking", "data loss prevention", "player profiles"]
lapis:
  pairs_with: ["Roblox", "Lapis", "DataStore wrapper", "schema validation", "migration support"]
datastoreservice_native:
  pairs_with: ["Roblox", "DataStoreService", "GetAsync", "SetAsync", "UpdateAsync", "simple data"]
memorystoreservice:
  pairs_with: ["Roblox", "MemoryStoreService", "ephemeral data", "matchmaking queues", "leaderboards"]
```

### 9. ui_framework

```yaml
roblox_native_gui:
  pairs_with: ["Roblox", "ScreenGui", "Frame", "TextLabel", "UIListLayout", "UIGridLayout", "StarterGui"]
fusion:
  pairs_with: ["Roblox", "Fusion", "reactive UI", "declarative", "state management"]
roact_react_lua:
  pairs_with: ["Roblox", "Roact", "React-Lua", "component-based", "virtual DOM"]
```

### 10. asset_pipeline

```yaml
roblox_packages_rojo:
  pairs_with: ["Roblox", "Rojo", "Wally", "Git", "filesystem sync", "Packages"]
roblox_toolbox:
  pairs_with: ["Roblox", "Creator Store", "Toolbox", "free models", "rapid prototyping"]
```

### 11. deployment_platform

```yaml
roblox_crossplatform:
  pairs_with: ["Roblox", "PC", "Mac", "iOS", "Android", "Xbox", "PlayStation", "Meta Quest", "auto-scaling UI"]
```

### 12. animation_system

```yaml
roblox_animator:
  pairs_with: ["Roblox", "AnimationController", "Animator", "KeyframeSequence", "priority system"]
roblox_tweenservice:
  pairs_with: ["Roblox", "TweenService", "property animation", "UI animation", "easing styles"]
moon_animator:
  pairs_with: ["Roblox", "Moon Animator", "Studio plugin", "advanced keyframing", "cinematic"]
```

### 13. monetization

```yaml
roblox_gamepasses:
  pairs_with: ["Roblox", "GamePasses", "one-time purchase", "permanent perks", "MarketplaceService"]
roblox_developer_products:
  pairs_with: ["Roblox", "Developer Products", "consumable purchases", "in-game currency", "ProcessReceipt"]
roblox_subscriptions:
  pairs_with: ["Roblox", "Subscriptions", "recurring revenue", "monthly billing", "tiered access"]
```

### 14. localization

```yaml
roblox_localizationservice:
  pairs_with: ["Roblox", "LocalizationService", "LocalizationTable", "Cloud Localization Portal", "auto-translation"]
```

---

## Architecture Patterns for Roblox

### New Requirement Patterns

```yaml
roblox_experience:
  triggers: ["roblox", "luau", "roblox studio", "roblox game"]
  decisions_needed:
    - game_engine
    - rendering_pipeline
    - networking
    - save_persistence
    - monetization
    - deployment_platform
  suggested_stack:
    - "Roblox Studio + Luau"
    - "Rojo + Wally + Git for professional workflow"
    - "Knit framework for service-controller architecture"
    - "Future Lighting + PBR SurfaceAppearance"
    - "Constraint-based physics + Humanoid"
    - "RemoteEvent + UnreliableRemoteEvent"
    - "ProfileService for player data"
    - "Native ScreenGui or Fusion for reactive UI"
    - "GamePasses + Developer Products + Subscriptions"

roblox_multiplayer:
  triggers: ["roblox multiplayer", "roblox pvp", "roblox co-op"]
  decisions_needed:
    - networking
    - save_persistence
    - ai_system
  suggested_stack:
    - "RemoteEvents for client-server communication"
    - "UnreliableRemoteEvent for high-frequency data (positions)"
    - "Server-side validation for all game-critical logic"
    - "ProfileService for player data with session locking"
    - "MemoryStoreService for matchmaking queues"
    - "MessagingService for cross-server events"
```

### Recommended Default Stacks

```yaml
roblox_standard:
  name: "Roblox Experience (Standard)"
  components: ["Roblox Engine", "Luau", "Rojo", "Knit", "Future Lighting",
    "Constraint Physics", "New Audio API", "RemoteEvent + UnreliableRemoteEvent",
    "ContextActionService", "PathfindingService + SimplePath", "ProfileService",
    "Native ScreenGui + TopbarPlus", "Rojo + Wally + Git",
    "Cross-platform", "Animator + Moon Animator",
    "GamePasses + Developer Products", "LocalizationService"]
  good_for: "Roblox multiplayer experiences, obbies, tycoons, simulators, RPGs"

roblox_simple:
  name: "Roblox Simple Game"
  components: ["Roblox Engine", "Luau", "Studio-only workflow",
    "ShadowMap Lighting", "Native physics", "SoundService",
    "RemoteEvent", "ContextActionService", "DataStoreService (native)",
    "Native ScreenGui", "Toolbox assets", "Built-in Animation Editor",
    "GamePasses + Developer Products"]
  good_for: "Simple games, game jams, learning, obbies, showcases"
```

---

## Roblox Engine Knowledge File Structure

The `roblox-engine.md` file must follow the same 14-section structure as `unity-engine.md`:

| Section | Roblox Content | Est. Lines |
|---------|---------------|-----------|
| **Overview** | Roblox platform: Luau, client-server, Instance hierarchy, managed hosting | 10 |
| **Architecture Fundamentals** | Instance-Service model, Client-Server split, Replication, ModuleScript patterns, Rojo project structure | 120 |
| **Coding Best Practices** | Luau type annotations, ModuleScript organization, `task.spawn`/`task.defer`, event-driven over polling, naming conventions | 100 |
| **Performance Optimization** | StreamingEnabled, object pooling, RemoteEvent batching, rendering budgets, `while wait()` anti-pattern | 60 |
| **Third-Party Packages** | Knit, ProfileService, Roact/Fusion, Jecs, SimplePath, TopbarPlus, BridgeNet2, TestEZ, Wally packages | 60 |
| **Common Architectural Patterns** | Service-Controller (Knit), ECS (Jecs), Hybrid approach, Bootstrap pattern | 50 |
| **Licensing and Cost** | Roblox Studio: free, hosting: free, DevEx revenue share, API costs | 15 |
| **When NOT to Use Roblox** | Non-Roblox platforms, custom rendering, offline games, precise physics, mature content | 25 |
| **Platform-Specific Caveats** | Mobile (60%+ players, touch), Xbox/PS (gamepad-only), VR (Meta Quest), cross-platform UI scaling | 30 |
| **Security Architecture** | Server-authoritative model, never trust client, RemoteEvent validation, rate limiting, sanity checks | 40 |
| **Data Architecture** | DataStoreService, MemoryStoreService, ProfileService patterns, cross-server MessagingService | 30 |
| **Rojo Project Structure** | `default.project.json`, filesystem-to-Instance mapping, `init.server.luau`/`init.client.luau`/`init.luau` conventions | 30 |
| **Architecture Decision Checklist** | 12-item checklist (server/client split, data persistence, networking, monetization, etc.) | 15 |
| **Total** | | **~585** |

### Content Sources

All Roblox domain content is available in the prior research document:
`_bmad-output/planning-artifacts/research/technical-roblox-game-dev-with-claude-code-research-2026-04-01.md`

Key sections to draw from:
- Technology Stack Analysis (lines 88-200)
- Architectural Patterns and Design (lines 325-460)
- Security Architecture (lines 416-440)
- Data Architecture (lines 442-460)

---

## Roblox Testing Knowledge File Structure

The `roblox-testing.md` file follows `unity-testing.md` (10 sections, 398 lines):

| Section | Roblox Content | Est. Lines |
|---------|---------------|-----------|
| **Overview** | TestEZ framework, Studio testing, MCP-based testing | 10 |
| **Test Framework Setup** | TestEZ installation via Wally, test runner setup, Rojo test structure | 40 |
| **Unit Tests** | TestEZ `describe`/`it` syntax, assertions, mocking services | 60 |
| **Integration Tests** | Service interaction testing, RemoteEvent testing, DataStore mocking | 60 |
| **Play Mode Testing** | Studio play mode, client-server split view, MCP `run_script_in_play_mode` | 40 |
| **Test Utilities** | Custom assertion helpers, test data factories | 30 |
| **CI Integration** | GitHub Actions + Rojo build + Selene + StyLua, Lemur headless tests | 40 |
| **Best Practices** | Server-first testing, isolation, deterministic tests | 20 |
| **Troubleshooting** | Common issues: service ordering, yielding in tests, DataStore mocking | 20 |
| **Total** | | **~320** |

---

## MCP Server Integration

### `engine-mcps.yaml` Roblox Entry

```yaml
roblox:
  engine: "Roblox Engine"
  mcps:
    roblox_official_mcp:
      name: "Official Roblox Studio MCP Server"
      repo: "Roblox/studio-rust-mcp-server"
      search_term: "Roblox official MCP server Studio"
      description: "Official Rust-based MCP server that ships with Roblox Studio. Provides direct Studio manipulation including code execution, model insertion, play mode control, and console output reading."
      capabilities:
        - "Execute Luau code in Studio (run_code)"
        - "Insert models from Creator Store (insert_model)"
        - "Read console output (get_console_output)"
        - "Start/stop play mode (start_stop_play)"
        - "Run scripts in play mode with log capture (run_script_in_play_mode)"
        - "Check Studio mode state (get_studio_mode)"
      install_type: "Build from source (Rust) + Studio Plugin"
      install_steps:
        - "Clone Roblox/studio-rust-mcp-server"
        - "Build with cargo (requires Rust toolchain)"
        - "Install companion Studio plugin"
        - "Configure MCP JSON config to point to server binary"
      requirements: "Roblox Studio 2025+, Rust toolchain for building"
      supported_clients: ["Claude Desktop", "Claude Code"]
      license: "Apache-2.0"
      benefit_summary: "Direct AI agent control of Roblox Studio for code execution, testing, and model insertion"

    roblox_community_mcp:
      name: "Roblox Studio MCP for Claude Code"
      repo: "ZubeidHendricks/roblox-studio-mcp-claude-code"
      search_term: "roblox studio mcp claude code community"
      description: "Community-maintained MCP server specifically designed for Claude Code integration with Roblox Studio."
      capabilities:
        - "Code execution in Studio"
        - "Studio state querying"
        - "Optimized for Claude Code workflow"
      install_type: "npm package"
      install_steps:
        - "Clone the repository"
        - "Follow SETUP_GUIDE.md"
        - "Configure MCP settings"
      requirements: "Roblox Studio, Node.js"
      supported_clients: ["Claude Code"]
      license: "MIT"
      benefit_summary: "Easier setup than official server, purpose-built for Claude Code"
```

---

## Agent Updates

### Files Requiring Engine Name Updates

| Agent | File | Current Text | Updated Text |
|-------|------|-------------|-------------|
| Game Developer | `gds-agent-game-dev/SKILL.md` | "expertise in **Unity, Unreal, and custom engines**" | "expertise in **Unity, Unreal, Godot, Roblox, and custom engines**" |
| Game QA | `gds-agent-game-qa/SKILL.md` | "12+ years in **Unity, Unreal, and Godot**" | "12+ years in **Unity, Unreal, Godot, and Roblox**" |

Other agents (Architect, Designer, Scrum Master, Tech Writer) reference engines dynamically via `project-context.md` and don't need text changes.

---

## Contribution Strategy

### Recommended Approach: Two-Phase (Local First, Then Upstream PR)

#### Phase 1: Local Implementation (1-2 days)

Create the files directly in the local BMGD installation for immediate use:

1. Create `roblox-engine.md` knowledge file
2. Create `roblox-testing.md` knowledge file
3. Update `decision-catalog.yaml` with Roblox options
4. Update `engine-mcps.yaml` with Roblox MCP entries
5. Update `architecture-patterns.yaml` with Roblox patterns
6. Update `qa-index.csv` with roblox-testing row
7. Update `module.yaml` to add "roblox" option
8. Update agent SKILL.md files

#### Phase 2: Upstream Contribution (1-2 weeks)

1. **Discord discussion** -- Post in #suggestions-feedback explaining the Roblox extension
2. **Feature request issue** -- Create on GitHub with the integration blueprint
3. **Submit PRs** -- Break into 3-4 small PRs under the 800-line limit:
   - PR 1: `roblox-engine.md` + `module.yaml` update (~400 lines)
   - PR 2: `decision-catalog.yaml` + `engine-mcps.yaml` + `architecture-patterns.yaml` updates (~200 lines)
   - PR 3: `roblox-testing.md` + `qa-index.csv` update (~350 lines)
   - PR 4: Agent SKILL.md updates + `setup-roblox.md` (~100 lines)

### Why This Approach

| Factor | Assessment |
|--------|-----------|
| **Speed** | Phase 1 gives immediate working solution |
| **Maintainability** | Phase 2 gets upstream acceptance, updates come with BMGD |
| **Community benefit** | Highest -- every BMGD user gets Roblox support |
| **Upstream acceptance likelihood** | **High** -- BMGD architecture is explicitly designed for new engines; `module.yaml` has multi-select for platforms; CONTRIBUTING.md encourages "domain-specific modules" |
| **Risk** | Low -- Roblox files are additive only; zero changes to existing engine support |

### Alternatives Considered and Rejected

| Approach | Why Rejected |
|----------|-------------|
| **Standalone `bmad-module-roblox`** | Cannot inject into BMGD's architecture workflow knowledge directory. Would need to duplicate workflow logic. Overkill for engine support. |
| **CLAUDE.md supplementation only** | Does not integrate with BMGD's structured workflows. Agents won't load Roblox knowledge automatically. |
| **BMAD-FOUNDRY contribution** | FOUNDRY repo is completely empty with no process established. Direct BMGD PR is more practical. |

---

## Implementation Roadmap

### Day 1: Core Knowledge Files

- [ ] Create `roblox-engine.md` following unity-engine.md structure (14 sections, ~585 lines)
- [ ] Update `module.yaml`: add `"roblox"` to `primary_platform` multi-select
- [ ] Update `decision-catalog.yaml`: add Roblox options under all 14 categories

### Day 2: Supporting Files

- [ ] Create `roblox-testing.md` following unity-testing.md structure (~320 lines)
- [ ] Update `engine-mcps.yaml`: add `roblox:` block with official + community MCP
- [ ] Update `architecture-patterns.yaml`: add `roblox_experience` + `roblox_multiplayer` patterns
- [ ] Update `qa-index.csv`: add roblox-testing row
- [ ] Update agent SKILL.md files (2 agents)

### Day 3: Documentation and Validation

- [ ] Create `docs/how-to/setup-roblox.md`
- [ ] Run full validation checklist
- [ ] Test by running BMGD workflows with Roblox selected
- [ ] Prepare upstream contribution (Discord post, GitHub issue)

---

## Validation Checklist

Use this after implementation to verify complete integration:

- [ ] `roblox-engine.md` created with all 14 sections + Luau code examples
- [ ] `roblox-testing.md` created with TestEZ patterns + CI integration
- [ ] `engine-mcps.yaml` has `roblox:` block with official MCP + community MCP
- [ ] `decision-catalog.yaml` updated: all 14 categories have Roblox `pairs_with` entries
- [ ] `architecture-patterns.yaml` has `roblox_experience` and `roblox_multiplayer` patterns
- [ ] `qa-index.csv` has roblox-testing row added
- [ ] `module.yaml` has "roblox" in `primary_platform` multi-select
- [ ] `gds-agent-game-dev/SKILL.md` includes "Roblox" in expertise
- [ ] `gds-agent-game-qa/SKILL.md` includes "Roblox" in expertise
- [ ] All new markdown follows existing heading levels, code fence styles, bullet patterns
- [ ] All YAML files pass linting (`npm run lint`)
- [ ] All markdown files pass linting (`npm run lint:md`)
- [ ] CSV has consistent column count with existing rows
- [ ] Architecture workflow correctly loads roblox-engine.md when Roblox is selected
- [ ] QA agent correctly loads roblox-testing.md from qa-index.csv lookup

---

## Research Methodology and Sources

### Research Approach

- **Source code analysis**: Complete reading of BMGD v0.2.2 source (`bmad-module-game-dev-studio/src/`)
- **Template analysis**: Line-by-line comparison of unity-engine.md, godot-engine.md, phaser-engine.md structures
- **Community search**: GitHub (repos, issues, code), npm (packages), BMAD-FOUNDRY
- **Cross-reference**: Prior Roblox technical research document for domain content
- **Web search**: BMAD contribution process, module template, installer architecture

### Key Sources

- [BMGD Source Code](https://github.com/bmad-code-org/bmad-module-game-dev-studio) -- Module architecture and templates
- [BMAD Module Template](https://github.com/bmad-code-org/bmad-module-template) -- Official module scaffold
- [BMAD-FOUNDRY](https://github.com/bmad-code-org/BMAD-FOUNDRY) -- Community contributions (empty)
- [BMAD Method Installer](https://github.com/bmad-code-org/BMAD-METHOD) -- Module installation system
- [Prior Roblox Research](../_bmad-output/planning-artifacts/research/technical-roblox-game-dev-with-claude-code-research-2026-04-01.md) -- Roblox domain knowledge
- [Official Roblox MCP Server](https://github.com/Roblox/studio-rust-mcp-server) -- MCP integration
- [Roblox Creator Hub](https://create.roblox.com/docs) -- Official documentation

### Confidence Levels

- **BMGD architecture analysis**: HIGH -- based on complete source code reading
- **No existing extensions**: HIGH -- exhaustive search across GitHub, npm, BMAD org
- **Integration blueprint**: HIGH -- follows exact patterns from existing engine files
- **Roblox decision mappings**: HIGH -- cross-referenced with prior verified research
- **Upstream acceptance**: MEDIUM -- architecture supports it, but maintainer approval needed

---

## Research Conclusion

Adding Roblox support to BMAD Game Dev Studio is a **well-defined, low-risk integration** that fits naturally into the existing architecture. The module was explicitly designed for multi-engine extensibility -- the knowledge-fragment pattern, decision catalogs, and config-driven workflows make adding a new engine straightforward.

The work involves **3 new files and 5-6 config modifications**, with all content derivable from the existing engine templates and the prior Roblox technical research. The recommended two-phase approach (local first, upstream PR second) provides immediate utility while contributing back to the community.

**Next step**: Begin implementation following the Day 1 roadmap -- start with `roblox-engine.md` and `module.yaml`.
