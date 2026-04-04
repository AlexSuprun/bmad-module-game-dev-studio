# Story 2.2: Add Roblox Common Stacks and Starter Templates

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **game architect seeking a recommended technology stack for Roblox**,
I want **curated default stacks and starter templates for Roblox projects**,
so that **I can quickly adopt a proven combination of technologies instead of choosing each one individually**.

## Acceptance Criteria

1. **Given** `decision-catalog.yaml` is updated, **When** the architecture workflow suggests stacks for a Roblox project, **Then** two common stacks are present at the end of the `common_stacks` section:
   - `roblox_standard` — "Roblox Experience (Standard)": Rojo+Wally+Git, Future Lighting, Constraint Physics + AvatarAbilities (beta), New Audio API, RemoteEvent+UnreliableRemoteEvent, ContextActionService, PathfindingService+SimplePath, ProfileStore, Native ScreenGui or Fusion 0.3, Cross-platform. Good for multiplayer experiences, tycoons, simulators, RPGs
   - `roblox_simple` — "Roblox Simple Game": Studio-only workflow, ShadowMap Lighting, Native Humanoid physics, SoundService (legacy), RemoteEvent, ContextActionService, DataStoreService native, Native ScreenGui, Creator Store assets. Good for simple games, game jams, learning
2. **Given** `decision-catalog.yaml` is updated, **When** the workflow presents starter templates, **Then** two templates are present at the end of the `starter_templates` section:
   - `roblox_standard` — "Roblox Experience (Rojo)": `command_search` = "Rojo init + Wally init in empty project directory", good_for multiplayer/tycoons/simulators/RPGs/professional workflow
   - `roblox_studio_only` — "Roblox Studio Only": `command_search` = "Roblox Studio → New Place", good_for simple games/game jams/learning/obbies/showcases
3. **Given** `decision-catalog.yaml` is updated, **When** the workflow selects starters by game type, **Then** a `roblox_experience` entry is present under `starter_selection_rules.by_game_type` with recommended `["roblox_standard", "roblox_studio_only"]` and appropriate considerations
4. Knit is NOT in any default stack components (Knit is maintenance-only — may appear as `knit_networking` option entry only, which is part of Story 2.1 scope)
5. The YAML passes `npm run lint` with zero errors

## Tasks / Subtasks

- [x] Task 1: Check if Story 2.1 already added stacks/templates/rules (AC: all)
  - [x] Search `decision-catalog.yaml` for `roblox_standard` in `common_stacks` section
  - [x] If found and matching spec → verify all 3 ACs are satisfied → mark tasks complete
  - [x] If NOT found → proceed with Tasks 2–4
