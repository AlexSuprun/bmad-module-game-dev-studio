# Story 2.1: Add Roblox Options to Decision Catalog Categories

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **game architect making technology decisions for a Roblox project**,
I want **Roblox-specific options available in every decision category**,
so that **the architecture workflow can map my choices to Roblox technologies with accurate `pairs_with` relationships**.

## Acceptance Criteria

1. **Given** `decision-catalog.yaml` is updated with Roblox entries, **When** the architecture workflow processes decisions for a Roblox project, **Then** all 11 existing categories have Roblox-specific options:
   - `game_engine`: 1 entry (`roblox`)
   - `rendering_pipeline`: 3 entries (`roblox_future_lighting`, `roblox_shadowmap`, `roblox_compatibility`)
   - `physics_system`: 4 entries (`roblox_constraints`, `roblox_humanoid`, `roblox_avatar_abilities`, `roblox_custom_controller`)
   - `audio_system`: 2 entries (`roblox_audio_api`, `roblox_soundservice`)
   - `networking`: 3 entries (`roblox_remotes`, `knit_networking`, `bytenet`)
   - `input_system`: 3 entries (`roblox_contextactionservice`, `roblox_userinputservice`, `roblox_proximityprompts`)
   - `ai_system`: 3 entries (`roblox_pathfindingservice`, `simplepath`, `state_machines_luau`)
   - `save_persistence`: 5 entries (`profilestore`, `profileservice`, `lapis`, `datastoreservice_native`, `memorystoreservice`)
   - `ui_framework`: 3 entries (`roblox_native_gui`, `fusion`, `react_lua`)
   - `asset_pipeline`: 2 entries (`roblox_rojo_wally`, `roblox_studio_native`)
   - `deployment_platform`: 1 entry (`roblox_crossplatform`)
2. All `pairs_with` arrays reflect verified current information (ProfileStore recommended over ProfileService, ByteNet not BridgeNet2, React-Lua not Roact, new Audio API exited beta, AvatarAbilities included)
3. Two common stacks are added: `roblox_standard` and `roblox_simple`
4. Two starter templates are added: `roblox_standard` and `roblox_studio_only`
5. `starter_selection_rules` includes a `roblox_experience` entry
6. Option keys follow existing naming conventions (`engine_descriptor` format)
7. Knit is NOT in default stack components (only as a `knit_networking` option)
8. The YAML passes `npm run lint`

## Tasks / Subtasks

