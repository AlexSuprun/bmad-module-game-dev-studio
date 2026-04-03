# Story 2.3: Add Roblox Architecture Patterns

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **game architect designing a Roblox experience**,
I want **Roblox-specific architecture patterns that trigger on relevant keywords**,
so that **the workflow automatically identifies required decisions and suggests an appropriate technology stack when I describe a Roblox game**.

## Acceptance Criteria

1. **Given** `architecture-patterns.yaml` is updated, **When** a user's project description contains Roblox-related keywords, **Then** `roblox_experience` pattern is present under `requirement_patterns:` with:
   - `triggers`: `["roblox", "roblox experience", "roblox game", "luau", "roblox studio"]`
   - `decisions_needed`: `[game_engine, rendering_pipeline, networking, save_persistence, ui_framework, input_system]`
   - `suggested_stack`: Rojo+Wally, Future Lighting, ProfileStore, RemoteEvent+UnreliableRemoteEvent, Native GUI or Fusion

2. **Given** `architecture-patterns.yaml` is updated, **When** a user's project description contains Roblox multiplayer keywords, **Then** `roblox_multiplayer` pattern is present under `requirement_patterns:` with:
   - `triggers`: `["roblox multiplayer", "roblox pvp", "roblox co-op", "roblox competitive", "roblox server"]`
   - `decisions_needed`: `[networking, save_persistence, ai_system, input_system]`
   - `suggested_stack`: server-side validation, ProfileStore with session locking, MemoryStoreService, MessagingService

3. `suggested_stack` entries use ProfileStore (not ProfileService) and omit Knit from defaults
4. The YAML passes `npm run lint` with zero errors

## Tasks / Subtasks

- [x] Task 1: Check if patterns already exist (AC: all)
  - [x] Run `grep -n "roblox_experience\|roblox_multiplayer" src/workflows/3-technical/gds-game-architecture/architecture-patterns.yaml`
  - [x] If both found and matching spec → run lint → done
  - [x] If NOT found → proceed with Tasks 2–3
