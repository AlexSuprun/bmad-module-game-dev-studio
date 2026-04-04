# Roblox Decision Catalog & Architecture Patterns Research

**Date:** 2026-04-02
**Author:** Alex Suprun
**Research Type:** Technical Deep Dive
**Purpose:** Verify and enhance Roblox decision catalog entries and architecture patterns for BMGD integration

---

## Decision Catalog Schema Analysis

### File: `decision-catalog.yaml`

**Top-level structure:**

```yaml
decision_categories:    # 14 categories of architectural decisions
common_stacks:          # 9 opinionated stack combinations
starter_templates:      # 10 engine-specific project templates
starter_selection_rules: # Heuristics for template selection by game type
```

**Category schema (each of 14 categories):**

```yaml
decision_categories:
  <category_key>:
    triggers: [<string>, ...]           # Keywords that activate this category
    importance: "<critical|high|medium>" # Priority level
    affects: "<description>"            # What epics/areas are impacted
    options:
      <option_key>:                     # snake_case, often prefixed with engine name
        pairs_with: [<string>, ...]     # Related technologies, engines, patterns
```

**Naming conventions for option keys:**
- Engine-specific entries use `engine_descriptor` format: `unity_urp`, `unity_hdrp`, `unreal_nanite_lumen`, `godot_vulkan`, `godot_compatibility`
- Generic entries use descriptive names: `engine_native`, `custom_controller`, `deterministic`, `behavior_trees`
- Third-party tools use their own names: `fmod`, `wwise`, `photon`, `mirror`, `rewired`
- `pairs_with` arrays contain a mix of engine names, technology names, use cases, and descriptive phrases

**All 14 category names:**
1. `game_engine` (6 options: unity, unreal, godot, phaser, bevy, monogame)
2. `rendering_pipeline` (7 options)
3. `physics_system` (5 options)
4. `audio_system` (3 options)
5. `networking` (8 options)
6. `input_system` (5 options)
7. `ai_system` (6 options)
8. `animation_system` -- NOT present in current file (see note below)
9. `save_persistence` (5 options)
10. `ui_framework` (5 options)
11. `asset_pipeline` (4 options)
12. `deployment_platform` (6 options)

**Important finding:** The current `decision-catalog.yaml` only has **12 categories**, not 14. The research doc references 14 categories including `animation_system`, `monetization`, and `localization`, but these do **not** exist in the current file. The file has 12 `decision_categories` entries. The `monetization` topic appears only under `integration_requirements` in `architecture-patterns.yaml`. Animation and localization are not present as decision categories at all.

**Entry counts per category (existing engines):**
| Category | Options Count | Engine-specific entries |
|----------|--------------|----------------------|
| game_engine | 6 | unity, unreal, godot, phaser, bevy, monogame |
| rendering_pipeline | 7 | unity_urp, unity_hdrp, unreal_nanite_lumen, unreal_forward, godot_vulkan, godot_compatibility, custom_2d |
| physics_system | 5 | engine_native, jolt, box2d, custom_controller, deterministic |
| audio_system | 3 | engine_native, fmod, wwise |
| networking | 8 | unity_netcode, photon, mirror, unreal_replication, eos, steam_networking, godot_multiplayer, custom_udp |
| input_system | 5 | unity_input_system, unreal_enhanced_input, godot_input_map, rewired, custom_buffer |
| ai_system | 6 | behavior_trees, state_machines, goap, utility_ai, navigation_mesh, astar_grid |
| save_persistence | 5 | player_prefs, serialized_json, sqlite, platform_cloud, custom_binary |
| ui_framework | 5 | unity_ui_toolkit, unity_ugui, unreal_umg, godot_control, imgui |
| asset_pipeline | 4 | unity_addressables, unity_resources, unreal_asset_manager, godot_resource_loader |
| deployment_platform | 6 | steam_pc, console, mobile_ios, mobile_android, web_html5, vr_headset |

**Common stacks schema:**

```yaml
common_stacks:
  <stack_key>:
    name: "<Display Name>"
    components: [<string>, ...]
    good_for: "<description>"
```

**Starter templates schema:**

```yaml
starter_templates:
  <template_key>:
    name: "<Display Name>"
    command_search: "<how to create>"
    decisions_provided: [<string>, ...]
    good_for: [<string>, ...]
```

**Validation rules / comments:**
- Line 4: `# All version/feature info MUST be verified via WebSearch during workflow`
- File states it provides "RELATIONSHIPS and WORKFLOW LOGIC, not generic tech knowledge"
- No formal schema validation; relies on consistent YAML structure

---

## Architecture Patterns Schema Analysis

### File: `architecture-patterns.yaml`

**Top-level structure:**

```yaml
requirement_patterns:       # 13 game-type patterns
quality_attributes:         # 5 quality patterns
integration_requirements:   # 6 service integration categories
decision_rules:             # 5 heuristic rule sets
```

**Requirement pattern schema:**

```yaml
requirement_patterns:
  <pattern_key>:
    triggers:
      - "<keyword string>"        # List of trigger phrases
    decisions_needed:
      - <decision_key>            # snake_case decision identifiers
    suggested_stack:
      - "<recommendation string>" # Descriptive technology recommendations
```

**Quality attribute schema (different structure):**