- [x] Task 2: Add two common stacks to `common_stacks` section (AC: #1, #4)
  - [x] Append `roblox_standard` stack after existing `vr_experience` entry
  - [x] Append `roblox_simple` stack after `roblox_standard`
- [x] Task 3: Add two starter templates to `starter_templates` section (AC: #2)
  - [x] Append `roblox_standard` template after existing `bevy_default` entry
  - [x] Append `roblox_studio_only` template after `roblox_standard`
- [x] Task 4: Add `roblox_experience` entry to `starter_selection_rules.by_game_type` (AC: #3)
  - [x] Append after existing `narrative_rpg` entry (last entry in `by_game_type`)
- [x] Task 5: Verify `npm run lint` passes with zero errors (AC: #5)

## Dev Notes

### CRITICAL: Scope Overlap with Story 2.1

Story 2.1's implementation file (created before 2.2) included stacks/templates/rules as Tasks 2, 3, 4 in its own scope. Story 2.1 status is `in-progress` at the time this story was created.

**Dev action required:** Before implementing, check `decision-catalog.yaml` for existing Roblox stacks:
```bash
grep -n "roblox_standard\|roblox_simple\|roblox_studio_only" src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml
```
- If entries exist → verify they match the spec below → run lint → done
- If entries do NOT exist → implement per spec below

### Target File

```
src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml
```

This is the **only file that needs modification**. No other files are touched.

### Existing File Structure (End Sections)

```
common_stacks:          (line ~243) — ends with `vr_experience:` entry
starter_templates:      (line ~290) — ends with `bevy_default:` entry
starter_selection_rules: (line ~358)
  by_game_type:         — ends with `narrative_rpg:` entry (line ~396)
```

### Common Stacks Schema

Existing `common_stacks` entries use this format — match exactly:
```yaml
  <key>:
    name: "Human-readable name"
    components: ["item1", "item2", ...]   # inline or block list — match existing style (block list used for longer)
    good_for: "description string"
```

**Exact YAML to append after `vr_experience:` in `common_stacks`:**

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

### Starter Templates Schema

Existing `starter_templates` entries use this format:
```yaml
  <key>:
    name: "Human-readable name"
    command_search: "how to find/create this template"
    decisions_provided: ["decision1", "decision2", ...]
    good_for: ["use case1", "use case2", ...]
```

**Exact YAML to append after `bevy_default:` in `starter_templates`:**

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

### Starter Selection Rules Schema

Existing `starter_selection_rules.by_game_type` entries use this format:
```yaml
    <game_type_key>:
      recommended: ["template_key1", "template_key2"]
      considerations: "question? → option. question? → option"
```

**Exact YAML to append after `narrative_rpg:` in `starter_selection_rules.by_game_type`:**

```yaml
    roblox_experience:
      recommended: ["roblox_standard", "roblox_studio_only"]
      considerations: "Professional team? → Rojo + Wally. Solo/learning? → Studio only. Complex game? → Standard. Game jam? → Studio only"
```

**IMPORTANT:** The `roblox_experience` key goes under `by_game_type:` (indented 4 spaces), NOT at the top level of `starter_selection_rules:`.

### YAML Lint Compliance

```bash
npm run lint   # ← use this (YAML only, NOT npm run lint:md which is markdown)
```

Key YAML conventions to match existing entries:
- 2-space indentation throughout
- `common_stacks` and `starter_templates` keys indented 2 spaces
- `by_game_type` sub-keys indented 4 spaces
- `components` uses block list `- "item"` format (matches longer entries like `unreal_multiplayer`)
- `decisions_provided` and `good_for` in templates use `["inline", "array"]` format
- All string values use double quotes
- No trailing spaces or extra blank lines

### Anti-Patterns to Avoid

| DO NOT | DO INSTEAD | REASON |
|---|---|---|
| Include Knit in any stack `components` | Omit Knit from defaults | Knit is maintenance-only |
| Reference `ProfileService` as primary | Use `ProfileStore` | ProfileService is unmaintained |
| Label New Audio API as "beta" | Use as-is | New Audio API has exited beta |
| Add entries at wrong indentation level | Match schema above | Breaks YAML structure |
| Add `roblox_experience` at top level of `starter_selection_rules` | Add under `by_game_type:` | Must match existing structure |

### Previous Story Intelligence (2.1)

- `npm run lint` (YAML) and `npm run lint:md` (markdown) are **separate commands** — use `npm run lint` for this story
- Story 2.1 modifies the same file (`decision-catalog.yaml`) — check Git diff / current file state before editing to avoid conflicts or duplication
- Pattern established: pure YAML config append at end of sections, no test files, no markdown files
- Always run `npm run lint` after changes to catch indentation/quote errors early

### Git Context

Recent commits at story creation:
- `176b715` — Roblox MCP entries to `engine-mcps.yaml` (Story 1.3)
- `1d02793` — ECS pattern added to `roblox-engine.md` (Story 1.2)
- `717136a` — `roblox-engine.md` knowledge file created (Story 1.2)
- `a7c86e0` — `module.yaml` updated with roblox option (Story 1.1)

Story 2.1 may have additional commits by the time this story is implemented — run `git log --oneline -5` to check current state.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.2]
- [Source: _bmad-output/implementation-artifacts/2-1-add-roblox-options-to-decision-catalog-categories.md] — scope overlap context
- [Source: src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml] — existing schema to match (lines 243–399)

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

### Completion Notes List

- Story 2.1 had already implemented all content for this story (stacks, templates, selection rules) in `decision-catalog.yaml`. Task 1 confirmed all entries matched spec exactly. `npm run lint` passed with zero errors. No code changes were required.

### File List

- src/workflows/3-technical/gds-game-architecture/decision-catalog.yaml (modified by Story 2.1 — verified in scope of this story)
