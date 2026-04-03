# Story 1.3: Add Roblox MCP Server Entries

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **developer setting up AI tooling for a Roblox project**,
I want **the architecture workflow to recommend Roblox-specific MCP servers**,
so that **I can integrate Claude Code or other AI tools directly with Roblox Studio**.

## Acceptance Criteria

1. **Given** `engine-mcps.yaml` is updated with a `roblox:` engine block, **When** an agent recommends MCP servers for a Roblox project, **Then** two MCP entries are available:
   - `roblox_builtin_mcp` — Roblox Studio Built-in MCP Server (primary): all 12 schema fields populated, capabilities include code execution / model insertion / play mode / 3D mesh generation, install_type notes built-in with Studio 2026+, license "Proprietary (Roblox)"
   - `roblox_community_mcp` — boshyxd/robloxstudio-mcp (alternative): all 12 schema fields populated, 39+ tools, Creator Store plugin install, license MIT
2. `recommendation_rules.multiple_options` includes `roblox_default: "roblox_builtin_mcp"` and `roblox_alternative: "roblox_community_mcp"`
3. The YAML passes `npm run lint`

## Tasks / Subtasks

- [x] Task 1: Add `roblox:` engine block to `engine-mcps.yaml` after the `godot:` block (AC: #1)
  - [x] Add `roblox_builtin_mcp` entry with all 12 fields (name, repo, search_term, description, capabilities, install_type, install_steps, requirements, supported_clients, license, benefit_summary — no `note` field needed)
  - [x] Add `roblox_community_mcp` entry with all 12 fields
- [x] Task 2: Update `recommendation_rules.multiple_options` in `engine-mcps.yaml` (AC: #2)
  - [x] Add `roblox_default: "roblox_builtin_mcp"` after `godot_alternative`
  - [x] Add `roblox_alternative: "roblox_community_mcp"` after `roblox_default`
- [x] Task 3: Verify `npm run lint` passes with zero errors (AC: #3)

## Dev Notes

### Target File

```
src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml
```

This is the **only file that needs modification**. No other files are touched in this story.

### Existing File Structure

The file has this top-level layout (engine order matters — roblox goes between godot and general):

```
engine_mcps:
  unity:      ...
  unreal:     ...
  godot:      ...
  # INSERT roblox: HERE
  general:    ...

recommendation_rules:
  always_recommend: [...]
  multiple_options:
    note: "..."
    unity_default: "mcp_unity"
    unity_alternative: "unity_mcp_coplay"
    unreal_default: "unreal_mcp"
    godot_default: "godot_mcp_bradypp"
    godot_alternative: "godot_mcp_ee0pdt"
    # INSERT roblox_default AND roblox_alternative HERE
  recommend_if: {...}
  presentation_order: [...]

presentation_guidance: {...}
```

### MCP Entry Schema (12 fields)

Every MCP entry follows this exact schema (verified from existing entries):

| Field | Type | Notes |
|---|---|---|
| `name` | string | Human-readable with author attribution |
| `repo` | string | `owner/repo` GitHub path (use `"N/A (ships with Roblox Studio)"` for built-in) |
| `search_term` | string | WebSearch verification query |
| `description` | string | One-line description |
| `capabilities` | string[] | Array of capability strings |
| `install_type` | string | Brief install method description |
| `install_steps` | string[] | Ordered setup steps |
| `requirements` | string | Version/dependency requirements |
| `supported_clients` | string[] | Array of supported AI clients |
| `license` | string | License identifier |
| `benefit_summary` | string | One-sentence user-facing value |
| `note` | string | OPTIONAL — add only if needed |

### roblox_builtin_mcp Entry (PRIMARY — use this exactly)

```yaml
      roblox_builtin_mcp:
        name: "Roblox Studio Built-in MCP Server"
        repo: "N/A (ships with Roblox Studio)"
        search_term: "Roblox Studio built-in MCP server 2026"
        description: "Native MCP server built into Roblox Studio, providing direct AI agent access to Studio for code execution, model insertion, play mode control, and 3D mesh generation"
        capabilities:
          - "Execute Luau code in Studio"
          - "Insert models from Creator Store"
          - "Read console output"
          - "Start/stop play mode and run scripts in play mode"
          - "Check Studio mode state"
          - "Generate textured 3D meshes from text prompts"
        install_type: "Built into Roblox Studio (no separate install for Studio side)"
        install_steps:
          - "Ensure Roblox Studio is updated to latest version (2026+)"
          - "Enable MCP Server in Studio Settings"
          - "Configure your MCP client (Claude Code, Cursor, etc.) to connect"
        requirements: "Roblox Studio 2026+ (latest version)"
        supported_clients: ["Claude Desktop", "Claude Code", "Cursor", "Codex"]
        license: "Proprietary (Roblox)"
        benefit_summary: "Zero-install AI agent access to Roblox Studio - code execution, model insertion, play mode control, and 3D mesh generation all built in."
```

### roblox_community_mcp Entry (ALTERNATIVE — use this exactly)

```yaml
      roblox_community_mcp:
        name: "Roblox Studio MCP (boshyxd)"
        repo: "boshyxd/robloxstudio-mcp"
        search_term: "boshyxd robloxstudio-mcp github"
        description: "Feature-rich community MCP server with 39+ tools for game hierarchy exploration, script editing, instance manipulation, and build management"
        capabilities:
          - "Execute arbitrary Luau code in Studio edit context"
          - "21 read-only inspector tools (file tree, search, properties, grep scripts)"
          - "Create, delete, modify, and reparent instances"
          - "Read, edit, insert, and delete scripts"
          - "Export/import builds as JSON"
          - "Creator Store asset integration"
          - "Undo/redo support"
          - "Playtest start/monitor/stop with log capture"
        install_type: "Studio plugin (Creator Store) + Node.js MCP server"
        install_steps:
          - "Install Studio plugin from Creator Store or download MCPPlugin.rbxmx from Releases"
          - "Clone repo and install Node.js MCP server"
          - "Configure MCP client to connect"
        requirements: "Roblox Studio, Node.js"
        supported_clients: ["Claude Desktop", "Claude Code", "Cursor", "Gemini", "Codex"]
        license: "MIT"
        benefit_summary: "39+ tools for comprehensive Studio manipulation including read-only inspection mode, build export/import, and script grep - goes beyond the built-in MCP capabilities."
```

### recommendation_rules.multiple_options Addition

Add these two lines after `godot_alternative: "godot_mcp_ee0pdt"` (before `recommend_if`):

```yaml
    roblox_default: "roblox_builtin_mcp"
    roblox_alternative: "roblox_community_mcp"
```

### Complete roblox Engine Block (Ready to Insert)

Insert this block between the `godot:` block and the `general:` block:

```yaml
  roblox:
    engine: "Roblox"
    mcps:
      roblox_builtin_mcp:
        name: "Roblox Studio Built-in MCP Server"
        repo: "N/A (ships with Roblox Studio)"
        search_term: "Roblox Studio built-in MCP server 2026"
        description: "Native MCP server built into Roblox Studio, providing direct AI agent access to Studio for code execution, model insertion, play mode control, and 3D mesh generation"
        capabilities:
          - "Execute Luau code in Studio"
          - "Insert models from Creator Store"
          - "Read console output"
          - "Start/stop play mode and run scripts in play mode"
          - "Check Studio mode state"
          - "Generate textured 3D meshes from text prompts"
        install_type: "Built into Roblox Studio (no separate install for Studio side)"
        install_steps:
          - "Ensure Roblox Studio is updated to latest version (2026+)"
          - "Enable MCP Server in Studio Settings"
          - "Configure your MCP client (Claude Code, Cursor, etc.) to connect"
        requirements: "Roblox Studio 2026+ (latest version)"
        supported_clients: ["Claude Desktop", "Claude Code", "Cursor", "Codex"]
        license: "Proprietary (Roblox)"
        benefit_summary: "Zero-install AI agent access to Roblox Studio - code execution, model insertion, play mode control, and 3D mesh generation all built in."

      roblox_community_mcp:
        name: "Roblox Studio MCP (boshyxd)"
        repo: "boshyxd/robloxstudio-mcp"
        search_term: "boshyxd robloxstudio-mcp github"
        description: "Feature-rich community MCP server with 39+ tools for game hierarchy exploration, script editing, instance manipulation, and build management"
        capabilities:
          - "Execute arbitrary Luau code in Studio edit context"
          - "21 read-only inspector tools (file tree, search, properties, grep scripts)"
          - "Create, delete, modify, and reparent instances"
          - "Read, edit, insert, and delete scripts"
          - "Export/import builds as JSON"
          - "Creator Store asset integration"
          - "Undo/redo support"
          - "Playtest start/monitor/stop with log capture"
        install_type: "Studio plugin (Creator Store) + Node.js MCP server"
        install_steps:
          - "Install Studio plugin from Creator Store or download MCPPlugin.rbxmx from Releases"
          - "Clone repo and install Node.js MCP server"
          - "Configure MCP client to connect"
        requirements: "Roblox Studio, Node.js"
        supported_clients: ["Claude Desktop", "Claude Code", "Cursor", "Gemini", "Codex"]
        license: "MIT"
        benefit_summary: "39+ tools for comprehensive Studio manipulation including read-only inspection mode, build export/import, and script grep - goes beyond the built-in MCP capabilities."
```

### Critical Anti-Patterns to Avoid

**DO NOT use:**
- `Roblox/studio-rust-mcp-server` — this repo is **DEPRECATED** as of March 2026; Roblox shifted engineering to the built-in MCP
- `ZubeidHendricks/roblox-studio-mcp-claude-code` — boshyxd/robloxstudio-mcp is the better community option (39+ tools vs fewer, actively maintained)
- Any other standalone Roblox MCP repo as the primary recommendation

**Note:** `"N/A (ships with Roblox Studio)"` is valid for `repo` field — the built-in MCP has no GitHub repo since it ships with Studio itself.

### YAML Lint Compliance

This project uses `npm run lint` for YAML validation. Key rules from existing entries:
- String arrays use `["item1", "item2"]` inline format for `supported_clients`
- Multi-item arrays use `- "item"` block format for `capabilities` and `install_steps`
- Indentation: 2 spaces throughout (verify by checking existing engine blocks)
- No trailing spaces
- Consistent quote style: double quotes for all string values

Run `npm run lint` after changes. This is the YAML linter — **not** `npm run lint:md` (which is for markdown only).

### Previous Story Learnings (Stories 1.1 & 1.2)

- `npm run lint` (YAML) and `npm run lint:md` (markdown) are separate commands — use `npm run lint` for this story
- This is a pure YAML config addition — no test files, no markdown files needed
- Story 1.1 was a `module.yaml` change (adding roblox to primary_platform multi-select) — already done
- Story 1.2 created `roblox-engine.md` — already in ready-for-dev status
- The worktree is on `feat/roblox-support` branch

### Git Context

Recent commits:
- `a7c86e0` — `src/module.yaml` updated with roblox option (Story 1.1 complete)
- `428c528` — Research documents added to `_bmad-output/planning-artifacts/research/`
- Story 1.2 story file created, status: ready-for-dev

### File Size Impact

The addition will add approximately 50-55 lines to `engine-mcps.yaml`. Current file is ~270 lines; new total ~325 lines.

### Project Structure Notes

- **Single file change**: `src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml`
- This file is consumed by the architecture workflow step 3 (engine selection) to recommend MCP servers
- No other config files reference `engine-mcps.yaml` directly — standalone change with no cascade effects
- No new directories or files required

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story 1.3]
- [Source: _bmad-output/planning-artifacts/research/roblox-config-mcps-agents-research.md#Roblox MCP Server Entries]
- [Source: src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml] — existing schema to match

### Review Findings

- [x] [Review][Decision] "Codex" vs "Codex CLI" in supported_clients — Fixed: changed `"Codex"` → `"Codex CLI"` in both Roblox entries to match existing file convention. [src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml]
- [x] [Review][Patch] `install_type` missing "2026+" version signal (AC1 violation) — Fixed: `install_type` updated to `"Built into Roblox Studio 2026+ (no separate install for Studio side)"`. [src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml]
- [x] [Review][Defer] `general` engine block missing `engine` field — pre-existing schema inconsistency, not introduced by this change [src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml] — deferred, pre-existing
- [x] [Review][Defer] `workflow.md` missing `knowledge_fragments.roblox` entry — roblox-engine.md created in Story 1.2 but workflow.md not updated to register it; outside Story 1.3 scope [src/workflows/3-technical/gds-game-architecture/workflow.md] — deferred, outside scope

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

None.

### Completion Notes List

- Added `roblox:` engine block between `godot:` and `general:` in `engine-mcps.yaml` with both `roblox_builtin_mcp` and `roblox_community_mcp` entries, each with all 12 required schema fields.
- Added `roblox_default: "roblox_builtin_mcp"` and `roblox_alternative: "roblox_community_mcp"` to `recommendation_rules.multiple_options`.
- `npm run lint` passes with zero errors.

### Change Log

- 2026-04-03: Added Roblox MCP entries (roblox_builtin_mcp, roblox_community_mcp) and recommendation rules to engine-mcps.yaml.

### File List

- src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml
