# Roblox Module Config, MCPs & Agent Updates Research

**Date:** 2026-04-02
**Scope:** module.yaml, engine-mcps.yaml, agent SKILL.md files, setup guide, Roblox MCP ecosystem, development tooling
**Depends on:** technical-bmgd-roblox-extension-research-2026-04-01.md

---

## module.yaml Analysis

### Exact YAML Structure

File: `src/module.yaml`

```yaml
code: gds
name: "BMGD: BMad Game Dev Studio"
header: "BMad Game Development Studio Module"
subheader: ""
default_selected: false
module_version: 0.2.0

# Core config variables (injected from BMAD core):
## user_name, communication_language, document_output_language, output_folder

project_name:
  prompt: "What is the name of your game project?"
  default: "{directory_name}"
  result: "{value}"

game_dev_experience:
  prompt: [multi-line array]
  default: "intermediate"
  result: "{value}"
  single-select: [beginner, intermediate, expert]

planning_artifacts:
  prompt: "..."
  default: "{output_folder}/planning-artifacts"
  result: "{project-root}/{value}"

implementation_artifacts:
  prompt: "..."
  default: "{output_folder}/implementation-artifacts"
  result: "{project-root}/{value}"

sprint_artifacts:
  inherit: "implementation_artifacts"

project_knowledge:
  prompt: "..."
  default: "docs"
  result: "{project-root}/{value}"

primary_platform:                    # <-- ENGINE SELECTION
  prompt: "Which game development framework or engine do you want to install support for?"
  default: ["unity", "unreal", "godot", "other"]
  required: true
  result: "{value}"
  multi-select:
    - value: "unity"
      label: "Unity"
    - value: "unreal"
      label: "Unreal Engine"
    - value: "godot"
      label: "Godot"
    - value: "other"
      label: "Custom / Other"

directories:
  - "{planning_artifacts}"
  - "{implementation_artifacts}"
  - "{project_knowledge}"
```

### How `primary_platform` Multi-Select Works

- Uses `multi-select:` (not `single-select:`), allowing users to pick multiple engines simultaneously
- `default:` is an array `["unity", "unreal", "godot", "other"]` meaning all are pre-selected by default
- `required: true` means at least one must be selected
- `result: "{value}"` stores the selected values -- the resolved config value is used by workflows

### How Engine Selection Propagates

1. User selects engines during `npx bmad-method install` via the multi-select prompt
2. Selected values are stored in `{project-root}/_bmad/gds/config.yaml`
3. Architecture workflow (step-03) loads `knowledge/{engine}-engine.md` for selected engines
4. Decision catalog maps architectural choices to engine-specific `pairs_with` options
5. QA agent loads `knowledge/{engine}-testing.md` via `qa-index.csv` lookup
6. All agents read `project-context.md` (generated output) which contains the selected engine
7. Engine MCPs are recommended based on the selected engine(s)

### Required Change to Add Roblox

Add one entry to the `multi-select:` list under `primary_platform:`:

```yaml
primary_platform:
  # ... existing fields unchanged ...
  multi-select:
    - value: "unity"
      label: "Unity"
    - value: "unreal"
      label: "Unreal Engine"
    - value: "godot"
      label: "Godot"
    - value: "roblox"              # NEW
      label: "Roblox"             # NEW
    - value: "other"
      label: "Custom / Other"
```

Also update the `default:` array to include `"roblox"`:

```yaml
  default: ["unity", "unreal", "godot", "roblox", "other"]
```

---

## engine-mcps.yaml Schema Analysis

File: `src/workflows/3-technical/gds-game-architecture/engine-mcps.yaml`

### Top-Level Structure

```yaml
engine_mcps:
  unity:
    engine: "Unity"
    mcps: { ... }
  unreal:
    engine: "Unreal Engine"
    mcps: { ... }
  godot:
    engine: "Godot"
    mcps: { ... }
  general:
    engine: "Any"
    description: "General-purpose MCPs useful for any game engine"
    mcps: { ... }

recommendation_rules: { ... }
presentation_guidance: { ... }
```

