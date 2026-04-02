---
stepsCompleted: [1, 2, 3, 4]
inputDocuments:
  - 'docs/research/technical-bmgd-roblox-extension-research-2026-04-01.md'
  - 'docs/research/technical-roblox-game-dev-with-claude-code-research-2026-04-01.md'
  - 'docs/project-overview.md'
  - 'docs/source-tree-analysis.md'
  - 'docs/development-guide.md'
  - '_bmad-output/planning-artifacts/research/roblox-engine-knowledge-research.md'
  - '_bmad-output/planning-artifacts/research/roblox-decision-catalog-research.md'
  - '_bmad-output/planning-artifacts/research/roblox-testing-knowledge-research.md'
  - '_bmad-output/planning-artifacts/research/roblox-config-mcps-agents-research.md'
  - '_bmad-output/planning-artifacts/research/roblox-domain-content-mapping-research.md'
---

# bmad-module-game-dev-studio - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for adding Roblox engine support to BMAD Game Dev Studio (BMGD), decomposing the requirements from 5 focused research reports and 2 original research documents into implementable stories. Scope is limited to local implementation only (no upstream contribution process).

## Requirements Inventory

### Functional Requirements

FR1: Create `roblox-engine.md` knowledge file with all 12 sections following the existing engine template structure (Overview, Architecture Fundamentals, Luau Scripting Guide, Coding Best Practices, Performance Optimization, Third-Party Packages, Common Architectural Patterns, Security Architecture, Licensing and Cost, When NOT to Use Roblox, Platform-Specific Caveats, Architecture Decision Checklist) — estimated 550-650 lines
FR2: Create `roblox-testing.md` knowledge file with all 11 sections following the testing template structure (Overview, Framework Setup, Unit Tests, Integration Tests, Studio Testing Tools, Test Utilities, CI Integration, Performance Testing, Best Practices, Troubleshooting, E2E Testing) — estimated ~390 lines
FR3: Add `"roblox"` option to `module.yaml` `primary_platform` multi-select and update the `default` array
FR4: Add Roblox-specific entries to all 12 existing decision catalog categories in `decision-catalog.yaml` (38 new option entries total)
FR5: Add `roblox_experience` and `roblox_multiplayer` requirement patterns to `architecture-patterns.yaml`
FR6: Add `roblox_standard` and `roblox_simple` common stacks to `decision-catalog.yaml`
FR7: Add `roblox_standard` and `roblox_studio_only` starter templates to `decision-catalog.yaml`
FR8: Add `roblox:` engine block to `engine-mcps.yaml` with built-in MCP server (primary) and boshyxd community MCP (alternative) entries
FR9: Update `recommendation_rules` in `engine-mcps.yaml` with `roblox_default` and `roblox_alternative` entries
FR10: Add `roblox-testing` row to `qa-index.csv` with correct 5-column format
FR11: Update Game Developer agent (`gds-agent-game-dev/SKILL.md`) to include "Godot, Roblox" in engine expertise (lines 10, 14)
FR12: Update Game QA agent (`gds-agent-game-qa/SKILL.md`) to include "Roblox" in engine expertise and add TestEZ reference (lines 10, 14, 33)
FR13: Update Game Solo Dev agent (`gds-agent-game-solo-dev/SKILL.md`) to include "Roblox" in engine expertise (line 14)
FR14: Create `docs/how-to/setup-roblox.md` following the Diataxis how-to template structure (Prerequisites, Steps 1-8, Engine-Specific Considerations, Tips, Common Mistakes, Next Steps)
FR15: Update `docs/index.md` to list Roblox in supported engines section and add setup guide link

### NonFunctional Requirements

NFR1: All YAML files must pass linting (`npm run lint`)
NFR2: All markdown files must pass linting (`npm run lint:md`)
NFR3: All new markdown files must follow existing formatting conventions — heading levels, code fence styles, bullet patterns, no YAML frontmatter in knowledge files
NFR4: CSV additions must maintain consistent 5-column format with existing rows
NFR5: New engine knowledge files must follow the exact section structure of existing engine files (self-contained, no cross-references to other BMGD files)
NFR6: Code examples in knowledge files must use Luau with `--!strict` type annotations and include PREFERRED/AVOID comment patterns
NFR7: All Roblox content must reflect verified current information — no deprecated libraries (ProfileService, Roact, BridgeNet2, standalone MCP) as primary recommendations
NFR8: YAML entries must follow existing naming conventions — `engine_descriptor` format for option keys, consistent `pairs_with` array style

### Additional Requirements

- ProfileStore must be recommended over ProfileService (ProfileService is unmaintained; same author created ProfileStore as successor)
- React-Lua must be referenced instead of deprecated Roact
- ByteNet must replace archived BridgeNet2 in networking entries
- Built-in MCP server (ships with Roblox Studio 2026+) must be primary MCP recommendation, not the deprecated standalone `studio-rust-mcp-server`
- boshyxd/robloxstudio-mcp (39+ tools, v2.5.0) should be the community MCP alternative, not ZubeidHendricks
- Knit should be listed as an option but NOT in default stacks (no longer receiving updates)
- Decision catalog has 12 existing categories (not 14 as original research assumed) — `animation_system`, `monetization`, `localization` do not exist
- Creating 3 new decision categories is out of scope for this extension (would require entries for ALL engines, not just Roblox)
- Rokit toolchain manager should be the first install step in setup-roblox.md (manages Rojo, Wally, Selene, StyLua, Lune)
- AvatarAbilities Library (March 2026 Studio Beta) should be included as a physics option alongside Humanoid and custom controllers
- New Audio API has exited beta — should be recommended over legacy SoundService for new projects

### UX Design Requirements

N/A — This extension adds no user-facing UI. All changes are to YAML config files, markdown knowledge files, and CSV index files consumed by AI agents.

### FR Coverage Map

FR1: Epic 1 — Create roblox-engine.md knowledge file (12 sections, ~600 lines)
FR3: Epic 1 — Add "roblox" to module.yaml primary_platform multi-select
FR8: Epic 1 — Add Roblox MCP entries to engine-mcps.yaml (built-in + community)
FR9: Epic 1 — Update engine-mcps.yaml recommendation_rules for Roblox
FR4: Epic 2 — Add 38 Roblox entries across 12 decision catalog categories
FR5: Epic 2 — Add roblox_experience and roblox_multiplayer architecture patterns
FR6: Epic 2 — Add roblox_standard and roblox_simple common stacks
FR7: Epic 2 — Add roblox_standard and roblox_studio_only starter templates
FR2: Epic 3 — Create roblox-testing.md knowledge file (11 sections, ~390 lines)
FR10: Epic 3 — Add roblox-testing row to qa-index.csv
FR11: Epic 4 — Update Game Developer agent SKILL.md (add Godot, Roblox)
FR12: Epic 4 — Update Game QA agent SKILL.md (add Roblox + TestEZ reference)
FR13: Epic 4 — Update Game Solo Dev agent SKILL.md (add Roblox)
FR14: Epic 4 — Create docs/how-to/setup-roblox.md
FR15: Epic 4 — Update docs/index.md with Roblox listing

## Epic List

### Epic 1: Roblox Engine Knowledge & Platform Setup
Enable Roblox as a selectable engine with deep architecture knowledge and MCP tool recommendations — the foundation everything else builds on.
**FRs covered:** FR1, FR3, FR8, FR9

### Epic 2: Roblox Decision Catalog & Architecture Patterns
Provide intelligent Roblox-specific technology decisions across all decision categories with curated stacks and starter templates.
**FRs covered:** FR4, FR5, FR6, FR7

### Epic 3: Roblox Testing & QA Support
Enable the QA agent to provide Roblox-specific testing strategies including TestEZ, MCP-based testing, and CI/CD pipelines.
**FRs covered:** FR2, FR10

### Epic 4: Agent Awareness & Developer Onboarding
Update all agent identities to include Roblox expertise and provide a clear setup guide for new Roblox projects.
**FRs covered:** FR11, FR12, FR13, FR14, FR15

## Epic 1: Roblox Engine Knowledge & Platform Setup

Enable Roblox as a selectable engine with deep architecture knowledge and MCP tool recommendations — the foundation everything else builds on.

### Story 1.1: Add Roblox to Engine Selection

As a **BMGD user installing the module**,
I want **Roblox to appear as a selectable engine during `npx bmad-method install`**,
So that **I can choose Roblox as my game development platform and have it propagate to all workflows**.

**Acceptance Criteria:**

**Given** a user runs `npx bmad-method install` with BMGD module
**When** they reach the `primary_platform` multi-select prompt
**Then** "Roblox" appears as a selectable option alongside Unity, Unreal, Godot, and Custom/Other
**And** the `default` array includes `"roblox"` so it is pre-selected by default
**And** selecting Roblox stores `"roblox"` in the resolved config for downstream workflows
**And** the YAML passes `npm run lint`

### Story 1.2: Create Roblox Engine Knowledge File

As a **game architect using the BMGD architecture workflow**,
I want **a comprehensive Roblox engine knowledge file loaded when Roblox is selected**,
So that **I receive expert-level Roblox architecture guidance including Instance-Service model, Luau best practices, security patterns, and platform caveats**.

**Acceptance Criteria:**

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

### Story 1.3: Add Roblox MCP Server Entries

As a **developer setting up AI tooling for a Roblox project**,
I want **the architecture workflow to recommend Roblox-specific MCP servers**,
So that **I can integrate Claude Code or other AI tools directly with Roblox Studio**.

**Acceptance Criteria:**

**Given** `engine-mcps.yaml` is updated with a `roblox:` engine block
**When** an agent recommends MCP servers for a Roblox project
**Then** two MCP entries are available:
1. `roblox_builtin_mcp` — Roblox Studio Built-in MCP Server (primary): all 12 schema fields populated, capabilities include code execution/model insertion/play mode/3D mesh generation, install_type notes built-in with Studio 2026+, license "Proprietary (Roblox)"
2. `roblox_community_mcp` — boshyxd/robloxstudio-mcp (alternative): all 12 schema fields populated, 39+ tools, Creator Store plugin install, license MIT

**And** `recommendation_rules.multiple_options` includes `roblox_default: "roblox_builtin_mcp"` and `roblox_alternative: "roblox_community_mcp"`
**And** the YAML passes `npm run lint`

## Epic 2: Roblox Decision Catalog & Architecture Patterns

Provide intelligent Roblox-specific technology decisions across all decision categories with curated stacks and starter templates.

### Story 2.1: Add Roblox Options to Decision Catalog Categories

As a **game architect making technology decisions for a Roblox project**,
I want **Roblox-specific options available in every decision category**,
So that **the architecture workflow can map my choices to Roblox technologies with accurate `pairs_with` relationships**.

**Acceptance Criteria:**

**Given** `decision-catalog.yaml` is updated with Roblox entries
**When** the architecture workflow processes decisions for a Roblox project
**Then** all 12 existing categories have Roblox-specific options:
1. `game_engine`: 1 entry (roblox — Luau, Rojo, Wally, pesde, Knit)
2. `rendering_pipeline`: 3 entries (roblox_future_lighting, roblox_shadowmap, roblox_compatibility)
3. `physics_system`: 4 entries (roblox_constraints, roblox_humanoid, roblox_avatar_abilities, roblox_custom_controller)
4. `audio_system`: 2 entries (roblox_audio_api, roblox_soundservice)
5. `networking`: 3 entries (roblox_remotes, knit_networking, bytenet)
6. `input_system`: 3 entries (roblox_contextactionservice, roblox_userinputservice, roblox_proximityprompts)
7. `ai_system`: 3 entries (roblox_pathfindingservice, simplepath, state_machines_luau)
8. `save_persistence`: 5 entries (profilestore, profileservice, lapis, datastoreservice_native, memorystoreservice)
9. `ui_framework`: 3 entries (roblox_native_gui, fusion, react_lua)
10. `asset_pipeline`: 2 entries (roblox_rojo_wally, roblox_studio_native)
11. `deployment_platform`: 1 entry (roblox_crossplatform)

**And** all `pairs_with` arrays reflect verified current information (ProfileStore not ProfileService as primary, ByteNet not BridgeNet2, React-Lua not Roact, Audio API exited beta, AvatarAbilities included)
**And** option keys follow existing naming conventions (`engine_descriptor` format)
**And** the YAML passes `npm run lint`

### Story 2.2: Add Roblox Common Stacks and Starter Templates

As a **game architect seeking a recommended technology stack for Roblox**,
I want **curated default stacks and starter templates for Roblox projects**,
So that **I can quickly adopt a proven combination of technologies instead of choosing each one individually**.

**Acceptance Criteria:**

**Given** `decision-catalog.yaml` is updated with Roblox stacks and templates
**When** the architecture workflow suggests stacks for a Roblox project
**Then** two common stacks are available:
1. `roblox_standard` — "Roblox Experience (Standard)": Rojo+Wally+Git, Future Lighting, ProfileStore, Fusion/Native GUI, RemoteEvent+UnreliableRemoteEvent, ContextActionService, cross-platform. Good for multiplayer experiences, tycoons, simulators, RPGs
2. `roblox_simple` — "Roblox Simple Game": Studio-only, ShadowMap, DataStoreService native, SoundService, Native ScreenGui. Good for simple games, game jams, learning

