# Story 1.1: Add Roblox to Engine Selection

## Story

**As a** BMGD user installing the module,
**I want** Roblox to appear as a selectable engine during `npx bmad-method install`,
**So that** I can choose Roblox as my game development platform and have it propagate to all workflows.

## Status

done

## Acceptance Criteria

**Given** a user runs `npx bmad-method install` with BMGD module
**When** they reach the `primary_platform` multi-select prompt
**Then** "Roblox" appears as a selectable option alongside Unity, Unreal, Godot, and Custom/Other
**And** the `default` array includes `"roblox"` so it is pre-selected by default
**And** selecting Roblox stores `"roblox"` in the resolved config for downstream workflows
**And** the YAML passes `npm run lint`

## Tasks/Subtasks

- [x] Task 1: Add Roblox entry to `primary_platform` multi-select in `src/module.yaml`
  - [x] Add `{ value: "roblox", label: "Roblox" }` entry to the multi-select list
  - [x] Add `"roblox"` to the `default` array
- [x] Task 2: Verify YAML passes `npm run lint`

### Review Findings

- [x] [Review][Defer] Downstream Roblox support gaps: knowledge files, decision catalog, MCP entries, test framework branches, agent persona strings, and how-to docs are absent for `roblox` [multiple files] — deferred, pre-existing; tracked in Stories 1.2, 1.3 and Epics 2–4

## Dev Notes

### Target File
- `src/module.yaml` — the module configuration file consumed by `npx bmad-method install`

### Current State
The `primary_platform` multi-select currently has 4 options: `unity`, `unreal`, `godot`, `other`.
The `default` array is: `["unity", "unreal", "godot", "other"]`.

### Required Change
Add a 5th option for Roblox:
```yaml
- value: "roblox"
  label: "Roblox"
```
And add `"roblox"` to the `default` array.

### Acceptance Criteria Notes
- NFR1: YAML must pass `npm run lint` (eslint with eslint-plugin-yml)
- NFR8: Value key must follow existing `engine_descriptor` naming convention (lowercase, no spaces)

## Dev Agent Record

### Implementation Plan

Simple single-file YAML change. No tests applicable (config file, no executable logic). Validation is `npm run lint` passing.

### Debug Log

_Empty_

### Completion Notes

Added `"roblox"` as a 5th `primary_platform` multi-select option in `src/module.yaml`, positioned between Godot and Custom/Other. Also added `"roblox"` to the `default` array so it is pre-selected during `npx bmad-method install`. `npm run lint` passes with zero warnings.

## File List

- src/module.yaml

## Change Log

- Added Roblox to `primary_platform` multi-select and `default` array in `src/module.yaml` (Date: 2026-04-03)