```yaml
quality_attributes:
  <attribute_key>:
    triggers:
      - "<keyword>"
    architectural_needs:
      - <need_key>                # snake_case identifiers (no suggested_stack)
```

**Integration requirements schema:**

```yaml
integration_requirements:
  <category_key>:
    common_choices:
      - "<choice description>"
    considerations:
      - <consideration_key>
```

**Decision rules schema:**

```yaml
decision_rules:
  <rule_set_key>:
    if_requirements_include:
      - <condition>: "<recommendation>"
```

**Existing patterns (13 requirement patterns):**
1. `realtime_multiplayer` (7 triggers, 6 decisions, 4 stack items)
2. `turn_based_multiplayer` (6 triggers, 5 decisions, 4 stack items)
3. `physics_simulation` (7 triggers, 5 decisions, 4 stack items)
4. `procedural_generation` (8 triggers, 5 decisions, 4 stack items)
5. `rpg_systems` (9 triggers, 5 decisions, 4 stack items)
6. `platformer_mechanics` (6 triggers, 5 decisions, 4 stack items)
7. `strategy_rts` (8 triggers, 5 decisions, 4 stack items)
8. `puzzle_mechanics` (6 triggers, 5 decisions, 4 stack items)
9. `open_world` (7 triggers, 5 decisions, 4 stack items)
10. `narrative_driven` (7 triggers, 5 decisions, 4 stack items)
11. `mobile_casual` (7 triggers, 5 decisions, 4 stack items)
12. `vr_xr` (8 triggers, 5 decisions, 4 stack items)
13. `fighting_action` (7 triggers, 5 decisions, 4 stack items)
14. `racing_vehicle` (6 triggers, 5 decisions, 4 stack items)

**Engine-specific patterns:** None. All 14 patterns are engine-agnostic. They reference engine features in `suggested_stack` strings (e.g., "PhysX, Jolt, Godot Physics") but no pattern is engine-specific by key name.

**Default stack definitions (9 stacks in common_stacks):**
All follow the same `name`/`components`/`good_for` structure. Engine-specific by nature (e.g., `unity_3d_action`, `godot_2d`, `vr_experience`).

---

## Roblox Entries per Category

### Category 1: game_engine

**Existing format example:**
```yaml
godot:
  pairs_with: ["GDScript", "C#", "GDExtension", "Godot Physics", "GodotSteam"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox:
  pairs_with: ["Luau", "Roblox Studio", "Rojo", "Wally", "pesde", "Knit", "Roblox MCP Server"]
```

**Web research findings:**
- **Rojo** is at v7.5.1 (April 2025). Stable, with "syncback" feature for converting existing places to Rojo projects. Actively maintained.
- **Wally** is stable and widely used. Maintained by Uplift Games (Adopt Me studio). Requires Rust 1.80+ for source compilation.
- **pesde** is an emerging alternative to Wally -- a package manager for Luau supporting multiple runtimes (Roblox, Lune). Can use Wally registries as sources. Growing adoption in 2025-2026.
- **Knit** (by Sleitnick) is stable but **no longer receiving updates**. Battle-tested in production. Still widely used but developers should be aware it is in maintenance-only mode.
- Added `pesde` to pairs_with as it is gaining traction as a Wally alternative/complement.