**And** two starter templates are available:
1. `roblox_standard` — "Roblox Experience (Rojo)": command_search for Rojo init + Wally init, good_for multiplayer/tycoons/RPGs
2. `roblox_studio_only` — "Roblox Studio Only": command_search for Studio New Place, good_for simple games/game jams/learning

**And** Knit is NOT in default stack components (unmaintained, only as option)
**And** the YAML passes `npm run lint`

### Story 2.3: Add Roblox Architecture Patterns

As a **game architect designing a Roblox experience**,
I want **Roblox-specific architecture patterns that trigger on relevant keywords**,
So that **the workflow automatically identifies required decisions and suggests an appropriate technology stack when I describe a Roblox game**.

**Acceptance Criteria:**

**Given** `architecture-patterns.yaml` is updated with Roblox patterns
**When** a user's project description matches Roblox-related triggers
**Then** two requirement patterns are available:
1. `roblox_experience` — triggers: ["roblox", "roblox experience", "roblox game", "luau", "roblox studio"]; decisions_needed: [game_engine, rendering_pipeline, networking, save_persistence, ui_framework, input_system]; suggested_stack includes Rojo+Wally, Future Lighting, ProfileStore, RemoteEvent+UnreliableRemoteEvent, Native GUI or Fusion
2. `roblox_multiplayer` — triggers: ["roblox multiplayer", "roblox pvp", "roblox co-op", "roblox competitive", "roblox server"]; decisions_needed: [networking, save_persistence, ai_system, input_system]; suggested_stack includes server-side validation, ProfileStore with session locking, MemoryStoreService, MessagingService

**And** suggested_stack entries use ProfileStore (not ProfileService) and omit Knit from defaults
**And** the YAML passes `npm run lint`

## Epic 3: Roblox Testing & QA Support

Enable the QA agent to provide Roblox-specific testing strategies including TestEZ, MCP-based testing, and CI/CD pipelines.

### Story 3.1: Create Roblox Testing Knowledge and Register in QA Index

As a **QA engineer using the BMGD testing workflow for a Roblox project**,
I want **Roblox-specific testing knowledge loaded by the QA agent**,
So that **I receive expert guidance on TestEZ, Studio play mode testing, MCP-based automation, DataStore mocking, and CI/CD pipelines for Roblox**.

**Acceptance Criteria:**

