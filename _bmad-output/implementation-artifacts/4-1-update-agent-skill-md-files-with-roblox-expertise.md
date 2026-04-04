# Story 4.1: Update Agent SKILL.md Files with Roblox Expertise

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **BMGD user interacting with AI agents on a Roblox project**,
I want **the Game Developer, Game QA, and Solo Dev agents to identify as Roblox-capable**,
so that **I trust their Roblox-specific guidance and know they have relevant engine expertise**.

## Acceptance Criteria

1. **Given** `gds-agent-game-dev/SKILL.md` is updated, **When** the agent introduces itself, **Then** line 10 (Overview) reads "Unity, Unreal, Godot, Roblox, and custom engines" and line 14 (Identity) reads "Unity, Unreal, Godot, Roblox, and custom engines"

2. **Given** `gds-agent-game-qa/SKILL.md` is updated, **When** the agent introduces itself, **Then** line 10 (Overview) reads "Unity, Unreal, Godot, and Roblox", line 14 (Identity) reads "Unity, Unreal, Godot, and Roblox", and line 34 (Critical Actions) reads "Unity Test Framework, Unreal Automation, Godot GUT, or Roblox TestEZ"

3. **Given** `gds-agent-game-solo-dev/SKILL.md` is updated, **When** the agent introduces itself, **Then** line 14 (Identity) reads "Unity, Unreal, Godot, and Roblox"

4. **And** no other content in any SKILL.md file is changed beyond the specified lines

5. **And** all modified markdown files pass `npm run lint:md` with zero errors

## Tasks / Subtasks