### Engine Block Schema

```yaml
engine_mcps:
  <engine_key>:                    # e.g., "unity", "unreal", "godot"
    engine: "<Display Name>"       # e.g., "Unity", "Unreal Engine"
    mcps:
      <mcp_key>:                   # e.g., "mcp_unity", "unreal_mcp"
        name: "<string>"           # Human-readable name with author
        repo: "<owner/repo>"      # GitHub repo path
        search_term: "<string>"   # WebSearch verification query
        description: "<string>"   # One-line description
        capabilities:             # Array of capability strings
          - "<capability>"
        install_type: "<string>"  # Brief install method description
        install_steps:            # Ordered array of setup steps
          - "<step>"
        requirements: "<string>"  # Version requirements
        supported_clients:        # Array of supported AI clients
          - "<client>"
        license: "<string>"       # e.g., "MIT", "Apache-2.0"
        benefit_summary: "<str>"  # One sentence explaining value
        note: "<string>"          # OPTIONAL - additional notes
```

### Fields Per MCP Entry (All Fields)

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | Yes | string | Human-readable name with author attribution |
| `repo` | Yes | string | GitHub `owner/repo` path |
| `search_term` | Yes | string | Query for WebSearch verification of current status |
| `description` | Yes | string | One-line description of the MCP server |
| `capabilities` | Yes | string[] | List of what the MCP can do |
| `install_type` | Yes | string | Brief description of install method |
| `install_steps` | Yes | string[] | Ordered setup instructions |
| `requirements` | Yes | string | Version/dependency requirements |
| `supported_clients` | Yes | string[] | List of supported AI clients |
| `license` | Yes | string | License identifier |
| `benefit_summary` | Yes | string | One-sentence user-facing value statement |
| `note` | No | string | Extra context (e.g., experimental status) |

### MCPs Per Engine Currently

| Engine | Count | MCP Keys |
|--------|-------|----------|
| Unity | 2 | `mcp_unity`, `unity_mcp_coplay` |
| Unreal | 2 | `unreal_mcp`, `unreal_mcp_comprehensive` |
| Godot | 3 | `godot_mcp_bradypp`, `godot_mcp_ee0pdt`, `godot_mcp_docs` |
| General | 1 | `context7` |

### `general` Section Structure

The `general` section differs slightly from engine sections:

```yaml
general:
  engine: "Any"
  description: "General-purpose MCPs useful for any game engine"  # Extra field
  mcps:
    context7: { ... }  # Same MCP entry schema
```

### Recommendation Rules Structure

```yaml
recommendation_rules:
  always_recommend:
    - "<rule string>"
  multiple_options:
    note: "<guidance>"
    <engine>_default: "<mcp_key>"
    <engine>_alternative: "<mcp_key>"
  recommend_if:
    <rule_name>:
      trigger: "<condition>"
      recommend: "<recommendation>"
  presentation_order:
    - "<category>"
```

### Presentation Guidance Structure

```yaml
presentation_guidance:
  <skill_level>:          # expert, intermediate, beginner
    style: "<description>"
    example: |
      <template with {{engine}}, {{mcp_name}}, etc. placeholders>
```

---

## Roblox MCP Server Entries

### CRITICAL UPDATE: Roblox Studio Now Has a Built-in MCP Server

As of March 2026, Roblox has shifted engineering efforts to a **built-in MCP server that ships directly with Roblox Studio**. The standalone `studio-rust-mcp-server` repo is deprecated in favor of this built-in solution. This is a major change from the original research document.

The built-in MCP server:
- Ships natively with Roblox Studio (no separate install needed for Studio side)
- Is the recommended way to connect external AI tools to Studio
- Supports Claude Code, Claude Desktop, Cursor, Codex, and any MCP-compatible client
- Includes 3D mesh generation from text prompts (added March 19, 2026)
- Roblox's future vision: the native Assistant will also become an MCP client