- [x] Task 1: Add Roblox entries to all 11 `decision_categories` in `decision-catalog.yaml` (AC: #1, #2, #6)
  - [x] Add `roblox` entry to `game_engine` category
  - [x] Add 3 entries to `rendering_pipeline` category
  - [x] Add 4 entries to `physics_system` category
  - [x] Add 2 entries to `audio_system` category
  - [x] Add 3 entries to `networking` category
  - [x] Add 3 entries to `input_system` category
  - [x] Add 3 entries to `ai_system` category
  - [x] Add 5 entries to `save_persistence` category
  - [x] Add 3 entries to `ui_framework` category
  - [x] Add 2 entries to `asset_pipeline` category
  - [x] Add 1 entry to `deployment_platform` category
- [x] Task 2: Add 2 common stacks to `common_stacks` section (AC: #3, #7)
  - [x] Add `roblox_standard` stack
  - [x] Add `roblox_simple` stack
- [x] Task 3: Add 2 starter templates to `starter_templates` section (AC: #4)
  - [x] Add `roblox_standard` template
  - [x] Add `roblox_studio_only` template
- [x] Task 4: Add `roblox_experience` entry to `starter_selection_rules` (AC: #5)
- [x] Task 5: Verify `npm run lint` passes with zero errors (AC: #8)

## Dev Notes

### Target File

```
src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml
```

This is the **only file that needs modification**. No other files are touched in this story.

### Existing File Structure

```yaml
decision_categories:     # 11 categories (see full list below)
common_stacks:           # 9 existing stacks (add roblox entries at the end)
starter_templates:       # existing templates (add roblox entries at the end)
starter_selection_rules: # heuristics for recommending starters
```

**File header note (line 4):** `# All version/feature info MUST be verified via WebSearch during workflow`

### Category Schema (MUST follow exactly)

```yaml
decision_categories:
  <category_key>:
    triggers: [<string>, ...]
    importance: "<critical|high|medium>"
    affects: "<description>"
    options:
      <option_key>:
        pairs_with: [<string>, ...]
```

**pairs_with is a flat array of quoted strings** (no nested structure). Use inline format or block depending on length. Match the style of surrounding entries.

### All 11 Existing Categories (verified from file)

1. `game_engine` — 6 options (unity, unreal, godot, phaser, bevy, monogame)
2. `rendering_pipeline` — 7 options (unity_urp, unity_hdrp, unreal_nanite_lumen, unreal_forward, godot_vulkan, godot_compatibility, custom_2d)
3. `physics_system` — 5 options (engine_native, jolt, box2d, custom_controller, deterministic)
4. `audio_system` — 3 options (engine_native, fmod, wwise)
5. `networking` — 8 options (unity_netcode, photon, mirror, unreal_replication, eos, steam_networking, godot_multiplayer, custom_udp)
6. `input_system` — 5 options (unity_input_system, unreal_enhanced_input, godot_input_map, rewired, custom_buffer)
7. `ai_system` — 6 options (behavior_trees, state_machines, goap, utility_ai, navigation_mesh, astar_grid)
8. `save_persistence` — 5 options (player_prefs, serialized_json, sqlite, platform_cloud, custom_binary)
9. `ui_framework` — 5 options (unity_ui_toolkit, unity_ugui, unreal_umg, godot_control, imgui)
10. `asset_pipeline` — 4 options (unity_addressables, unity_resources, unreal_asset_manager, godot_resource_loader)
11. `deployment_platform` — 6 options (steam_pc, console, mobile_ios, mobile_android, web_html5, vr_headset)

**NOTE:** `animation_system`, `monetization`, and `localization` do NOT exist in the current file — do NOT create new categories (out of scope per NFR from epics).

### Exact YAML Entries to Add

#### Category 1: `game_engine` — append after `godot:` or at end of options

```yaml
      roblox:
        pairs_with: ["Luau", "Roblox Studio", "Rojo", "Wally", "pesde", "Knit", "Roblox MCP Server"]
```

#### Category 2: `rendering_pipeline` — append after `custom_2d:` or at end of options

```yaml
      roblox_future_lighting:
        pairs_with: ["Roblox", "Future lighting", "PBR materials", "SurfaceAppearance", "per-pixel lighting", "high-end visuals"]
      roblox_shadowmap:
        pairs_with: ["Roblox", "ShadowMap lighting", "shadow-mapped sun", "voxelized local lights", "mid-range hardware"]
      roblox_compatibility:
        pairs_with: ["Roblox", "Compatibility lighting", "Voxel lighting", "low-end devices", "legacy content", "mobile-friendly"]
```

#### Category 3: `physics_system` — append at end of options

```yaml
      roblox_constraints:
        pairs_with: ["Roblox", "AlignPosition", "AlignOrientation", "BallSocketConstraint", "HingeConstraint", "SpringConstraint", "ControllerManager"]
      roblox_humanoid:
        pairs_with: ["Roblox", "Humanoid", "character movement", "WalkSpeed", "JumpPower", "HumanoidStateType"]
      roblox_avatar_abilities:
        pairs_with: ["Roblox", "AvatarAbilities", "Luau-based controller", "AbilityManager", "Crouch", "Sprint", "Strafe", "R15"]
      roblox_custom_controller:
        pairs_with: ["Roblox", "raycasting", "CFrame manipulation", "custom movement", "physics-free character"]
```

**Key note on `roblox_avatar_abilities`:** AvatarAbilities Library launched Studio Beta March 18, 2026. It replaces the Humanoid "black box" with a Luau-based AbilityManager. Default abilities: Crouch, Strafe, Sprint. Currently R15 only. This is the future direction for character controllers.

#### Category 4: `audio_system` — append at end of options

```yaml
      roblox_audio_api:
        pairs_with: ["Roblox", "AudioPlayer", "AudioEmitter", "AudioListener", "AudioDeviceOutput", "Wire-based routing", "spatial audio", "AudioLimiter", "AngleAttenuation"]
      roblox_soundservice:
        pairs_with: ["Roblox", "SoundService", "Sound", "SoundGroup", "simple audio", "legacy API"]
```

**Key note:** New Audio API has **exited beta** (no longer experimental). Wire-based routing: AudioPlayer → Wire → AudioEmitter/AudioDeviceOutput. AudioDeviceOutput is required for audio to be heard. Recommend `roblox_audio_api` for new projects; `roblox_soundservice` is legacy.

#### Category 5: `networking` — append at end of options

```yaml
      roblox_remotes:
        pairs_with: ["Roblox", "RemoteEvent", "RemoteFunction", "UnreliableRemoteEvent", "server-authoritative"]
      knit_networking:
        pairs_with: ["Roblox", "Knit", "Services", "Controllers", "automatic remote creation", "maintenance-only"]
      bytenet:
        pairs_with: ["Roblox", "ByteNet", "buffer serialization", "strict Luau", "optimized networking", "type-safe"]
```

**Critical anti-patterns:**
- Do NOT use `bridgenet2` — it is **archived and no longer supported**
- `knit_networking` note: Knit receives no new updates (maintenance-only mode)
- `bytenet` is created by ffrostfall (same developer as BridgeNet/BridgeNet2) using Luau buffer serialization

#### Category 6: `input_system` — append at end of options

```yaml
      roblox_contextactionservice:
        pairs_with: ["Roblox", "ContextActionService", "action binding", "priority-based", "mobile buttons", "gamepad", "contextual actions"]
      roblox_userinputservice:
        pairs_with: ["Roblox", "UserInputService", "raw input", "InputBegan", "InputEnded", "device detection", "mouse/keyboard"]
      roblox_proximityprompts:
        pairs_with: ["Roblox", "ProximityPrompt", "interaction system", "3D world interaction", "gamepad ButtonX"]
```

#### Category 7: `ai_system` — append at end of options

```yaml
      roblox_pathfindingservice:
        pairs_with: ["Roblox", "PathfindingService", "NavigationMesh", "PathWaypoints", "agent parameters", "pathfinding modifiers"]
      simplepath:
        pairs_with: ["Roblox", "SimplePath", "wrapper library", "NPC movement", "repetitive pathfinding", "humanoid and non-humanoid"]
      state_machines_luau:
        pairs_with: ["Roblox", "Luau state machines", "enemy AI", "NPC behavior", "finite state machines"]
```

#### Category 8: `save_persistence` — append at end of options

```yaml
      profilestore:
        pairs_with: ["Roblox", "ProfileStore", "successor to ProfileService", "session locking", "recommended for new projects"]
      profileservice:
        pairs_with: ["Roblox", "ProfileService", "session locking", "data loss prevention", "player profiles", "stable but unmaintained"]
      lapis:
        pairs_with: ["Roblox", "Lapis", "DataStore wrapper", "promise-based API", "immutability", "save batching", "retries"]
      datastoreservice_native:
        pairs_with: ["Roblox", "DataStoreService", "GetAsync", "SetAsync", "UpdateAsync", "simple data", "no wrapper needed"]
      memorystoreservice:
        pairs_with: ["Roblox", "MemoryStoreService", "ephemeral data", "matchmaking queues", "leaderboards", "sorted maps"]
```

**Critical ordering:** `profilestore` MUST come before `profileservice`. ProfileStore is the actively maintained successor by the same author (loleris/MadStudioRoblox). ProfileService is stable but unmaintained — it is listed only for awareness/legacy projects.

#### Category 9: `ui_framework` — append at end of options

```yaml
      roblox_native_gui:
        pairs_with: ["Roblox", "ScreenGui", "Frame", "TextLabel", "UIListLayout", "UIGridLayout", "StarterGui", "no dependencies"]
      fusion:
        pairs_with: ["Roblox", "Fusion 0.3", "reactive UI", "declarative", "state management", "contextual values", "memory management"]
      react_lua:
        pairs_with: ["Roblox", "React-Lua", "functional components", "hooks", "virtual DOM", "Roblox-maintained", "React 17 based"]
```

**Critical anti-patterns:**
- Do NOT use `roact` — Roact is **deprecated**; React-Lua (react-luau) is its replacement
- Fusion version is `0.3` (released August 2024) — features contextual values and improved memory management

#### Category 10: `asset_pipeline` — append at end of options

```yaml
      roblox_rojo_wally:
        pairs_with: ["Roblox", "Rojo", "Wally", "pesde", "Git", "VS Code", "filesystem sync", "CI/CD", "professional workflow"]
      roblox_studio_native:
        pairs_with: ["Roblox", "Creator Store", "Toolbox", "Packages", "Studio-only workflow", "rapid prototyping"]
```

#### Category 11: `deployment_platform` — append at end of options

```yaml
      roblox_crossplatform:
        pairs_with: ["Roblox", "PC", "Mac", "iOS", "Android", "Xbox", "PlayStation 4/5", "Meta Quest", "Samsung Galaxy Store", "Xbox Ally", "auto-scaling UI", "StreamingEnabled"]
```

**Platforms note (2025-2026):** PS4/PS5 added Oct 2023, Meta Quest supported (Quest 2/Pro/3), Samsung Galaxy Store added Oct 2025, Xbox Ally handhelds added 2025-2026. Roblox handles all hosting — no server management.

### Common Stacks to Add

Append these at the **end** of the `common_stacks` section:

```yaml
  roblox_standard:
    name: "Roblox Experience (Standard)"
    components:
      - "Roblox Engine"
      - "Luau"
      - "Rojo + Wally/pesde + Git"
      - "Future Lighting"
      - "Constraint Physics + AvatarAbilities (beta)"
      - "New Audio API (AudioPlayer + Wire routing)"
      - "RemoteEvent + UnreliableRemoteEvent"
      - "ContextActionService"
      - "PathfindingService + SimplePath"
      - "ProfileStore"
      - "Native ScreenGui or Fusion 0.3"
      - "Cross-platform"
    good_for: "Roblox multiplayer experiences, obbies, tycoons, simulators, RPGs"

  roblox_simple:
    name: "Roblox Simple Game"
    components:
      - "Roblox Engine"
      - "Luau"
      - "Studio-only workflow"
      - "ShadowMap Lighting"
      - "Native Humanoid physics"
      - "SoundService (legacy audio)"
      - "RemoteEvent"
      - "ContextActionService"
      - "DataStoreService (native)"
      - "Native ScreenGui"
      - "Creator Store assets"
    good_for: "Simple games, game jams, learning, obbies, showcases"
```

**Anti-pattern:** Knit is NOT in default stack components. Knit receives no new updates (maintenance-only). It may appear as `knit_networking` option but not in default stacks.

### Starter Templates to Add

Append these at the **end** of the `starter_templates` section:

```yaml
  roblox_standard:
    name: "Roblox Experience (Rojo)"
    command_search: "Rojo init + Wally init in empty project directory"
    decisions_provided: ["Roblox engine", "Luau language", "Rojo filesystem sync", "Wally packages", "client-server architecture"]
    good_for: ["Roblox multiplayer", "tycoons", "simulators", "RPGs", "professional workflow"]

  roblox_studio_only:
    name: "Roblox Studio Only"
    command_search: "Roblox Studio → New Place"
    decisions_provided: ["Roblox engine", "Luau language", "Studio-only workflow", "built-in tools"]
    good_for: ["Simple games", "game jams", "learning", "obbies", "showcases"]
```

### Starter Selection Rules to Add

Append this entry to the `starter_selection_rules` section:

```yaml
  roblox_experience:
    recommended: ["roblox_standard", "roblox_studio_only"]
    considerations: "Professional team? → Rojo + Wally. Solo/learning? → Studio only. Complex game? → Standard. Game jam? → Studio only"
```

### YAML Lint Compliance

This project uses `npm run lint` for YAML validation (NOT `npm run lint:md` which is markdown-only).

Key YAML conventions (match existing entries):
- `pairs_with` uses `["item1", "item2"]` inline array format for short arrays
- Multi-item arrays in `components` use `- "item"` block format
- Indentation: 2 spaces throughout
- No trailing spaces
- Consistent double quotes for all string values
- Option keys are `snake_case`

Run `npm run lint` after all changes. Zero errors required.

### Critical Anti-Patterns to Avoid

| DO NOT USE | USE INSTEAD | REASON |
|---|---|---|
| `bridgenet2` | `bytenet` | BridgeNet2 is archived/unsupported |
| `ProfileService` as primary | `ProfileStore` as primary | ProfileService is unmaintained; ProfileStore is successor |
| `roact` | `react_lua` | Roact is deprecated |
| Knit in default stacks | Rojo + Wally/pesde | Knit is maintenance-only |
| `Fusion 0.2` or unversioned | `Fusion 0.3` | Version matters; 0.3 has contextual values |
| `roblox_audio_api` as "beta" | Remove "beta" label | New Audio API has exited beta |
| `animation_system` category | — | Category does not exist — out of scope |
| `monetization` category | — | Category does not exist — out of scope |
| `localization` category | — | Category does not exist — out of scope |

### Project Structure Notes

- **Single file change**: `src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml`
- This file is consumed by the architecture workflow for technology decision mapping
- No cascade effects — other YAML files do not reference `decision-catalog.yaml` entries directly
- No new directories or files required
- Expected line additions: approximately 110-130 lines (30 option entries + 2 stacks + 2 templates + 1 rule)

### Previous Story Intelligence (Stories 1.1–1.3)

- `npm run lint` (YAML) and `npm run lint:md` (markdown) are **separate commands** — use `npm run lint` for this story
- Story 1.1 modified `src/module.yaml` — already done
- Story 1.2 created `docs/roblox-engine.md` knowledge file — already done
- Story 1.3 modified `src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml` — complete, in review
- Pattern from 1.3: pure YAML config addition, no test files, no markdown files needed
- The worktree is on `feat/roblox-support` branch

### Git Context

Recent commits:
- `1d02793` — `docs/roblox-engine.md` ECS pattern added (Story 1.2)
- `717136a` — `docs/roblox-engine.md` knowledge file created (Story 1.2)
- `a7c86e0` — `src/module.yaml` updated with roblox option (Story 1.1)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.1]
- [Source: _bmad-output/planning-artifacts/research/roblox-decision-catalog-research.md]
- [Source: src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml] — existing schema to match

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

### Completion Notes List

- Added 30 Roblox option entries across all 11 decision categories in `decision-catalog.yaml`
- Added `roblox_standard` and `roblox_simple` common stacks (Knit excluded per AC #7)
- Added `roblox_standard` and `roblox_studio_only` starter templates
- Added `roblox_experience` entry to `starter_selection_rules`
- `profilestore` placed before `profileservice` per story ordering requirement
- `npm run lint` passes with zero errors

### File List

- `src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml`

## Review Findings

- [x] [Review][Decision] Knit in `game_engine.roblox.pairs_with` — Removed "Knit" from engine pairs_with per AC7 intent; Knit appears only as `knit_networking` option.

- [x] [Review][Patch] "Future lighting" capitalisation inconsistency — fixed to "Future Lighting" [decision-catalog.yaml: roblox_future_lighting.pairs_with]

- [x] [Review][Patch] `roblox_crossplatform` includes "Xbox Ally" — removed "Xbox Ally" from pairs_with [decision-catalog.yaml: roblox_crossplatform.pairs_with]

- [x] [Review][Defer] `wwise` entry has incorrect pairs_with (Roblox/SoundService data) and `roblox_soundservice` is a character-for-character duplicate of it — deferred, pre-existing bug not caused by this diff [decision-catalog.yaml: audio_system.wwise]

- [x] [Review][Defer] `godot_resource_loader` lists "Roblox" in pairs_with, now actively reachable by Roblox engine matching for the first time — deferred, pre-existing [decision-catalog.yaml: asset_pipeline.godot_resource_loader]

- [x] [Review][Defer] `roblox_avatar_abilities` category entry has no beta/stability marker (common stack says "(beta)" but category option does not) — schema lacks a status field; requires schema design decision — deferred [decision-catalog.yaml: physics_system.roblox_avatar_abilities]

- [x] [Review][Defer] `roblox_standard` key name shared across common_stacks and starter_templates — spec specifies both keys as `roblox_standard`; no fix possible within story scope — deferred [decision-catalog.yaml]

- [x] [Review][Defer] `workflow.md` knowledge_fragments map omits `roblox` engine registration — out of scope for this story; already tracked as deferred from Story 1.3 review — deferred [gds-game-architecture/workflow.md]
