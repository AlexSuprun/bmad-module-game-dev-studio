---
title: "Set up a Roblox project with BMGD"
description: Configure a new Roblox project for full production game development with BMGD
---

# Set up a Roblox project with BMGD

Configure a new Roblox project with BMGD workflows for full production game development.

---

## When to Use This Guide

- You're starting a new Roblox experience or game project
- You want to use BMGD's Full Production workflow with Roblox
- You need formal documentation (GDD, architecture, sprint tracking)

---

## When to Skip This

- You just want to prototype quickly or do a game jam — use [Quick Flow](../tutorials/first-game-project.md) instead
- You're building with Godot — see [Set up Godot with BMGD](./setup-godot.md)
- You're building with Unity — see [Set up Unity with BMGD](./setup-unity.md)

---

## Prerequisites

> **Before starting:**
> - BMad Method installed with BMGD module enabled
> - Roblox Studio installed (free from roblox.com)
> - Basic familiarity with Luau (Roblox's scripting language)
> - A game concept or idea you want to develop
> - (Optional for Professional workflow) VS Code with the Rojo extension

---

## Steps

### Step 1: Create your Roblox project

Choose one of two paths depending on your project needs:

**Path A — Studio Only (beginner / simple games):**

1. Open **Roblox Studio**
2. Select a template (Baseplate, Obby, etc.)
3. Save and publish the place to your Roblox account

**Path B — Professional Workflow (recommended for production):**

1. Install **Rokit** — the toolchain manager that installs and versions Rojo, Wally, Selene, StyLua, and Lune.

   First, install Rokit itself by following the instructions at `github.com/rojo-rbx/rokit`. Then run in your project directory:

   ```
   rokit install
   ```

2. Add **Rojo** (Roblox Studio ↔ filesystem sync) via Rokit:

   ```
   rokit add rojo-rbx/rojo
   ```

3. Initialize your Rojo project:

   ```
   rojo init my-project
   cd my-project
   ```

4. Install and initialize **Wally** (Roblox package manager):

   ```
   rokit add UnitTesting/Wally
   wally init
   ```

5. Open the generated place file in Roblox Studio via Rojo:

   ```
   rojo serve
   ```

   Then connect from the Rojo plugin inside Roblox Studio.

> **Important:** Rokit must come first. It is the toolchain manager that installs and manages versions of Rojo, Wally, Selene, StyLua, and Lune.

### Step 2: Generate your project context

BMGD uses a `project-context.md` file to maintain consistency across all workflows.

In your BMad-enabled environment at the project root:

```
/bmgd-generate-project-context
```

This invokes the **Game Architect (Cloud Dragonborn)** to create a `project-context.md` file that includes:

- Project name and description
- Target platforms (PC, mobile, console)
- Roblox-specific settings (StreamingEnabled, mobile targets)
- Performance budgets
- Critical technical decisions

### Step 3: Run the brainstorming workflow

Define your game concept with the Game Designer agent.

```
/bmgd-brainstorm-game
```

The **Game Designer (Samus Shepard)** will guide you through:

- Selecting and combining brainstorming techniques
- Generating and refining game ideas
- Choosing a concept to develop

### Step 4: Create your Game Brief

Capture your vision and positioning.

```
/bmgd-game-brief
```

The Game Designer creates `game-brief.md` with:

- Game vision and elevator pitch
- Target audience and market positioning
- Platform and genre decisions
- Competitive analysis
- Art and audio direction

### Step 5: Design your game (GDD)

Create a comprehensive Game Design Document.

```
/bmgd-create-gdd
```

The Game Designer helps you:

- Select your game type from 24 available templates
- Define core gameplay mechanics
- Design progression systems
- Plan levels and content
- Specify art and audio requirements

Output: `gdd.md`

### Step 6: Create your technical architecture

Plan your Roblox project structure and systems.

```
/bmgd-create-architecture
```

The **Game Architect (Cloud Dragonborn)** creates `architecture.md` with:

- Service directory structure (Rojo-based)
- Client-server boundary decisions
- RemoteEvent/RemoteFunction layout
- Roblox-specific package recommendations (ProfileStore, ByteNet, React-Lua or Fusion)
- Performance budgets and StreamingEnabled configuration

### Step 7: Plan your first sprint

Ready to start building? Use the Game Scrum Master to plan your work.

```
/bmgd-sprint-planning
```

The **Game Scrum Master (Max)** creates:

- `sprint-status.yaml` — Your sprint tracking file
- Stories from your GDD and Architecture
- Sprint goals and definition of done

### Step 8: Start implementing

Build features with the Game Developer agent.

```
/bmgd-dev-story [story-name]
```

The **Game Developer (Link Freeman)** helps you:

- Implement story tasks with Luau
- Follow Roblox best practices (client-server split, RemoteEvent patterns)
- Write tests for your features
- Review code before marking complete

---

## What You Get

After completing this setup, you'll have:

| File/Folder | Purpose |
|------------|---------|
| `project-context.md` | AI context for consistency across all BMGD workflows |
| `game-brief.md` | Your game's vision and positioning |
| `gdd.md` | Complete game design document |
| `architecture.md` | Technical architecture and Roblox-specific patterns |
| `sprint-status.yaml` | Sprint tracking with stories and progress |
| `stories/` | Folder containing individual story files |

---

## Roblox-Specific Considerations

### Project Structure

For the Professional workflow, BMGD recommends this Rojo-based directory structure:

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

### Key Patterns

- **Client-Server Split:** Never trust the client. All authoritative game logic (damage, currency, state) runs on the server. Clients only handle input and visual feedback.
- **ModuleScript Organization:** Shared logic lives in `src/shared/`. Use `require()` for module imports. Avoid circular dependencies.
- **Replication:** Use `RemoteEvent` (fire-and-forget) and `RemoteFunction` (request-response) in `src/shared/Remotes/`. Server fires to client for UI updates; client fires to server for actions.

### Performance Budgets

- **60 FPS** target for desktop; mobile targets 30 FPS minimum
- **Mobile-first:** ~60% of Roblox players are on mobile — test on low-end devices
- **StreamingEnabled:** Enable for large worlds to load terrain and models on demand (set in Workspace properties)
- **Frame budget:** 16.6ms per frame at 60 FPS

### Testing Setup

Add TestEZ via Wally by adding to your `wally.toml`:

```
[dev-dependencies]
TestEZ = "UnitTesting/TestEZ@0.4.1"
```

Then run `wally install` to install dependencies.

- **Selene** (static analysis): `rokit add Kampfkarren/selene` → configure `.selene.toml`
- **StyLua** (formatter): `rokit add JohnnyMorganz/StyLua` → configure `.stylua.toml`
- Run linting: `selene src/` and `stylua --check src/`

For full QA guidance, run `/bmgd-test-framework` with the **Game QA agent (GLaDOS)**.

### MCP Setup

Two options for Roblox Studio AI integration:

| Option | Tool | When to Use |
|--------|------|-------------|
| **Built-in (recommended)** | Roblox Studio Built-in MCP Server | Roblox Studio 2026+, ships with Studio |
| **Community alternative** | boshyxd/robloxstudio-mcp v2.5.0 (39+ tools) | Older Studio or need extra tools |

The built-in MCP server is enabled in Roblox Studio settings — no separate install. The community MCP (`boshyxd/robloxstudio-mcp`) installs via the Creator Store plugin.

---

## Tips

> **Best Practice:** Always run `/bmgd-generate-project-context` after Step 1. The `project-context.md` is the single source of truth that all BMGD agents reference — every subsequent workflow depends on it.

> **Version Control:** If using the Professional workflow, commit your Rojo project to git immediately. Roblox Studio saves are binary `.rbxl` files; Rojo's filesystem structure is the human-readable source of truth.

> **Choose Your Path Early:** Studio-only is great for game jams and learning, but migrating a Studio-only project to Rojo later is non-trivial. If your game will grow beyond a single session, use the Professional path from the start.

---

## Common Mistakes

| Mistake | Solution |
|---------|----------|
| Skipping project-context generation | Always generate `project-context.md` first — it guides all other workflows |
| Putting game logic in LocalScripts | Server-authoritative design: all game state (currency, health, inventory) must live on the server |
| Installing Rojo before Rokit | Install Rokit first — it manages all Roblox toolchain versions including Rojo and Wally |
| Using deprecated packages as primary | Use ProfileStore (not ProfileService), ByteNet (not BridgeNet2), React-Lua (not Roact) — see the architecture workflow output |

---

## Next Steps

- **[Quick Flow vs Full Production](../explanation/quick-flow-vs-full.md)** — Understand both development approaches
- **[Set up Godot with BMGD](./setup-godot.md)** — If you're considering Godot instead
- **[Run sprint planning](./sprint-planning.md)** — When you're ready to start building
- **[Agents Reference](../reference/agents.md)** — Learn about all 6 BMGD agents