- [x] Task 1: Update `src/agents/gds-agent-game-dev/SKILL.md` (AC: #1, #4, #5)
  - [x] Line 10 — change `Unity, Unreal, and custom engines` → `Unity, Unreal, Godot, Roblox, and custom engines` (in Overview sentence)
  - [x] Line 14 — change `Unity, Unreal, and custom engines` → `Unity, Unreal, Godot, Roblox, and custom engines` (in Identity sentence)
  - [x] Verify no other lines changed (`git diff` should show exactly 2 lines changed)

- [x] Task 2: Update `src/agents/gds-agent-game-qa/SKILL.md` (AC: #2, #4, #5)
  - [x] Line 10 — change `Unity, Unreal, and Godot projects` → `Unity, Unreal, Godot, and Roblox projects` (in Overview sentence)
  - [x] Line 14 — change `Unity, Unreal, and Godot` → `Unity, Unreal, Godot, and Roblox` (in Identity sentence)
  - [x] Line 34 — change `Unity Test Framework, Unreal Automation, or Godot GUT documentation` → `Unity Test Framework, Unreal Automation, Godot GUT, or Roblox TestEZ documentation` (in Critical Actions)
  - [x] Verify no other lines changed (`git diff` should show exactly 3 lines changed)

- [x] Task 3: Update `src/agents/gds-agent-game-solo-dev/SKILL.md` (AC: #3, #4, #5)
  - [x] Line 14 — change `Expert in Unity, Unreal, and Godot` → `Expert in Unity, Unreal, Godot, and Roblox` (in Identity sentence)
  - [x] Verify no other lines changed (`git diff` should show exactly 1 line changed)

- [x] Task 4: Run `npm run lint:md` and fix any errors (AC: #5)

### Review Findings

- [x] [Review][Decision] `bmad-skill-manifest.yaml` identity strings not updated in all 3 agents — patched: updated `identity:` field in all 3 manifests to match SKILL.md engine lists.

- [x] [Review][Defer] Platform lists don't reflect Roblox's platform profile [all 3 SKILL.md files] — pre-existing, out of scope
- [x] [Review][Defer] No Roblox-specific behavior/instruction rules added to game-dev or solo-dev Critical Actions — pre-existing gap, out of scope per AC
- [x] [Review][Defer] TestEZ may be deprecated in favor of newer Roblox testing patterns — pre-existing knowledge concern, separate story
- [x] [Review][Defer] Workflow files have no Roblox branches (gds-test-framework, gds-test-automate, gds-e2e-scaffold, gds-test-design, gds-performance-test) — pre-existing, out of scope
- [x] [Review][Defer] gds-generate-project-context step files omit Roblox engine detection signatures — pre-existing
- [x] [Review][Defer] Architecture step-03-starter.md omits Roblox engine recommendation — pre-existing
- [x] [Review][Defer] roblox-testing.md knowledge file not referenced in gds-test-framework workflow load path — pre-existing

## Dev Notes

### Target Files — Exact Changes

#### File 1: `src/agents/gds-agent-game-dev/SKILL.md`

**Line 10** (Overview paragraph):

| | Text |
|---|---|
| BEFORE | `…performs code reviews with deep expertise in Unity, Unreal, and custom engines.` |
| AFTER | `…performs code reviews with deep expertise in Unity, Unreal, Godot, Roblox, and custom engines.` |

**Line 14** (Identity paragraph):

| | Text |
|---|---|
| BEFORE | `Battle-hardened dev with expertise in Unity, Unreal, and custom engines.` |
| AFTER | `Battle-hardened dev with expertise in Unity, Unreal, Godot, Roblox, and custom engines.` |

#### File 2: `src/agents/gds-agent-game-qa/SKILL.md`

**Line 10** (Overview paragraph):

| | Text |
|---|---|
| BEFORE | `…ensures quality across Unity, Unreal, and Godot projects.` |
| AFTER | `…ensures quality across Unity, Unreal, Godot, and Roblox projects.` |

**Line 14** (Identity paragraph):

| | Text |
|---|---|
| BEFORE | `Senior QA architect with 12+ years in game testing across Unity, Unreal, and Godot.` |
| AFTER | `Senior QA architect with 12+ years in game testing across Unity, Unreal, Godot, and Roblox.` |

**Line 34** (Critical Actions):

| | Text |
|---|---|
| BEFORE | `- Cross-check recommendations with the current official Unity Test Framework, Unreal Automation, or Godot GUT documentation.` |
| AFTER | `- Cross-check recommendations with the current official Unity Test Framework, Unreal Automation, Godot GUT, or Roblox TestEZ documentation.` |

#### File 3: `src/agents/gds-agent-game-solo-dev/SKILL.md`

**Line 14** (Identity paragraph):

| | Text |
|---|---|
| BEFORE | `Indie is a battle-hardened solo game developer who ships complete games from concept to launch. Expert in Unity, Unreal, and Godot, they've shipped titles…` |
| AFTER | `Indie is a battle-hardened solo game developer who ships complete games from concept to launch. Expert in Unity, Unreal, Godot, and Roblox, they've shipped titles…` |

### Anti-Patterns to Avoid

| DO NOT | DO INSTEAD | REASON |
|--------|-----------|--------|
| Rewrite or reformat surrounding content | Change only the listed text within each line | AC #4 requires no other changes |
| Add "Roblox" to Capabilities tables | Leave Capabilities unchanged | Only engine lists in Overview/Identity/Critical Actions need updating |
| Change the order of existing engines | Keep Unity, Unreal, Godot order; append Roblox last (with "and") | Consistent with existing multi-engine format |
| Use `npm run lint` | Use `npm run lint:md` | `npm run lint` is YAML-only; markdown requires `lint:md` |

### Lint Command

```bash
npm run lint:md   # markdown linting — NOT npm run lint (YAML only)
```

### Scope Boundary

**These 3 files only.** Do not modify:
- Any other SKILL.md in `src/agents/` or `src/workflows/`
- Any YAML files
- Any other content within the 3 target files

Story 4.2 (setup-roblox.md) and Story 4.3 (docs/index.md) are separate stories.

### Previous Story Intelligence

- Epics 1–3 established all Roblox content. Story 4.1 is purely identity/persona text updates — no YAML, no knowledge files, no CSV.
- The consistent naming convention observed across all prior stories: `Unity, Unreal, Godot` is the existing engine trio; Roblox is appended as fourth with "and Roblox" pattern.
- From Story 3.1 learnings: lint command is `npm run lint:md` (not `npm run lint`).

### Git Context

Recent commits show the Roblox engine knowledge base is complete:
- `7feb639` — Story 2.3 (architecture patterns)
- `e079a2f` — Story 2.2 (stacks/templates)
- `2d06401` — Story 2.1 (decision catalog)
- `176b715` — Story 1.3 (MCP entries)
- `1d02793` / `717136a` — Story 1.2 (roblox-engine.md)

This is the first story in Epic 4.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story 4.1] — exact line specs
- [Source: src/agents/gds-agent-game-dev/SKILL.md] — current content (lines 10, 14)
- [Source: src/agents/gds-agent-game-qa/SKILL.md] — current content (lines 10, 14, 34)
- [Source: src/agents/gds-agent-game-solo-dev/SKILL.md] — current content (line 14)

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

None.

### Completion Notes List

- Updated gds-agent-game-dev/SKILL.md: 2 lines changed (Overview + Identity) to add Godot and Roblox to engine list.
- Updated gds-agent-game-qa/SKILL.md: 3 lines changed (Overview, Identity, Critical Actions) to add Roblox; Critical Actions now references Roblox TestEZ.
- Updated gds-agent-game-solo-dev/SKILL.md: 1 line changed (Identity) to add Roblox.
- `npm run lint:md` passed with 0 errors across 366 files.
- git diff confirms exactly 2+3+1 = 6 lines changed, no other content modified.

### File List

- src/agents/gds-agent-game-dev/SKILL.md
- src/agents/gds-agent-game-qa/SKILL.md
- src/agents/gds-agent-game-solo-dev/SKILL.md
