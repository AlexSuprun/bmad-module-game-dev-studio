# Story 4.2: Create Roblox Setup Guide

Status: done

<!-- Note: Validate with validate-create-story before dev-story if quality check desired. -->

## Story

As a **developer starting a new Roblox project with BMGD**,
I want **a step-by-step setup guide for Roblox**,
so that **I can go from Roblox Studio installation to my first BMGD sprint with clear instructions**.

## Acceptance Criteria

1. **Given** `docs/how-to/setup-roblox.md` is created following the Diataxis how-to template, **When** a user reads the guide, **Then** the file contains all standard sections matching the structure of `setup-godot.md`:
   - YAML frontmatter (title, description)
   - "When to Use This Guide" (3 bullets)
   - "When to Skip This" (3–4 bullets with cross-references)
   - "Prerequisites" (BMad installed, Roblox Studio, basic Luau familiarity, game concept, optionally VS Code + Rojo extension)
   - Steps 1–8 (Step 1 has two sub-paths: Studio-only OR Professional with Rokit)
   - "What You Get" table (matching other guides)
   - "Roblox-Specific Considerations" section with 5 subsections
   - "Tips" (3 blockquote tips)
   - "Common Mistakes" (4-row table)
   - "Next Steps" (4 links)

2. **Given** Step 1 describes project creation, **When** a user chooses the Professional workflow path, **Then** Rokit is the first tool installed (manages Rojo, Wally, Selene, StyLua, Lune) before Rojo init and Wally init

3. **Given** the Roblox-Specific Considerations section exists, **When** a user reads it, **Then** it contains:
   - Project Structure: Rojo-based directory tree (`src/server/`, `src/client/`, `src/shared/`, `Packages/`)
   - Key Patterns: client-server split, ModuleScript organization, replication
   - Performance Budgets: 60 FPS target, mobile-first (60% of Roblox players), StreamingEnabled recommendation
   - Testing Setup: TestEZ via Wally (`UnitTesting/TestEZ@0.4.1`), Selene + StyLua
   - MCP Setup: built-in MCP (Roblox Studio 2026+) as primary, boshyxd/robloxstudio-mcp as community alternative

4. **And** the file passes `npm run lint:md` with zero errors

## Tasks / Subtasks