### Official Standalone MCP Server (Deprecated but Functional)

**Repo:** [Roblox/studio-rust-mcp-server](https://github.com/Roblox/studio-rust-mcp-server)
**Status:** DEPRECATED -- engineering shifted to built-in MCP server. Last release: v0.2.321. Repo last updated March 27, 2026. Not actively merging PRs since July 2025.
**Architecture:** Rust-based (axum web server + rmcp server via stdio transport). Studio plugin long-polls the web server.
**Capabilities (verified):**
- `run_code` -- Execute Luau code in Studio
- `insert_model` -- Insert models from Creator Store
- `get_console_output` -- Read Studio console output
- `start_stop_play` -- Start/stop play mode
- `run_script_in_play_mode` -- Run scripts during play with log capture
- `get_studio_mode` -- Check current Studio mode state

**Install process (verified):**
1. Clone `Roblox/studio-rust-mcp-server`
2. Build with `cargo build --release` (requires Rust toolchain)
3. Install companion Studio plugin (included in repo)
4. Configure MCP client JSON config pointing to the built binary

**Requirements:** Roblox Studio 2025+, Rust toolchain for building
**Supported clients:** Claude Desktop, Claude Code
**License:** Apache-2.0

**Sources:**
- [GitHub repo](https://github.com/Roblox/studio-rust-mcp-server)
- [Releases](https://github.com/Roblox/studio-rust-mcp-server/releases)
- [DevForum announcement](https://devforum.roblox.com/t/introducing-the-open-source-studio-mcp-server/3649365/print)

### Community MCP Server (ZubeidHendricks)

**Repo:** [ZubeidHendricks/roblox-studio-mcp-claude-code](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)
**Status:** Active, community-maintained. Builds upon the official Roblox MCP server. Purpose-built for Claude Code integration.
**Capabilities (verified):**
- `mcp__roblox_studio__run_code` -- Execute Luau code in Studio
- `mcp__roblox_studio__insert_model` -- Insert models from Creator Store
- Create RemoteEvent/RemoteFunction objects programmatically
- Generate and insert ModuleScript, Script, LocalScript objects
- Build complete Roblox games programmatically
- Automate game development workflows

**Install process:** Clone repo, follow SETUP_GUIDE.md, configure MCP settings
**Requirements:** Roblox Studio, Node.js
**Supported clients:** Claude Code
**License:** MIT

**Sources:**
- [GitHub repo](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)
- [Setup guide](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code/blob/main/SETUP_GUIDE.md)

### Other MCP Servers Found (NEW -- Not in Original Research)

#### boshyxd/robloxstudio-mcp (RECOMMENDED as primary community option)

**Repo:** [boshyxd/robloxstudio-mcp](https://github.com/boshyxd/robloxstudio-mcp)
**Status:** Very active. v2.5.0+ with 39+ tools. Monorepo architecture. Studio plugin rewritten in TypeScript (roblox-ts) with modular handlers.
**Capabilities (verified):**
- `execute_luau` -- Run arbitrary Luau code in Studio edit context
- 21 read-only inspector tools: `get_file_tree`, `search_files`, `get_place_info`, `get_services`, `search_objects`, `get_instance_properties`, `grep_scripts`
- Instance manipulation: create, delete, modify, reparent
- Script management: read, edit, insert, delete source code
- Property/attribute/tag manipulation
- `export_build`/`import_build` -- Save and compose builds as JSON
- Creator Store asset integration
- Undo/redo support
- Playtest start/monitor/stop with log capture

**Install process:**
1. Install Studio plugin from Creator Store (easiest) or download MCPPlugin.rbxmx from Releases
2. Install Node.js MCP server component
3. Configure MCP client

**Requirements:** Roblox Studio, Node.js
**Supported clients:** Claude Code, Claude Desktop, Cursor, Gemini, Codex, and any MCP-compatible client
**License:** MIT (assumed based on community standard)

**Sources:**
- [GitHub repo](https://github.com/boshyxd/robloxstudio-mcp)
- [DevForum thread v2.5.0](https://devforum.roblox.com/t/v250-roblox-studio-mcp-speed-up-your-workflow-by-letting-ai-read-paths-and-properties/3707071)

#### drgost1/robloxstudio-mcp

**Repo:** [drgost1/robloxstudio-mcp](https://github.com/drgost1/robloxstudio-mcp)
**Status:** Active. 51 tools for AI-powered game development.
**Capabilities:** Explore game hierarchies, read/edit scripts, manipulate properties, create instances, build terrain, run playtests -- all locally via HTTP polling. No external servers, no data collection.
**Install:** Creator Store plugin or manual .rbxmx download + MCP server setup
**Supported clients:** Claude Code, Claude Desktop, Cursor, Gemini, Codex, any MCP-compatible client

**Sources:**
- [GitHub repo](https://github.com/drgost1/robloxstudio-mcp)

### Recommended engine-mcps.yaml Entry

Based on web research findings, the original research document's MCP entries should be updated. The built-in MCP server changes the landscape significantly. Recommended entry:

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
          - "Install Studio plugin from Creator Store or download MCPPlugin.rbxmx"
          - "Clone repo and install Node.js MCP server"
          - "Configure MCP client to connect"
        requirements: "Roblox Studio, Node.js"
        supported_clients: ["Claude Desktop", "Claude Code", "Cursor", "Gemini", "Codex"]
        license: "MIT"
        benefit_summary: "39+ tools for comprehensive Studio manipulation including read-only inspection mode, build export/import, and script grep - goes beyond the built-in MCP capabilities."
```

Also update `recommendation_rules`:

```yaml
recommendation_rules:
  multiple_options:
    roblox_default: "roblox_builtin_mcp"
    roblox_alternative: "roblox_community_mcp"
```

---

## Agent SKILL.md Analysis

### Agents Requiring Updates

#### 1. Game Developer (Link Freeman) -- `gds-agent-game-dev/SKILL.md`

**Lines requiring changes:**

- **Line 4 (description frontmatter):** Currently says "code review" but does not mention engine names -- no change needed here.
- **Line 10 (Overview, line 3 of body):** `"...deep expertise in Unity, Unreal, and custom engines."` -- Needs update
  - **Change to:** `"...deep expertise in Unity, Unreal, Godot, Roblox, and custom engines."`
- **Line 14 (Identity):** `"Battle-hardened dev with expertise in Unity, Unreal, and custom engines."` -- Needs update
  - **Change to:** `"Battle-hardened dev with expertise in Unity, Unreal, Godot, Roblox, and custom engines."`

Note: Godot is also missing from these lines despite being a supported engine. The Roblox update should fix both omissions.

#### 2. Game QA (GLaDOS) -- `gds-agent-game-qa/SKILL.md`

**Lines requiring changes:**

- **Line 10 (Overview):** `"...Unity, Unreal, and Godot projects."` -- Needs update
  - **Change to:** `"...Unity, Unreal, Godot, and Roblox projects."`
- **Line 14 (Identity):** `"...12+ years in game testing across Unity, Unreal, and Godot."` -- Needs update
  - **Change to:** `"...12+ years in game testing across Unity, Unreal, Godot, and Roblox."`
- **Line 33 (Critical Actions):** `"Cross-check recommendations with the current official Unity Test Framework, Unreal Automation, or Godot GUT documentation."` -- Needs update
  - **Change to:** `"Cross-check recommendations with the current official Unity Test Framework, Unreal Automation, Godot GUT, or Roblox TestEZ documentation."`

### Agents NOT Requiring Updates

| Agent | File | Reason No Change Needed |
|-------|------|------------------------|
| **Game Architect** (Cloud Dragonborn) | `gds-agent-game-architect/SKILL.md` | No engine names in text. References engines dynamically via workflows and `project-context.md`. Identity says "20+ years shipping 30+ titles" and "all platforms" -- generic enough. |
| **Game Designer** (Samus Shepard) | `gds-agent-game-designer/SKILL.md` | No engine names in text at all. Focuses on game design concepts (mechanics, player psychology, narrative) which are engine-agnostic. |
| **Game Scrum Master** (Max) | `gds-agent-game-scrum-master/SKILL.md` | No engine names in text. Focuses on sprint planning, story creation, and agile ceremonies -- all engine-agnostic process work. |
| **Game Solo Dev** (Indie) | `gds-agent-game-solo-dev/SKILL.md` | **Line 14 mentions "Expert in Unity, Unreal, and Godot"** -- This SHOULD be updated to include Roblox. Adding to the update list. |
| **Tech Writer** (Paige) | `gds-agent-tech-writer/SKILL.md` | No engine names in text. Focuses on documentation standards and structured writing -- engine-agnostic. |

**CORRECTION:** The Solo Dev agent (Indie) also needs an update:

#### 3. Game Solo Dev (Indie) -- `gds-agent-game-solo-dev/SKILL.md` (ADDED)

**Lines requiring changes:**

- **Line 14 (Identity):** `"Expert in Unity, Unreal, and Godot, they've shipped titles across mobile, PC, and console."` -- Needs update
  - **Change to:** `"Expert in Unity, Unreal, Godot, and Roblox, they've shipped titles across mobile, PC, and console."`

### Summary of All Agent Updates

| Agent | Lines Changed | What Changes |
|-------|--------------|--------------|
| Game Developer | 2 lines (Overview, Identity) | Add "Godot, Roblox" to engine list |
| Game QA | 3 lines (Overview, Identity, Critical Actions) | Add "Roblox" to engine list + TestEZ reference |
| Game Solo Dev | 1 line (Identity) | Add "Roblox" to engine list |
| All others (4 agents) | 0 lines | No changes needed |

---

## Setup Guide Template Analysis

### Structure from setup-unity.md and setup-godot.md

Both setup guides follow an identical structure (Diataxis "How-To" pattern):

```markdown
---
title: "Set up a <Engine> project with BMGD"
description: Configure a new <Engine> project for full production game development with BMGD
---

# Set up a <Engine> project with BMGD
[One-line description]

## When to Use This Guide
[3 bullet points]

## When to Skip This
[3-4 bullet points with cross-references to other guides]

## Prerequisites
[Blockquote with 4 items: BMad installed, engine installed, basic familiarity, game concept]

## Steps
### Step 1: Create your <Engine> project
[Engine-specific project creation steps]

### Step 2: Generate your project context
[Standard: /bmgd-generate-project-context]

### Step 3: Run the brainstorming workflow
[Standard: /bmgd-brainstorm-game]

### Step 4: Create your Game Brief
[Standard: /bmgd-game-brief]

### Step 5: Design your game (GDD)
[Standard: /bmgd-create-gdd]

### Step 6: Create your technical architecture
[Standard: /bmgd-create-architecture -- engine-specific output description]

### Step 7: Plan your first sprint
[Standard: /bmgd-sprint-planning]

### Step 8: Start implementing
[Standard: /bmgd-dev-story -- engine-specific language mention]

## What You Get
[Standard table: project-context.md, game-brief.md, gdd.md, architecture.md, sprint-status.yaml, stories/]

## <Engine>-Specific Considerations
### Project Structure
[Recommended folder layout as code block]

### <Engine-specific subsection>
[Key patterns/concepts for the engine]

### Performance Budgets
[FPS targets per platform]

### Testing Setup
[/bmgd-test-framework with engine-specific framework details]

## Tips
[3 blockquote tips]

## Common Mistakes
[4-row table: Mistake | Solution]

## Next Steps
[4 links to related guides]
```

### Key Differences Between Unity and Godot Guides

| Section | Unity | Godot |
|---------|-------|-------|
| Prerequisites | Unity Hub, Unity 2022 LTS+, C# | Godot 4.2+, GDScript |
| Step 1 | Unity Hub template selection (2D, 3D, URP, HDRP) | Godot Project Manager, renderer choice (Forward+, Mobile, Compatibility) |
| Engine Considerations | ScriptableObjects, Assets/ structure | Scene Organization, Autoload Singletons, Signal Patterns |
| Testing | Unity Test Framework (Edit/Play Mode) | GUT (Godot Unit Test) |
| Common Mistakes | Wrong Unity template, monolithic Assets | Monolithic scenes, mixing GDScript/C# |

---

## Roblox Setup Guide Content

Based on the template analysis and web research, here is the content needed for `docs/how-to/setup-roblox.md`:

### Prerequisites
- BMad Method installed with BMGD module enabled
- Roblox Studio installed (free at create.roblox.com)
- Basic familiarity with Luau scripting
- A game concept or idea you want to develop
- (Recommended) VS Code with Rojo extension for external editing

### Step 1: Create your Roblox project
Two paths:
1. **Studio-only:** Open Roblox Studio, select a template (Baseplate, Classic Baseplate, Flat Terrain, etc.)
2. **Professional workflow (Rojo):**
   - Install Rokit: toolchain manager for Roblox
   - Run `rokit init` in project directory
   - Install Rojo and Wally via Rokit
   - Run `rojo init` to scaffold `default.project.json`
   - Run `wally init` for package management

### Engine-Specific Considerations

**Project Structure (Rojo-based):**
```
project-root/
  src/
    server/        # ServerScriptService
    client/        # StarterPlayerScripts
    shared/        # ReplicatedStorage
  Packages/        # Wally-managed dependencies
  default.project.json
  wally.toml
  .luaurc
  selene.toml
  stylua.toml
```

**Key Patterns:**
- Client-Server split: Server-authoritative model, never trust client
- ModuleScript organization: Services (server), Controllers (client), Shared modules
- Knit framework: Optional service-controller architecture
- Replication: Understand what replicates vs. what stays server-side

**Performance Budgets:**
- 60 FPS across all platforms
- Mobile (60%+ of players): Touch input, auto-scaling UI, memory constraints
- StreamingEnabled for large worlds
- Object pooling for frequently created/destroyed instances

**Testing Setup:**
- TestEZ framework via Wally (`wally add jsdotlua/jest`)
- `describe`/`it` syntax for unit and integration tests
- Studio play mode for integration testing
- Selene linter + StyLua formatter for CI/CD

### MCP Setup (Roblox-specific step)
1. **Built-in MCP (recommended):** Update Studio to latest version, enable MCP Server in settings, configure Claude Code
2. **Community MCP (advanced):** Install boshyxd/robloxstudio-mcp for 39+ tools including read-only inspection and build export

---

## Roblox Development Tooling Status

### Rojo (Filesystem Sync)

| Property | Value |
|----------|-------|
| **Latest version** | 7.6.1 |
| **Repo** | [rojo-rbx/rojo](https://github.com/rojo-rbx/rojo) |
| **Install** | Via Rokit (recommended), binary download, or `cargo install rojo` (requires Rust 1.88+) |
| **Compatibility** | Current Roblox Studio (requires matching Studio plugin version per major release) |
| **Status** | Actively maintained. Standard tool for professional Roblox development. |

### Wally (Package Manager)

| Property | Value |
|----------|-------|
| **Latest version** | 0.3.2 (approximate -- last major release cycle) |
| **Repo** | [UpliftGames/wally](https://github.com/UpliftGames/wally) |
| **Registry** | [wally.run](https://wally.run/) |
| **Install** | Via Rokit (recommended) or binary download |
| **Popular packages** | Knit, ProfileService, Roact/React-Lua, Fusion, TestEZ/Jest, Signal, Promise, TableUtil, TopbarPlus, SimplePath |
| **Status** | Stable, de facto standard. Lockfile-based dependency management. |
| **Note** | Jelly ([OMouta/Jelly](https://github.com/OMouta/Jelly)) is an emerging npm-like alternative built on top of Wally |

### Selene (Linter)

| Property | Value |
|----------|-------|
| **Latest version** | 0.30.0 (released 2026-01-22) |
| **Repo** | [Kampfkarren/selene](https://github.com/Kampfkarren/selene) |
| **Install** | Via Rokit, binary download, or cargo |
| **Features** | Opinionated Lua/Luau linter, Roblox-aware, updated parser supporting recent Luau features, `require` accepts strings in Luau |
| **Status** | Actively maintained |

### StyLua (Formatter)

| Property | Value |
|----------|-------|
| **Latest version** | ~2.4.0 (GitHub) / 2.3.1 (npm) |
| **Repo** | [JohnnyMorganz/StyLua](https://github.com/JohnnyMorganz/StyLua) |
| **Install** | Via Rokit, npm (`@johnnymorganz/stylua`), binary download, or cargo |
| **Features** | Follows Roblox Lua Style Guide, supports Lua 5.2-5.4 + LuaJIT + Luau, stable formatting across minor versions since v2.0 |
| **Status** | Actively maintained. Stable since v2.0.0. |

### Lune (Standalone Luau Runtime)

| Property | Value |
|----------|-------|
| **Latest version** | Recent releases track Luau 0.682+ |
| **Repo** | [lune-org/lune](https://github.com/lune-org/lune) |
| **Install** | Via Rokit, Homebrew (`brew install lune`), or cargo |
| **Features** | Standalone Luau runtime (like Node.js for Lua). Built in Rust. File I/O, HTTP, process management, etc. Useful for build scripts, CI/CD tasks, code generation. |
| **Status** | Actively maintained |

### Rokit (Toolchain Manager)

| Property | Value |
|----------|-------|
| **Latest version** | 1.0.0+ (stable) |
| **Repo** | [rojo-rbx/rokit](https://github.com/rojo-rbx/rokit) |
| **Features** | Next-gen toolchain manager (successor to Foreman/Aftman). Drop-in compatible with existing projects. Native ARM64 support. Fastest install times. |
| **Install** | Automated installer script or cargo |
| **Status** | Actively maintained. Recommended way to install Rojo, Wally, Selene, StyLua, Lune. Last updated Feb 2026. |

### Recommended Toolchain for setup-roblox.md

```
Rokit -> installs -> Rojo 7.6.1
                  -> Wally 0.3.x
                  -> Selene 0.30.0
                  -> StyLua 2.4.x
                  -> Lune (optional, for CI/build scripts)
```

---

## Key Changes from Original Research

The original research document (`technical-bmgd-roblox-extension-research-2026-04-01.md`) was largely accurate but the following differences were identified through web verification:

### 1. Built-in MCP Server (MAJOR CHANGE)

**Original:** Listed `Roblox/studio-rust-mcp-server` as the primary official MCP, with build-from-source Rust install.
**Updated:** The standalone repo is now DEPRECATED. Roblox has shifted to a **built-in MCP server that ships with Studio itself** (announced Feb-March 2026). This dramatically simplifies the MCP install story -- no Rust toolchain, no building from source. Users just need a current version of Studio.

### 2. boshyxd/robloxstudio-mcp (NEW DISCOVERY)

**Original:** Not mentioned at all.
**Updated:** This is the most feature-rich community MCP server with 39+ tools (v2.5.0), including read-only inspection mode, build export/import, script grep, and Creator Store integration. Studio plugin rewritten in TypeScript (roblox-ts). Actively maintained with recent DevForum presence. Should be the recommended community option over ZubeidHendricks.

### 3. drgost1/robloxstudio-mcp (NEW DISCOVERY)

**Original:** Not mentioned.
**Updated:** Another active community option with 51 tools. Works locally via HTTP polling with no external servers or data collection.

### 4. Solo Dev Agent Needs Update (CORRECTION)

**Original:** Listed only Game Developer and Game QA agents as needing updates.
**Updated:** Game Solo Dev (Indie) agent also mentions specific engines in its Identity line ("Expert in Unity, Unreal, and Godot") and needs "Roblox" added.

### 5. Rokit Toolchain Manager (ADDITION)

**Original:** Not mentioned as part of Roblox tooling setup.
**Updated:** Rokit is the recommended way to install all Roblox CLI tools (Rojo, Wally, Selene, StyLua, Lune). It should be the first install step in setup-roblox.md.

### 6. Roblox Future Vision: MCP Client

**Original:** Not mentioned.
**Updated:** Roblox announced at RDC 2025 that the native Studio Assistant will become an MCP client itself, enabling orchestration across third-party programs (e.g., design UI in Figma, import into Studio). This does not affect current implementation but is worth noting for future architecture decisions.

### 7. Game Developer Agent Also Missing Godot

**Original:** Noted Game Developer mentions "Unity, Unreal, and custom engines" and recommended adding Roblox.
**Updated:** Godot is also missing from this agent's engine list. The update should add both Godot and Roblox: "Unity, Unreal, Godot, Roblox, and custom engines."

---

## Sources

- [Roblox/studio-rust-mcp-server GitHub](https://github.com/Roblox/studio-rust-mcp-server)
- [studio-rust-mcp-server Releases](https://github.com/Roblox/studio-rust-mcp-server/releases)
- [DevForum: Introducing the Open Source Studio MCP Server](https://devforum.roblox.com/t/introducing-the-open-source-studio-mcp-server/3649365/print)
- [DevForum: Studio MCP Server Updates and External LLM Support](https://devforum.roblox.com/t/studio-mcp-server-updates-and-external-llm-support-for-assistant/4415631)
- [DevForum: Assistant Updates: Studio Built-in MCP Server](https://devforum.roblox.com/t/assistant-updates-studio-built-in-mcp-server-and-playtest-automation/4474643)
- [ZubeidHendricks/roblox-studio-mcp-claude-code GitHub](https://github.com/ZubeidHendricks/roblox-studio-mcp-claude-code)
- [boshyxd/robloxstudio-mcp GitHub](https://github.com/boshyxd/robloxstudio-mcp)
- [DevForum: Roblox Studio MCP v2.5.0](https://devforum.roblox.com/t/v250-roblox-studio-mcp-speed-up-your-workflow-by-letting-ai-read-paths-and-properties/3707071)
- [drgost1/robloxstudio-mcp GitHub](https://github.com/drgost1/robloxstudio-mcp)
- [Rojo GitHub](https://github.com/rojo-rbx/rojo)
- [Rojo Installation Docs](https://rojo.space/docs/v6/getting-started/installation/)
- [Wally GitHub](https://github.com/UpliftGames/wally)
- [Wally Registry](https://wally.run/)
- [Selene Releases](https://github.com/Kampfkarren/selene/releases)
- [StyLua GitHub](https://github.com/JohnnyMorganz/StyLua)
- [Lune GitHub](https://github.com/lune-org/lune)
- [Rokit GitHub](https://github.com/rojo-rbx/rokit)
- [LobeHub: Roblox Studio MCP Server](https://lobehub.com/mcp/roblox-studio-rust-mcp-server)
- [LobeHub: boshyxd MCP](https://lobehub.com/mcp/boshyxd-robloxstudio-mcp)