- [x] Task 2: Append `roblox_experience` and `roblox_multiplayer` to `requirement_patterns:` section (AC: #1, #2, #3)
  - [x] Insert after the `racing_vehicle:` entry (last entry in `requirement_patterns:`, before `# Quality attribute patterns` comment at line ~304)
  - [x] Match indentation and quoting style of existing entries (2-space indent, double-quoted strings)
- [x] Task 3: Verify `npm run lint` passes with zero errors (AC: #4)

### Review Findings

- [x] [Review][Decision] "roblox" trigger in roblox_experience may subsume roblox_multiplayer triggers — If the workflow performs substring/prefix matching, phrases like "roblox pvp" match both `roblox_experience` (via bare "roblox") and `roblox_multiplayer` simultaneously. No priority or merge strategy is defined in the file. Clarify: is co-firing intended, or should "roblox" be narrowed to avoid shadowing the multiplayer pattern?
- [x] [Review][Defer] "roblox server" trigger is ambiguous/low-signal [architecture-patterns.yaml] — deferred, pre-existing: trigger is an infrastructure noun that appears in ordinary Roblox prose, not a deliberate design keyword; could cause false positives on single-player GDDs
- [x] [Review][Defer] decisions_needed key names don't align with decision_rules keys (game_engine vs engine_selection) [architecture-patterns.yaml] — deferred, pre-existing: schema-level mismatch exists across all patterns, not introduced by this story
- [x] [Review][Defer] rendering_pipeline has no decision_rules heuristic block [architecture-patterns.yaml] — deferred, pre-existing: gap exists across the entire file before this story
- [x] [Review][Defer] ai_system in roblox_multiplayer.decisions_needed has no corresponding suggested_stack guidance [architecture-patterns.yaml] — deferred, pre-existing: spec-level gap; faithfully implemented per AC2
- [x] [Review][Defer] decision-catalog.yaml still lists profileservice as a valid option despite inline deprecation warning [architecture-patterns.yaml] — deferred, pre-existing: catalog inconsistency predates this story
- [x] [Review][Defer] "roblox co-op" trigger may overlap with bare "co-op" in realtime_multiplayer if matching is substring-based [architecture-patterns.yaml] — deferred, pre-existing: matching strategy behavior is pre-existing and not defined in this file
- [x] [Review][Defer] Future Lighting recommendation is unqualified for mobile/low-end devices [architecture-patterns.yaml] — deferred, pre-existing: spec-level design decision, not an implementation error
- [x] [Review][Defer] MemoryStoreService/MessagingService recommendations lack platform quota/rate-limit caveats [architecture-patterns.yaml] — deferred, pre-existing: content-level gap in the spec

## Dev Notes

### Target File

```
src/workflows/3-technical/gds-game-architecture/architecture-patterns.yaml
```

**Only this file needs modification.** No other files are touched.

### Insertion Point

Append both patterns at the **end of `requirement_patterns:` section**, after `racing_vehicle:` entry and before the `# Quality attribute patterns` comment block (around line 304 in the current file).

Current end of `requirement_patterns:` section:
```yaml
  racing_vehicle:
    triggers:
      - "racing"
      ...
    suggested_stack:
      - "Multiple camera modes (chase, cockpit, replay)"

# Quality attribute patterns     ← INSERT BEFORE THIS COMMENT
quality_attributes:
```

### Exact YAML to Insert

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
      - "Roblox Engine with Luau + Rojo + Wally/pesde for project structure and packages"
      - "Future Lighting for best visual fidelity"
      - "ProfileStore for persistent data (NOT ProfileService — unmaintained)"
      - "RemoteEvent + UnreliableRemoteEvent for client-server communication"
      - "Native ScreenGui or Fusion 0.3 for UI"
      - "ContextActionService for cross-platform input handling"

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
      - "Server-side validation for all gameplay actions (never trust client)"
      - "ProfileStore with session locking for concurrent server data safety"
      - "MemoryStoreService for ephemeral cross-server state (queues, leaderboards)"
      - "MessagingService for cross-server communication"
      - "UnreliableRemoteEvent for high-frequency position updates"
```

### Schema Reference

Existing `requirement_patterns` entries follow this structure (match exactly):

```yaml
  <key>:
    triggers:
      - "trigger text"
    decisions_needed:
      - decision_key
    suggested_stack:
      - "suggestion string"
```

- 2-space indent for pattern keys under `requirement_patterns:`
- 4-space indent for sub-keys (`triggers:`, `decisions_needed:`, `suggested_stack:`)
- 6-space indent for list items (`- "value"`)
- All string values use double quotes
- No trailing spaces or extra blank lines between entries

### YAML Lint Command

```bash
npm run lint   # YAML linting only — do NOT use npm run lint:md (that's for markdown)
```

### Anti-Patterns to Avoid

| DO NOT | DO INSTEAD | REASON |
|---|---|---|
| Reference `ProfileService` as primary | Use `ProfileStore` | ProfileService is unmaintained; same author created ProfileStore as successor |
| Include Knit in `suggested_stack` | Omit Knit from defaults | Knit is maintenance-only (no longer receiving updates) |
| Add patterns outside `requirement_patterns:` | Keep under `requirement_patterns:` | `quality_attributes:` and `integration_requirements:` are different sections |
| Use `roblox_multiplayer` triggers that overlap `realtime_multiplayer` | Use "roblox multiplayer", "roblox pvp" etc. | Roblox patterns are platform-specific, distinct from generic multiplayer patterns |
| Label New Audio API as "beta" | Treat as released | New Audio API has exited beta as of 2026 |

### Previous Story Intelligence (2.1 and 2.2)

- **Story 2.1** (done): Added 38 Roblox entries to `decision-catalog.yaml` across all 12 decision categories. Also added `common_stacks`, `starter_templates`, and `starter_selection_rules` Roblox entries.
- **Story 2.2** (review): Verified Story 2.1 already implemented stacks/templates. No file changes were needed.
- **Pattern established:** Pure YAML config append at end of sections, no test files, no markdown files.
- **Lint separation:** `npm run lint` = YAML; `npm run lint:md` = markdown. This story uses YAML-only lint.
- `decision-catalog.yaml` has been modified; `architecture-patterns.yaml` has NOT been modified by previous stories — no overlap risk.

### Git Context

Recent commits:
- `2d06401` — Roblox options to `decision-catalog.yaml` (Story 2.1)
- `176b715` — Roblox MCP entries to `engine-mcps.yaml` (Story 1.3)
- `1d02793` — ECS pattern added to `roblox-engine.md` (Story 1.2)
- `717136a` — `roblox-engine.md` knowledge file created (Story 1.2)

Run `git log --oneline -5` to check current state before implementing.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.3]
- [Source: src/workflows/3-technical/gds-game-architecture/architecture-patterns.yaml] — existing schema (lines 1–508)
- [Source: _bmad-output/implementation-artifacts/2-2-add-roblox-common-stacks-and-starter-templates.md] — previous story learnings

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

### Completion Notes List

- Patterns `roblox_experience` and `roblox_multiplayer` did not exist; inserted after `racing_vehicle:` entry (lines 304–342) in `architecture-patterns.yaml`.
- Used ProfileStore (not ProfileService), omitted Knit per anti-pattern guidance.
- `npm run lint` passes with zero errors.

### File List

- src/workflows/3-technical/gds-game-architecture/architecture-patterns.yaml