**Given** `roblox-testing.md` is created at `src/gametest/knowledge/`
**When** the QA agent loads testing knowledge for a Roblox project
**Then** the file contains all 11 sections matching the testing template structure:
1. Overview (TestEZ as primary framework, Studio play mode, MCP-based testing, Lune alternative)
2. Test Framework Setup (Wally installation of TestEZ v0.4.1, Rojo-based test directory structure, test runner bootstrap script)
3. Unit Tests (TestEZ `describe`/`it`/`expect` syntax, parameterized tests, ModuleScript testing — with Luau code examples for damage calculation, weapon config)
4. Integration Tests (service interaction mocking, MockDataStoreService for DataStore testing, RemoteEvent testing via local server, MCP `run_script_in_play_mode` automation)
5. Studio Testing Tools (Play F5/Play Here F6/Run F8, local server 1-8 players, device emulation, Party Simulator beta)
6. Test Utilities (custom assertion helpers: nearlyEqual, withinRange, hasComponent; test data factories: createMockPlayer, createMockWeapon)
7. CI Integration (Rokit/Foreman toolchain setup, Selene lint + StyLua format, Rojo build, GitHub Actions workflow YAML, Lune as modern headless alternative to Lemur)
8. Performance Testing (MicroProfiler Ctrl+F6, frame budget 17ms/60FPS, benchmark utility function)
9. Best Practices (6 DOs including test shared modules in isolation, mock services; 5 DON'Ts including don't test engine built-ins, don't use deprecated `wait()`)
10. Troubleshooting (6-row table: tests not found/require fails/DataStore errors/tests hang/inconsistent results/MCP fails)
11. End-to-End Testing (cross-reference to `knowledge/e2e-testing.md`, 7-item Roblox-specific E2E checklist including MCP automation)

**And** `qa-index.csv` has a new row: `roblox-testing,Roblox TestEZ Testing,"Roblox testing with TestEZ, Studio play mode, MCP automation, and CI pipelines","roblox,testez,unit-tests,luau",knowledge/roblox-testing.md`
**And** the CSV maintains consistent 5-column format with existing rows
**And** code examples use Luau with game-domain relevant tests (damage, inventory, player data)
**And** the markdown file passes `npm run lint:md`

## Epic 4: Agent Awareness & Developer Onboarding

Update all agent identities to include Roblox expertise and provide a clear setup guide for new Roblox projects.

### Story 4.1: Update Agent SKILL.md Files with Roblox Expertise

As a **BMGD user interacting with AI agents on a Roblox project**,
I want **the Game Developer, Game QA, and Solo Dev agents to identify as Roblox-capable**,
So that **I trust their Roblox-specific guidance and know they have relevant engine expertise**.

**Acceptance Criteria:**

**Given** 3 agent SKILL.md files are updated
**When** an agent introduces itself or describes its expertise
**Then** the following changes are applied:

1. `gds-agent-game-dev/SKILL.md` (Game Developer — Link Freeman):
   - Line 10 (Overview): changed from "Unity, Unreal, and custom engines" to "Unity, Unreal, Godot, Roblox, and custom engines"
   - Line 14 (Identity): changed from "Unity, Unreal, and custom engines" to "Unity, Unreal, Godot, Roblox, and custom engines"

2. `gds-agent-game-qa/SKILL.md` (Game QA — GLaDOS):
   - Line 10 (Overview): changed from "Unity, Unreal, and Godot" to "Unity, Unreal, Godot, and Roblox"
   - Line 14 (Identity): changed from "Unity, Unreal, and Godot" to "Unity, Unreal, Godot, and Roblox"
   - Line 33 (Critical Actions): changed from "Unity Test Framework, Unreal Automation, or Godot GUT" to "Unity Test Framework, Unreal Automation, Godot GUT, or Roblox TestEZ"

3. `gds-agent-game-solo-dev/SKILL.md` (Solo Dev — Indie):
   - Line 14 (Identity): changed from "Unity, Unreal, and Godot" to "Unity, Unreal, Godot, and Roblox"

**And** no other content in the SKILL.md files is changed
**And** all markdown files pass `npm run lint:md`

### Story 4.2: Create Roblox Setup Guide

As a **developer starting a new Roblox project with BMGD**,
I want **a step-by-step setup guide for Roblox**,
So that **I can go from Roblox Studio installation to my first BMGD sprint with clear instructions**.

**Acceptance Criteria:**

**Given** `docs/how-to/setup-roblox.md` is created following the Diataxis how-to template
**When** a user reads the guide
**Then** the file contains all standard sections matching `setup-unity.md`/`setup-godot.md`:

1. Frontmatter (title: "Set up a Roblox project with BMGD", description)
2. When to Use This Guide (3 bullets)
3. When to Skip This (3-4 bullets with cross-references)
4. Prerequisites (BMad installed, Roblox Studio installed, basic Luau familiarity, game concept, optionally VS Code with Rojo)
5. Step 1: Create your Roblox project (two paths: Studio-only OR Professional with Rokit → Rojo → Wally)
6. Step 2: Generate project context (`/bmgd-generate-project-context`)
7. Step 3: Run brainstorming workflow
8. Step 4: Create Game Brief
9. Step 5: Design your game (GDD)
10. Step 6: Create technical architecture (mentions Roblox-specific output)
11. Step 7: Plan first sprint
12. Step 8: Start implementing (mentions Luau)
13. What You Get (standard table)
14. Roblox-Specific Considerations:
    - Project Structure (Rojo-based directory tree: src/server, src/client, src/shared, Packages)
    - Key Patterns (client-server split, ModuleScript organization, replication)
    - Performance Budgets (60 FPS, mobile-first, StreamingEnabled)
    - Testing Setup (TestEZ via Wally, Selene + StyLua)
    - MCP Setup (built-in MCP recommended, community MCP as alternative)
15. Tips (3 blockquote tips)
16. Common Mistakes (4-row table)
17. Next Steps (4 links)

**And** Rokit is mentioned as the first install step for the professional workflow path
**And** the file passes `npm run lint:md`

### Story 4.3: Update Documentation Site Index with Roblox

As a **BMGD user browsing the documentation site**,
I want **Roblox listed as a supported engine with a link to its setup guide**,
So that **I can discover Roblox support and navigate to the setup instructions**.

**Acceptance Criteria:**

**Given** `docs/index.md` is updated
**When** a user views the documentation homepage
**Then** Roblox appears in the "Supported Engines" section:
- `**[Roblox](https://www.roblox.com/create)** — Luau scripting, massive multiplayer platform` (or similar, matching the style of existing engine entries)

**And** the Quick Start table includes a row: `[Set up Roblox](how-to/setup-roblox.md)` | `Configure Roblox for BMGD`
**And** the file passes `npm run lint:md`