- [x] Task 1: Create `docs/how-to/setup-roblox.md` with full content (AC: #1, #2, #3, #4)
  - [x] Write YAML frontmatter (title: "Set up a Roblox project with BMGD", description)
  - [x] Write "When to Use This Guide" (3 bullets)
  - [x] Write "When to Skip This" (3–4 bullets matching other guide style)
  - [x] Write "Prerequisites" block quote
  - [x] Write Step 1 with two paths (Studio-only vs Professional/Rokit)
  - [x] Write Steps 2–8 (match godot guide commands and agent names)
  - [x] Write "What You Get" table
  - [x] Write "Roblox-Specific Considerations" with all 5 subsections
  - [x] Write "Tips" (3 blockquote tips)
  - [x] Write "Common Mistakes" (4-row table)
  - [x] Write "Next Steps" (4 links)

- [x] Task 2: Run `npm run lint:md` and fix any errors (AC: #4)

## Dev Notes

### Target File

**Single file to create:** `docs/how-to/setup-roblox.md`

No other files are modified in this story. Story 4.3 handles `docs/index.md` and `docs/how-to/index.md`.

### Template Reference — Follow `setup-godot.md` Exactly

The closest structural template is `docs/how-to/setup-godot.md`. Follow its:
- Heading levels (H2 for major sections, H3 for subsections)
- Blockquote style for Prerequisites and Tips (`> **Before starting:**`)
- HR separators (`---`) between every major section
- Code block style for commands (plain code fences, no language tag for slash commands)
- Table format for "What You Get" and "Common Mistakes"

**Do NOT add YAML frontmatter to the knowledge files.** The setup-roblox.md IS the how-to guide (not a knowledge file), so frontmatter IS required — same as setup-godot.md.

### BMGD Agent Names (Exact — from setup-godot.md and other guides)

| Agent | Name |
|-------|------|
| Game Architect | Cloud Dragonborn |
| Game Designer | Samus Shepard |
| Game Scrum Master | Max |
| Game Developer | Link Freeman |
| Game QA | GLaDOS |

### BMGD Slash Commands (Exact — match existing guides)

| Purpose | Command |
|---------|---------|
| Generate project context | `/bmgd-generate-project-context` |
| Brainstorm game | `/bmgd-brainstorm-game` |
| Create game brief | `/bmgd-game-brief` |
| Create GDD | `/bmgd-create-gdd` |
| Create architecture | `/bmgd-create-architecture` |
| Sprint planning | `/bmgd-sprint-planning` |
| Dev story | `/bmgd-dev-story [story-name]` |
| Test framework | `/bmgd-test-framework` |

### Step 1: Two Paths Pattern

Step 1 must offer two distinct paths. Use numbered sub-lists or sub-headings:

**Path A — Studio Only (beginner/simple games):**
1. Open Roblox Studio
2. Select a template (Baseplate, Obby, etc.)
3. Save and publish the place

**Path B — Professional Workflow (recommended for production):**
1. Install Rokit (manages Rojo, Wally, Selene, StyLua, Lune): `rokit install` or refer to `github.com/rojo-rbx/rokit`
2. Install Rojo (Roblox Studio ↔ filesystem sync): `rokit add rojo-rbx/rojo`
3. Initialize Rojo project: `rojo init my-project`
4. Install Wally (package manager): `rokit add UnitTesting/TestEZ`
5. Open the generated place in Roblox Studio via Rojo

**Important:** Rokit must come FIRST. It is the toolchain manager that installs Rojo, Wally, Selene, StyLua, and Lune.

### Roblox-Specific Considerations — All 5 Subsections

#### 1. Project Structure (Rojo-based directory tree)

```
my-project/
├── src/
│   ├── server/          ← ServerScripts (run on server only)
│   ├── client/          ← LocalScripts (run on client only)
│   └── shared/          ← ModuleScripts (replicated to both)
├── Packages/            ← Wally packages (auto-generated)
├── default.project.json ← Rojo project config
├── wally.toml           ← Wally package manifest
└── .selene.toml         ← Selene linter config
```

#### 2. Key Patterns

- **Client-Server Split:** Never trust the client. All authoritative game logic (damage, currency, state) runs on the server. Clients only handle input and visual feedback.
- **ModuleScript Organization:** Shared logic lives in `src/shared/`. Use `require()` for module imports. Avoid circular dependencies.
- **Replication:** Use `RemoteEvent` (fire-and-forget) and `RemoteFunction` (request-response) in `src/shared/Remotes/`. Server fires to client for UI updates; client fires to server for actions.

#### 3. Performance Budgets

- **60 FPS** target for desktop; mobile targets 30 FPS minimum
- **Mobile-first:** ~60% of Roblox players are on mobile — test on low-end devices
- **StreamingEnabled:** Enable for large worlds to load terrain/models on demand (set in Workspace properties)
- **Frame budget:** 16.6ms per frame at 60 FPS

#### 4. Testing Setup

```
# Add TestEZ via Wally (wally.toml)
[dev-dependencies]
TestEZ = "UnitTesting/TestEZ@^0.4.1"
```

- **Selene** (static analysis): `rokit add Kampfkarren/selene` → configure `.selene.toml`
- **StyLua** (formatter): `rokit add JohnnyMorganz/StyLua` → configure `stylua.toml`
- Run linting: `selene src/` and `stylua --check src/`

For full QA guidance, run `/bmgd-test-framework` with the **Game QA agent (GLaDOS)**.

#### 5. MCP Setup

Two options for Roblox Studio AI integration:

| Option | Tool | When to Use |
|--------|------|-------------|
| **Built-in (recommended)** | Roblox Studio Built-in MCP Server | Roblox Studio 2026+, ships with Studio |
| **Community alternative** | boshyxd/robloxstudio-mcp v2.5.0 (39+ tools) | Older Studio or need extra tools |

The built-in MCP server is enabled in Roblox Studio settings — no separate install. The community MCP (`boshyxd/robloxstudio-mcp`) installs via the Creator Store plugin.

### Tips Content (3 Tips)

1. Always run `/bmgd-generate-project-context` after Step 1. The `project-context.md` is the single source of truth that all BMGD agents reference — every subsequent workflow depends on it.
2. If using the Professional workflow, commit your Rojo project to git immediately. Roblox Studio saves are binary `.rbxl` files; Rojo's filesystem structure is the human-readable source of truth.
3. Choose your path early: Studio-only is great for game jams and learning, but migrating a Studio-only project to Rojo later is non-trivial. If your game will grow beyond a single session, use the Professional path from the start.

### Common Mistakes (4-Row Table)

| Mistake | Solution |
|---------|----------|
| Skipping project-context generation | Always generate `project-context.md` first — it guides all other workflows |
| Putting game logic in LocalScripts | Server-authoritative design: all game state (currency, health, inventory) must live on the server |
| Installing Rojo before Rokit | Install Rokit first — it manages all Roblox toolchain versions including Rojo and Wally |
| Using deprecated packages as primary | Use ProfileStore (not ProfileService), ByteNet (not BridgeNet2), React-Lua (not Roact) — see the architecture workflow output |

### Next Steps Links (4 Links)

Follow the style from setup-godot.md:

```markdown
- **[Quick Flow vs Full Production](../explanation/quick-flow-vs-full.md)** — Understand both development approaches
- **[Set up Godot with BMGD](./setup-godot.md)** — If you're considering Godot instead
- **[Run sprint planning](./sprint-planning.md)** — When you're ready to start building
- **[Agents Reference](../reference/agents.md)** — Learn about all 6 BMGD agents
```

### Anti-Patterns to Avoid

| DO NOT | DO INSTEAD | REASON |
|--------|-----------|--------|
| Add Roblox to `docs/index.md` | Leave `docs/index.md` alone | Story 4.3 handles that file |
| Add Roblox to `docs/how-to/index.md` | Leave `docs/how-to/index.md` alone | Story 4.3 handles that file |
| Recommend ProfileService as primary data solution | Use ProfileStore | ProfileService is unmaintained; ProfileStore is the successor by the same author |
| Recommend BridgeNet2 | Use ByteNet | BridgeNet2 is archived |
| Recommend Roact | Use React-Lua | Roact is deprecated |
| Recommend standalone `studio-rust-mcp-server` as primary | Use built-in MCP (Studio 2026+) | The deprecated standalone MCP is not the current recommendation |
| Use `npm run lint` | Use `npm run lint:md` | `npm run lint` is YAML-only; markdown requires `lint:md` |

### Lint Command

```bash
npm run lint:md   # markdown linting — NOT npm run lint (YAML only)
```

### Architecture Context from Step 6

When describing Step 6 (Create technical architecture), mention that the Roblox architecture output will include:
- Service directory structure (Rojo-based)
- Client-server boundary decisions
- RemoteEvent/RemoteFunction layout
- Roblox-specific package recommendations (ProfileStore, ByteNet, React-Lua or Fusion)
- Performance budgets and StreamingEnabled configuration

This is important context that differs from Unity/Godot architecture output — makes the guide Roblox-specific rather than generic.

### Previous Story Intelligence (4.1)

- Story 4.1 was purely text updates to 3 agent SKILL.md files — no file creation.
- All Roblox knowledge base is complete: `roblox-engine.md`, `roblox-testing.md`, decision catalog, architecture patterns, and MCP entries were added in Epics 1–3.
- From Story 3.1: lint command is `npm run lint:md` (confirmed again in 4.1).
- From Story 4.1: scope discipline is critical — only change the listed file; 4.3 handles docs/index.md and docs/how-to/index.md.

### Git Context

Relevant commits showing Roblox content now available for reference in the guide:
- `73e917a` — Story 3.1: roblox-testing.md, qa-index.csv (TestEZ, MCP testing)
- `7feb639` — Story 2.3: architecture-patterns.yaml (roblox_experience, roblox_multiplayer patterns)
- `e079a2f` — Story 2.2: roblox_standard/roblox_simple stacks, Rojo templates
- `2d06401` — Story 2.1: 38 Roblox entries in decision-catalog.yaml
- `176b715` — Story 1.3: engine-mcps.yaml (built-in MCP + boshyxd community MCP)
- `717136a` / `1d02793` — Story 1.2: roblox-engine.md (full 12-section knowledge file)

### Scope Boundary

**One file only:** `docs/how-to/setup-roblox.md`

Do NOT modify:
- `docs/index.md` — handled by Story 4.3
- `docs/how-to/index.md` — handled by Story 4.3
- Any YAML files, CSV files, or knowledge files
- Any agent SKILL.md files (done in Story 4.1)

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

None.

### Completion Notes List

- Created `docs/how-to/setup-roblox.md` following `setup-godot.md` structure exactly (YAML frontmatter, H2/H3 heading levels, blockquote style, HR separators, code fence style, table format)
- Step 1 implements two distinct paths: Path A (Studio Only) and Path B (Professional with Rokit first, then Rojo, Wally)
- All 5 Roblox-Specific Considerations subsections included: Project Structure (Rojo directory tree), Key Patterns (client-server split, ModuleScript, replication), Performance Budgets (60 FPS, mobile-first, StreamingEnabled), Testing Setup (TestEZ via Wally, Selene, StyLua), MCP Setup (built-in Studio 2026+ vs boshyxd community)
- 3 blockquote Tips, 4-row Common Mistakes table, 4 Next Steps links
- All agent names and slash commands match story Dev Notes exactly
- `npm run lint:md` passes with 0 errors

### File List

- docs/how-to/setup-roblox.md

### Review Findings

- [x] [Review][Decision] Step 1 Path B "Initialize Wally" label and command are incoherent — fixed: split into `rokit add UnitTesting/Wally` + `wally init`; TestEZ install remains in Testing Setup section only [docs/how-to/setup-roblox.md]
- [x] [Review][Decision] `rokit install` command assumes Rokit is already installed — fixed: reordered to show external install link first, then `rokit install` [docs/how-to/setup-roblox.md]
- [x] [Review][Patch] "When to Skip This" bullets 1 and 3 are nearly identical — fixed: consolidated into 3 distinct bullets with cross-references [docs/how-to/setup-roblox.md:21-27]
- [x] [Review][Patch] "When to Skip This" Godot/Unity bullet missing hyperlinks — fixed: added links to setup-godot.md and setup-unity.md [docs/how-to/setup-roblox.md:25]
- [x] [Review][Patch] StyLua config filename wrong: guide says `stylua.toml` but StyLua's actual default config is `.stylua.toml` (dot-prefixed) — fixed [docs/how-to/setup-roblox.md:~240]
- [x] [Review][Patch] TestEZ wally.toml version uses `^0.4.1` (semver range) — fixed: exact version `0.4.1` [docs/how-to/setup-roblox.md:~226]
- [x] [Review][Defer] Missing Roblox Studio version requirement in prerequisites — pre-existing gap; Roblox Studio auto-updates so pinning a version is non-trivial
- [x] [Review][Defer] No Rojo plugin connection troubleshooting — deep troubleshooting out of scope for a how-to guide
- [x] [Review][Defer] No Studio-Only → Professional workflow migration path — migration complexity out of scope for this story
- [x] [Review][Defer] MCP Setup section lacks step-by-step enablement instructions for both options — guide-level detail out of scope; consideration section not a tutorial
- [x] [Review][Defer] No failure recovery for BMad slash commands (Steps 2–8) — error handling out of scope for setup guide format