**Sources:**
- [Rojo Releases](https://github.com/rojo-rbx/rojo/releases)
- [Wally GitHub](https://github.com/UpliftGames/wally)
- [Knit Framework](https://sleitnick.github.io/Knit/)
- [Knit reliability discussion](https://devforum.roblox.com/t/is-the-knit-framework-still-reliable/3386821)
- [pesde GitHub](https://github.com/pesde-pkg/pesde)
- [pesde Roblox docs](https://docs.pesde.dev/guides/roblox/)

---

### Category 2: rendering_pipeline

**Existing format example:**
```yaml
godot_vulkan:
  pairs_with: ["Godot", "Forward+", "mid-range hardware"]
godot_compatibility:
  pairs_with: ["Godot", "OpenGL", "mobile", "web export"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_future_lighting:
  pairs_with: ["Roblox", "Future lighting", "PBR materials", "SurfaceAppearance", "per-pixel lighting", "high-end visuals"]
roblox_shadowmap:
  pairs_with: ["Roblox", "ShadowMap lighting", "shadow-mapped sun", "voxelized local lights", "mid-range hardware"]
roblox_compatibility:
  pairs_with: ["Roblox", "Compatibility lighting", "Voxel lighting", "low-end devices", "legacy content", "mobile-friendly"]
```

**Web research findings:**
- Roblox has **4 lighting technologies**: Compatibility (legacy), Voxel, ShadowMap, and Future.
- **Future** combines shadow-mapping with per-pixel lighting from local light sources (PointLight, SpotLight, SurfaceLight) for real-world-accurate lighting behavior. This is the highest quality option.
- **ShadowMap** uses shadow mapping for sun shadows but local light sources remain voxelized. Good middle ground.
- **Voxel** uses CPU-based voxel simulation (4-stud resolution) for shadows from diffuse and outdoor ambient light.
- **Compatibility** is the legacy mode.
- There were DevForum reports (2025) about these lighting types being deprecated from the dropdown UI, though they remain functional via API.
- Updated `pairs_with` to be more technically specific about what each mode provides.

**Sources:**
- [Roblox Lighting Technology enum](https://create.roblox.com/docs/reference/engine/enums/Technology)
- [Future Is Bright comparison](https://roblox.github.io/future-is-bright/compare.html)
- [Lighting deprecation discussion](https://devforum.roblox.com/t/were-the-shadow-map-voxel-future-and-compability-lightning-types-deprecated/3516247)

---

### Category 3: physics_system

**Existing format example:**
```yaml
custom_controller:
  pairs_with: ["platformers", "raycasting", "kinematic bodies"]
```

**Proposed Roblox entries (verified/updated):**
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

**Web research findings:**
- **Major discovery: AvatarAbilities Library** (Studio Beta, March 18, 2026) -- Roblox is replacing the Humanoid "black box" with a Luau-based "AbilityManager" that works alongside C++ "ControllerManager". Default abilities include Crouch, Strafe, and Sprint. Features higher-fidelity movement physics with ground friction, linear acceleration, and momentum conservation.
- AvatarAbilities is opt-in, currently R15 only, and represents the future direction for character controllers on Roblox.
- The legacy Humanoid system remains fully supported but the AvatarAbilities library gives developers transparency and customization they never had before.
- **ControllerManager** is the C++ physics-based character controller that the new system builds on.
- Added `roblox_avatar_abilities` as a new option not in the original research doc. This is a significant addition.

**Sources:**
- [AvatarAbilities Library Beta announcement](https://devforum.roblox.com/t/studio-beta-avatarabilities-library-for-a-luau-based-character-controller/4403904)
- [Character Physics Controllers release](https://devforum.roblox.com/t/releasing-character-physics-controllers/2623426)
- [Roblox API updates 2025](https://robloxapi.github.io/ref/updates/2025.html)

---

### Category 4: audio_system

**Existing format example:**
```yaml
engine_native:
  pairs_with: ["Unity Audio", "Unreal MetaSounds", "Godot AudioStreamPlayer"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_audio_api:
  pairs_with: ["Roblox", "AudioPlayer", "AudioEmitter", "AudioListener", "AudioDeviceOutput", "Wire-based routing", "spatial audio", "AudioLimiter", "AngleAttenuation"]
roblox_soundservice:
  pairs_with: ["Roblox", "SoundService", "Sound", "SoundGroup", "simple audio", "legacy API"]
```

**Web research findings:**
- The **new Audio API has officially exited beta** and is now available in Studio. This is a significant status change.
- Core architecture is wire-based: AudioPlayer -> Wire -> AudioEmitter/AudioDeviceOutput. Audio must route to an AudioDeviceOutput to be heard.
- **Recent additions** (2025-2026): AngleAttenuation (directional audio, still beta), AudioLimiter, AudioPlayer:GetWaveformAsync(), AudioEcho.RampTime, SoundService.DefaultListenerLocation.
- SoundService remains functional as the legacy/simple approach but the new Audio API is the recommended path for new projects.
- Updated `pairs_with` to include `AudioDeviceOutput` (required routing endpoint), `AudioLimiter`, and `AngleAttenuation` as newly available features.

**Sources:**
- [New Audio API exits beta](https://devforum.roblox.com/t/roblox-audio-api-exits-beta-enhanced-sound-controls-now-available/3153454)
- [Audio API new features: directional audio, limiter](https://devforum.roblox.com/t/new-audio-api-features-directional-audio-audiolimiter-and-more/3282100)
- [Audio API deep dive tutorial](https://devforum.roblox.com/t/robloxs-new-audio-api-a-somewhat-deep-dive/3156011)

---

### Category 5: networking

**Existing format example:**
```yaml
godot_multiplayer:
  pairs_with: ["Godot", "MultiplayerAPI", "ENet", "WebSocket"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_remotes:
  pairs_with: ["Roblox", "RemoteEvent", "RemoteFunction", "UnreliableRemoteEvent", "server-authoritative"]
knit_networking:
  pairs_with: ["Roblox", "Knit", "Services", "Controllers", "automatic remote creation", "maintenance-only"]
bytenet:
  pairs_with: ["Roblox", "ByteNet", "buffer serialization", "strict Luau", "optimized networking", "type-safe"]
```

**Web research findings:**
- **BridgeNet2 has been archived** and is no longer supported. The original research doc listed it as an active option -- this is now incorrect.
- **ByteNet** is the successor to BridgeNet/BridgeNet2, created by the same developer (ffrostfall). It uses Luau buffer serialization instead of RemoteEvent identifier compression. ByteNet has not been updated since March 2024 but an improved version exists.
- UnreliableRemoteEvent is confirmed available and useful for ephemeral data (particle effects, sound triggers, position updates) that doesn't need reliability guarantees.
- **Knit networking** should note "maintenance-only" since Knit is no longer actively updated.
- **RbxUtil Net** (by Sleitnick) is another option -- a lightweight networking utility from the same developer as Knit.
- Replaced `bridgenet2` with `bytenet` in the proposed entries.

**Sources:**
- [UnreliableRemoteEvent docs](https://create.roblox.com/docs/reference/engine/classes/UnreliableRemoteEvent)
- [BridgeNet2 archived status](https://devforum.roblox.com/t/bridgenet2-v100-a-blazing-fast-networking-library-for-roblox/2189165/238)
- [ByteNet GitHub](https://github.com/ffrostfall/ByteNet)
- [ByteNet DevForum](https://devforum.roblox.com/t/bytenet-advanced-networking-library-w-buffer-serialization-strict-luau-absurd-optimization-and-rbxts-support-043/2733365)
- [RbxUtil Net](https://sleitnick.github.io/RbxUtil/api/Net/)

---

### Category 6: input_system

**Existing format example:**
```yaml
unity_input_system:
  pairs_with: ["Unity", "action maps", "rebinding", "multiple devices"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_contextactionservice:
  pairs_with: ["Roblox", "ContextActionService", "action binding", "priority-based", "mobile buttons", "gamepad", "contextual actions"]
roblox_userinputservice:
  pairs_with: ["Roblox", "UserInputService", "raw input", "InputBegan", "InputEnded", "device detection", "mouse/keyboard"]
roblox_proximityprompts:
  pairs_with: ["Roblox", "ProximityPrompt", "interaction system", "3D world interaction", "gamepad ButtonX"]
```

**Web research findings:**
- **ContextActionService** is recommended for most gameplay input -- supports priority-based binding, contextual actions, and automatic mobile button generation.
- **UserInputService** is better for raw input detection, device type checking, and global input monitoring.
- Community consensus: use ContextActionService for gameplay (actions that change by context) and UserInputService for detecting device type or monitoring raw input.
- **ProximityPrompt** natively supports gamepad (ButtonX default), keyboard, and touch. Good for object interaction in 3D world.
- No significant new input APIs discovered for 2025-2026. These three remain the primary options.

**Sources:**
- [ContextActionService vs UserInputService](https://devforum.roblox.com/t/contextactionservice-or-userinputservice/2827512)
- [ContextActionService API](https://robloxapi.github.io/ref/class/ContextActionService.html)
- [ProximityPrompt API](https://robloxapi.github.io/ref/class/ProximityPrompt.html)

---

### Category 7: ai_system

**Existing format example:**
```yaml
behavior_trees:
  pairs_with: ["Unity (Behavior Designer)", "Unreal (Behavior Trees)", "Godot (LimboAI)"]
navigation_mesh:
  pairs_with: ["Unity NavMesh", "Unreal NavMesh", "Godot NavigationServer", "3D pathfinding"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_pathfindingservice:
  pairs_with: ["Roblox", "PathfindingService", "NavigationMesh", "PathWaypoints", "agent parameters", "pathfinding modifiers"]
simplepath:
  pairs_with: ["Roblox", "SimplePath", "wrapper library", "NPC movement", "repetitive pathfinding", "humanoid and non-humanoid"]
state_machines_luau:
  pairs_with: ["Roblox", "Luau state machines", "enemy AI", "NPC behavior", "finite state machines"]
```

**Web research findings:**
- **PathfindingService** supports pathfinding modifiers for computing smarter paths across different materials, around defined regions, or through obstacles. Agent parameters allow customization per NPC type.
- **SimplePath** (by grayzcale) remains an active open-source module. Uses a "repetitive" pathfinding approach that recomputes paths continuously to handle dynamic obstacles. Supports both humanoid and non-humanoid agents.
- No major new AI/NPC frameworks discovered for Roblox in 2025-2026 beyond these established options.
- **Jecs** (ECS library) could be relevant for AI systems managing large numbers of entities -- can iterate 800,000 entities at 60fps. Worth noting as a supporting library for AI-heavy games.

**Sources:**
- [SimplePath module](https://devforum.roblox.com/t/simplepath-pathfinding-module/1196762)
- [SimplePath GitHub](https://github.com/grayzcale/simplepath)
- [PathfindingService API](https://robloxapi.github.io/ref/class/PathfindingService.html)
- [Jecs ECS library](https://github.com/Ukendio/jecs)

---

### Category 8: save_persistence

**Existing format example:**
```yaml
platform_cloud:
  pairs_with: ["Steam Cloud", "PSN", "Xbox Cloud", "iCloud", "Google Play"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
profileservice:
  pairs_with: ["Roblox", "ProfileService", "session locking", "data loss prevention", "player profiles", "stable but unmaintained"]
profilestore:
  pairs_with: ["Roblox", "ProfileStore", "successor to ProfileService", "session locking", "recommended for new projects"]
lapis:
  pairs_with: ["Roblox", "Lapis", "DataStore wrapper", "promise-based API", "immutability", "save batching", "retries"]
datastoreservice_native:
  pairs_with: ["Roblox", "DataStoreService", "GetAsync", "SetAsync", "UpdateAsync", "simple data", "no wrapper needed"]
memorystoreservice:
  pairs_with: ["Roblox", "MemoryStoreService", "ephemeral data", "matchmaking queues", "leaderboards", "sorted maps"]
```

**Web research findings:**
- **ProfileService** is stable but **no longer actively maintained**. The author recommends **ProfileStore** for new projects.
- **ProfileStore** is the official successor to ProfileService by the same author (loleris/MadStudioRoblox). Should be the primary recommendation for new Roblox projects.
- **Lapis** (by nezuo) is a DataStore abstraction library with promise-based API, immutability by default, save batching, and automatic retries. Has not yet been battle-tested in large production games per its documentation.
- DataStoreService native API remains viable for simple use cases.
- MemoryStoreService for ephemeral, server-side data (matchmaking, real-time leaderboards).
- Added `profilestore` as a new entry and marked `profileservice` as "stable but unmaintained".

**Sources:**
- [ProfileService documentation](https://madstudioroblox.github.io/ProfileService/)
- [Data storing in 2025 discussion](https://devforum.roblox.com/t/data-storing-in-2025/3425924)
- [Lapis GitHub](https://github.com/nezuo/lapis)
- [Lapis documentation](https://nezuo.github.io/lapis/)
- [Best datastore module discussion](https://devforum.roblox.com/t/what-is-the-best-datastore-module-to-use-currently/3598021)

---

### Category 9: ui_framework

**Existing format example:**
```yaml
godot_control:
  pairs_with: ["Godot", "Control nodes", "themes", "containers"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_native_gui:
  pairs_with: ["Roblox", "ScreenGui", "Frame", "TextLabel", "UIListLayout", "UIGridLayout", "StarterGui", "no dependencies"]
fusion:
  pairs_with: ["Roblox", "Fusion 0.3", "reactive UI", "declarative", "state management", "contextual values", "memory management"]
react_lua:
  pairs_with: ["Roblox", "React-Lua", "functional components", "hooks", "virtual DOM", "Roblox-maintained", "React 17 based"]
```

**Web research findings:**
- **Roact is deprecated.** React-Lua (react-luau) is its replacement, maintained by Roblox officially. Based on React 17. Offers functional components and hooks.
- **Fusion 0.3** released August 2024 by Daniel P. H. Fox (Elttob/dphfox). Introduces contextual values, improved memory management, and dependency traceability. Independent of Roblox (community library).
- Both Fusion and React-Lua are actively used. Fusion is lighter-weight and Roblox-native in design philosophy. React-Lua is familiar to web developers.
- Renamed entry from `roact_react_lua` to `react_lua` since Roact is fully deprecated.
- Updated Fusion version to 0.3 with its new features.

**Sources:**
- [Fusion GitHub](https://github.com/dphfox/Fusion)
- [Fusion 0.3 documentation](https://elttob.uk/Fusion/0.3/)
- [React-Lua GitHub](https://github.com/Roblox/react-luau)
- [Fusion vs React-Lua discussion](https://devforum.roblox.com/t/fusion-vs-react-lua-which-one-to-use-for-long-term-development/2781328)
- [How to: React + Roblox](https://devforum.roblox.com/t/how-to-react-roblox/2964543)

---

### Category 10: asset_pipeline

**Existing format example:**
```yaml
godot_resource_loader:
  pairs_with: ["Godot", "ResourceLoader", "threaded loading", "PCK files"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_rojo_wally:
  pairs_with: ["Roblox", "Rojo", "Wally", "pesde", "Git", "VS Code", "filesystem sync", "CI/CD", "professional workflow"]
roblox_studio_native:
  pairs_with: ["Roblox", "Creator Store", "Toolbox", "Packages", "Studio-only workflow", "rapid prototyping"]
```

**Web research findings:**
- **Professional workflow:** Rojo + Wally/pesde + Git + VS Code is the industry-standard for serious Roblox development. Top studios use this stack.
- **pesde** is gaining ground as a Wally alternative/complement. Supports Wally registries as sources, meaning gradual migration is possible.
- **Rojo v7.5.1** includes syncback feature for converting existing places to Rojo projects -- lowers the barrier to adopting professional workflow.
- **Creator Store** (formerly Toolbox) remains the primary in-Studio asset discovery and sharing mechanism.
- **2026 roadmap direction:** Roblox is focused on scaling quality with improved asset creation workflow and faster iteration tools.
- Renamed `roblox_packages_rojo` to `roblox_rojo_wally` for clarity. Added `pesde` and `CI/CD` to pairs_with.
- Renamed `roblox_toolbox` to `roblox_studio_native` to better match the pattern of other entries.

**Sources:**
- [Professional Roblox workflow guide](https://freerbxscripts.com/post/set-up-a-pro-development-workflow/)
- [Rojo introduction](https://rojo.space/docs/v7/)
- [CI/CD with Rojo + Wally + TestEZ](https://devforum.roblox.com/t/cicd-setup-with-rojo-wally-testez/3610450)
- [Roblox 2025-2026 roadmap recap](https://soren.com/en/news/roblox/2025-12-17-roblox-recaps-2025-creator-roadmap-previews-2026)
- [pesde Roblox guide](https://docs.pesde.dev/guides/roblox/)

---

### Category 11: deployment_platform

**Existing format example:**
```yaml
steam_pc:
  pairs_with: ["Steamworks SDK", "Steam Cloud", "Steam Input", "achievements"]
console:
  pairs_with: ["platform SDK (NDA)", "certification requirements", "platform-specific APIs"]
```

**Proposed Roblox entries (verified/updated):**
```yaml
roblox_crossplatform:
  pairs_with: ["Roblox", "PC", "Mac", "iOS", "Android", "Xbox", "PlayStation 4/5", "Meta Quest", "Samsung Galaxy Store", "Xbox Ally", "auto-scaling UI", "StreamingEnabled"]
```

**Web research findings:**
- **PlayStation:** Roblox launched on PS4 and PS5 in October 2023. No PSVR2 support.
- **Meta Quest:** Supported on Quest 2, Quest Pro, and Quest 3. Full cross-platform play with all other platforms.
- **New in 2025:** Samsung Galaxy Store availability (October 2025).
- **New in 2025-2026:** Xbox Ally and Xbox Ally X handheld support with gamepad and touch controls.
- **VR:** Supported on Oculus Rift, Meta Quest, HTC Vive, Valve Index.
- Roblox handles all deployment, hosting, and scaling automatically. Developers don't manage servers or build pipelines per-platform.
- Updated `pairs_with` to include Samsung Galaxy Store and Xbox Ally as newly supported platforms.

**Sources:**
- [Roblox on PS5, PS4, and Meta Quest](https://www.vgchartz.com/article/458396/roblox-headed-to-ps5-ps4-and-meta-quest/)
- [Meta Quest FAQ](https://en.help.roblox.com/hc/en-us/articles/17810433924628-Meta-Quest-FAQ)
- [Roblox new platforms announcement](https://about.roblox.com/newsroom/2025/10/roblox-everywhere-bringing-roblox-two-new-platforms)
- [Roblox VR setup](https://en.help.roblox.com/hc/en-us/articles/208260046-Setting-up-VR-for-Roblox)

---

### Category 12: animation_system

**NOTE:** This category does **not exist** in the current `decision-catalog.yaml`. It would need to be **created as a new category** or the entries could be placed as additions to `asset_pipeline` or a new section. The original research doc assumed 14 categories but the file only has 12.

**Proposed new category (if added):**
```yaml
animation_system:
  triggers: ["animation", "character animation", "cutscene", "tweening", "keyframe"]
  importance: "medium"
  affects: "visual and gameplay epics"
  options:
    roblox_animation_editor:
      pairs_with: ["Roblox", "Animation Clip Editor", "KeyframeSequence", "priority system", "R15/R6", "glTF import"]
    roblox_tweenservice:
      pairs_with: ["Roblox", "TweenService", "property animation", "UI animation", "easing styles", "CFrame tweening"]
    moon_animator:
      pairs_with: ["Roblox", "Moon Animator 2", "Studio plugin", "IK/FK", "advanced keyframing", "cinematic", "camera animation"]
```

**Web research findings:**
- **Animation Clip Editor** received significant updates in March 2026: improved file importer, glTF file support, support for multiple animation clips per import.
- **Moon Animator 2** won a Roblox Innovation Award in 2025. Features IK/FK support, custom mesh character support, advanced camera tools. Last updated November 2025.
- **TweenService** remains the standard for programmatic property animation (positions, colors, sizes, CFrame, UI elements).
- If this category is created, it should also include entries for other engines (Unity Animator, Unreal Sequencer, Godot AnimationPlayer, etc.) to maintain consistency.

**Sources:**
- [Animation Clip Editor update](https://devforum.roblox.com/t/full-release-animation-clip-editor-improved-importing-and-gltf-support/4260501)
- [Default Animation Editor update](https://devforum.roblox.com/t/update-on-the-default-animation-editor/4165825)
- [Moon Animator 2 on Creator Store](https://create.roblox.com/store/asset/4725618216/Moon-Animator-2)

---

### Category 13: monetization

**NOTE:** This category does **not exist** in `decision-catalog.yaml`. Monetization is currently handled in `architecture-patterns.yaml` under `integration_requirements.monetization`. Adding it as a decision category is optional.

**Proposed entries (if category is added):**
```yaml
monetization:
  triggers: ["monetization", "in-app purchase", "microtransactions", "revenue", "game pass"]
  importance: "high"
  affects: "business and economy epics"
  options:
    roblox_gamepasses:
      pairs_with: ["Roblox", "GamePasses", "one-time purchase", "permanent perks", "MarketplaceService"]
    roblox_developer_products:
      pairs_with: ["Roblox", "Developer Products", "consumable purchases", "in-game currency", "ProcessReceipt"]
    roblox_subscriptions:
      pairs_with: ["Roblox", "Subscriptions", "recurring revenue", "Robux billing", "local currency billing", "tiered access"]
    roblox_rewarded_ads:
      pairs_with: ["Roblox", "Rewarded Video Ads", "opt-in ads", "13+ users", "in-game rewards", "ad monetization"]
    roblox_creator_rewards:
      pairs_with: ["Roblox", "Creator Rewards", "engagement-based payouts", "Premium player time"]
```

**Web research findings:**
- **Subscriptions expansion (RDC 2025):** Players can now purchase subscriptions in Robux in addition to local currency. This is a significant expansion of the subscription model.
- **Creator Rewards program** replaced Premium Payouts on July 24, 2025.
- **Rewarded Video Ads** launched 2025, expanded to all ads-eligible creators February 18, 2026. Players 13+ opt in to watch 6-30 second ads for in-game rewards. 80%+ completion rates.
- **Homepage Feature** ad unit announced at CES 2026 -- premium placement on Roblox homepage (151M+ DAU).
- Added `roblox_rewarded_ads` and `roblox_creator_rewards` as new entries not in the original research doc.
- Updated `roblox_subscriptions` to note Robux billing option from RDC 2025.

**Sources:**
- [Roblox monetization guide 2025](https://boostroom.com/blog/monetization-for-creators-gamepasses-dev-products)
- [Subscriptions in Robux announcement](https://devforum.roblox.com/t/let-your-players-pay-for-subscriptions-within-experiences-in-robux/4552995)
- [Roblox ad monetization guide 2026](https://www.gamebizconsulting.com/blog/roblox-ad-monetization-guide-2026)
- [Rewarded Video Ads for all creators](https://devforum.roblox.com/t/rewarded-video-ads-are-now-available-to-all-ads-eligible-creators/4063278)
- [Roblox income guide 2026](https://www.creation.dev/blog/how-to-make-money-on-roblox)

---

### Category 14: localization

**NOTE:** This category does **not exist** in `decision-catalog.yaml`. Localization is currently referenced only in the `narrative_driven` pattern's `decisions_needed` list. Adding it as a decision category is optional.

**Proposed entry (if category is added):**
```yaml
localization:
  triggers: ["localization", "translation", "internationalization", "i18n", "multilingual"]
  importance: "medium"
  affects: "UI and content epics"
  options:
    roblox_localizationservice:
      pairs_with: ["Roblox", "LocalizationService", "LocalizationTable", "Cloud Localization Portal", "auto-translation", "GetTranslatorForPlayerAsync"]
```

**Web research findings:**
- **LocalizationService** provides API for loading cloud localization tables and performing translations per player locale.
- **Cloud Localization Portal** includes Automatic Text Capture, translator management, and web-based translation editing.
- **Auto-translation** is a built-in feature available through the Localization Portal.
- No significant new localization features discovered for 2025-2026 beyond the established tooling.

**Sources:**
- [LocalizationService docs](https://create.roblox.com/docs/reference/engine/classes/LocalizationService)
- [Automatic translation docs](https://create.roblox.com/docs/production/localization/automatic-translations)
- [Cloud Localization Tool Suite](https://devforum.roblox.com/t/introducing-the-cloud-localization-tool-suite/212851)

---

## Architecture Pattern Proposals

### roblox_experience

```yaml
roblox_experience:
  triggers:
    - "roblox"
    - "roblox experience"
    - "roblox game"
    - "luau"
    - "roblox studio"
  decisions_needed:
    - game_engine
    - rendering_pipeline
    - networking
    - save_persistence
    - ui_framework
    - input_system
  suggested_stack:
    - "Roblox Studio + Luau with strict type annotations"
    - "Rojo + Wally/pesde + Git for professional workflow"
    - "Future Lighting + PBR SurfaceAppearance for visual quality"
    - "RemoteEvent + UnreliableRemoteEvent for client-server communication"
    - "ProfileStore for player data persistence"
    - "Native ScreenGui or Fusion 0.3 for reactive UI"
```

**Changes from original research:**
- Replaced "Knit framework" from suggested_stack (Knit is no longer updated; better as an option, not a default)
- Replaced "ProfileService" with "ProfileStore" (its actively maintained successor)
- Added "strict type annotations" guidance
- Added Wally/pesde (both are valid)
- Replaced "Constraint-based physics + Humanoid" (too specific for a general pattern)
- Added `ui_framework` and `input_system` to decisions_needed (these were missing)

### roblox_multiplayer

```yaml
roblox_multiplayer:
  triggers:
    - "roblox multiplayer"
    - "roblox pvp"
    - "roblox co-op"
    - "roblox competitive"
    - "roblox server"
  decisions_needed:
    - networking
    - save_persistence
    - ai_system
    - input_system
  suggested_stack:
    - "RemoteEvents for reliable client-server communication"
    - "UnreliableRemoteEvent for high-frequency data (positions, rotations)"
    - "Server-side validation for all game-critical logic"
    - "ProfileStore for player data with session locking"
    - "MemoryStoreService for matchmaking queues and cross-server data"
    - "MessagingService for cross-server events"
```

**Changes from original research:**
- Replaced "ProfileService" with "ProfileStore"
- Added `input_system` to decisions_needed
- Minor wording improvements to suggested_stack

---

## Default Stack Proposals

### roblox_standard

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
    - "Rojo + Wally + Git pipeline"
    - "Cross-platform"
    - "Animation Clip Editor + Moon Animator 2"
    - "GamePasses + Developer Products + Subscriptions"
    - "LocalizationService"
  good_for: "Roblox multiplayer experiences, obbies, tycoons, simulators, RPGs"
```

**Changes from original research:**
- Replaced "Knit" with the more general "Rojo + Wally/pesde + Git" (Knit is no longer updated)
- Replaced "ProfileService" with "ProfileStore"
- Updated "SoundService" references to "New Audio API" (it has exited beta)
- Added "AvatarAbilities (beta)" alongside Constraint Physics
- Updated "TopbarPlus" reference (minor library, not essential for default stack)
- Updated Fusion version to 0.3

### roblox_simple

```yaml
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
    - "Built-in Animation Clip Editor"
    - "GamePasses + Developer Products"
  good_for: "Simple games, game jams, learning, obbies, showcases"
```

**Changes from original research:** Minor -- clarified "SoundService (legacy audio)" and updated to "Animation Clip Editor" (current name).

### Starter Template Proposal

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

### Starter Selection Rules Addition

```yaml
roblox_experience:
  recommended: ["roblox_standard", "roblox_studio_only"]
  considerations: "Professional team? → Rojo + Wally. Solo/learning? → Studio only. Complex game? → Standard. Game jam? → Studio only"
```

---

## Key Changes from Original Research

### Deprecated / Incorrect Items

| Item | Original Research | Current Status | Action |
|------|------------------|----------------|--------|
| BridgeNet2 | Listed as active networking option | **Archived, no longer supported** | Replace with ByteNet |
| ProfileService | Listed as primary data solution | **Stable but unmaintained**; ProfileStore is the successor | Add ProfileStore, mark ProfileService as legacy |
| Roact | Listed alongside React-Lua | **Deprecated** | Use react_lua only, remove Roact reference |
| Knit | Listed as default in suggested stacks | **No longer receiving updates** (maintenance-only) | Keep as option but remove from default stacks |
| Roblox Audio API | Listed as beta | **Exited beta**, fully available | Update status |

### New Discoveries Not in Original Research

| Discovery | Details | Impact |
|-----------|---------|--------|
| **AvatarAbilities Library** | Studio Beta (March 2026). Luau-based character controller replacing Humanoid black box. | Add as new physics_system option |
| **pesde** | Emerging Wally alternative supporting multiple Luau runtimes. Compatible with Wally registries. | Add to game_engine and asset_pipeline pairs_with |
| **ByteNet** | BridgeNet successor using buffer serialization. Strict Luau, type-safe. | Replace BridgeNet2 entry |
| **ProfileStore** | Official successor to ProfileService by same author. | Add as primary data persistence recommendation |
| **Rewarded Video Ads** | Launched 2025, available to all creators Feb 2026. 80%+ completion rate. | Add as monetization option |
| **Creator Rewards** | Replaced Premium Payouts July 2025. | Add as monetization option |
| **Subscriptions in Robux** | Announced RDC 2025. Expands subscription billing options. | Update subscriptions entry |
| **Fusion 0.3** | Released August 2024. Contextual values, improved memory management. | Update version reference |
| **Animation Clip Editor updates** | March 2026: glTF support, multiple clips import. | Update animation entries |
| **Samsung Galaxy Store** | October 2025 availability. | Add to deployment_platform |
| **Xbox Ally handhelds** | Native Roblox support announced. | Add to deployment_platform |
| **Moon Animator 2 Innovation Award** | Won 2025 Roblox Innovation Award. | Note in animation entries |

### Category Count Discrepancy

The original research doc references "all 14 categories" but the actual `decision-catalog.yaml` only contains **12 categories**. The missing categories are:
- `animation_system` -- does not exist
- `monetization` -- exists only in `architecture-patterns.yaml` under `integration_requirements`
- `localization` -- does not exist

**Recommendation:** Either:
1. **Conservative:** Add Roblox entries only to the existing 12 categories, and place animation/monetization/localization entries in comments or as additions to architecture-patterns.yaml integration_requirements
2. **Expansive:** Create all 3 new categories with entries for ALL engines (not just Roblox), maintaining consistency

The expansive approach is better for the overall quality of the decision catalog but increases the scope of the PR significantly since you would need to add Unity, Unreal, Godot, etc. entries to the new categories too.

---

## Additional Supporting Libraries Worth Noting

These libraries appeared in research and may be useful as `pairs_with` references:

| Library | Purpose | Status |
|---------|---------|--------|
| **Jecs** | ECS framework for Luau (800K entities at 60fps) | Active, by Ukendio |
| **TopbarPlus** | Enhanced topbar/menu buttons | Active community library |
| **TestEZ** | Testing framework for Roblox | Stable, used with CI/CD |
| **Selene** | Luau linter | Active |
| **StyLua** | Luau formatter | Active |
| **RbxUtil** | Utility library collection by Sleitnick (Net, Signal, Timer, etc.) | Active |

---

## Summary of Verified Roblox Entries

| Category | Entries to Add | New Categories Needed |
|----------|---------------|----------------------|
| game_engine | 1 (roblox) | No |
| rendering_pipeline | 3 (future, shadowmap, compatibility) | No |
| physics_system | 4 (constraints, humanoid, avatar_abilities, custom) | No |
| audio_system | 2 (audio_api, soundservice) | No |
| networking | 3 (remotes, knit, bytenet) | No |
| input_system | 3 (contextaction, userinput, proximity) | No |
| ai_system | 3 (pathfinding, simplepath, state_machines) | No |
| save_persistence | 5 (profileservice, profilestore, lapis, datastore, memorystore) | No |
| ui_framework | 3 (native_gui, fusion, react_lua) | No |
| asset_pipeline | 2 (rojo_wally, studio_native) | No |
| deployment_platform | 1 (crossplatform) | No |
| animation_system | 3 (editor, tweenservice, moon_animator) | **Yes** |
| monetization | 5 (gamepasses, devproducts, subscriptions, rewarded_ads, creator_rewards) | **Yes** |
| localization | 1 (localizationservice) | **Yes** |
| **Total** | **38 new option entries** | **3 new categories (optional)** |
