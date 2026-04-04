# Story 4.3: Update Documentation Site Index with Roblox

Status: done

## Story

As a **BMGD user browsing the documentation site**,
I want **Roblox listed as a supported engine with a link to its setup guide**,
so that **I can discover Roblox support and navigate to the setup instructions**.

## Acceptance Criteria

1. **Given** `docs/index.md` is updated, **When** a user views the documentation homepage, **Then** Roblox appears in the "Supported Engines" bullet list matching the style of existing engine entries:
   - `**[Roblox](https://www.roblox.com/create)** — Luau scripting, massive multiplayer platform`

2. **Given** `docs/index.md` is updated, **When** a user views the Quick Start table, **Then** a Roblox row is present:
   - `[Set up Roblox](how-to/setup-roblox.md)` | `Configure Roblox for BMGD`

3. **Given** `docs/how-to/index.md` is updated, **When** a user views the Engine Setup Guides section, **Then** Roblox appears:
   - `**[Set up a Roblox project with BMGD](./setup-roblox.md)** — Configure Roblox for full production development`

4. **And** both files pass `npm run lint:md` with zero errors

## Tasks / Subtasks

- [x] Task 1: Update `docs/index.md` (AC: #1, #2, #4)
  - [x] Add `**[Roblox](https://www.roblox.com/create)** — Luau scripting, massive multiplayer platform` to the Supported Engines bullet list (after Godot, before "But you're not limited…")
  - [x] Add `| [Set up Roblox](how-to/setup-roblox.md) | Configure Roblox for BMGD |` to the Quick Start table (after the Godot row)

- [x] Task 2: Update `docs/how-to/index.md` (AC: #3, #4)
  - [x] Add `**[Set up a Roblox project with BMGD](./setup-roblox.md)** — Configure Roblox for full production development` to the Engine Setup Guides section (after Godot entry)

- [x] Task 3: Run `npm run lint:md` and fix any errors (AC: #4)

## Dev Notes

### Files to Modify (Exactly Two)

| File | Change |
|------|--------|
| `docs/index.md` | Add Roblox to Supported Engines list + Quick Start table |
| `docs/how-to/index.md` | Add Roblox to Engine Setup Guides list |

**Do NOT modify any other files.** `docs/how-to/setup-roblox.md` was created in Story 4.2 and is already complete.

### Exact Edits — `docs/index.md`

#### Current Supported Engines section (lines 13–21):

```markdown
## Supported Engines

BMGD has first-class support for:

- **[Unity](https://unity.com)** — C# scripting, industry-standard for many game types
- **[Unreal Engine](https://www.unrealengine.com/)** — Blueprint and C++, AAA powerhouse
- **[Godot](https://godotengine.org/)** — Open-source, GDScript, rapidly growing

But you're not limited to these—BMGD works with any platform.
```

#### After edit — add Roblox entry after Godot:

```markdown
## Supported Engines

BMGD has first-class support for:

- **[Unity](https://unity.com)** — C# scripting, industry-standard for many game types
- **[Unreal Engine](https://www.unrealengine.com/)** — Blueprint and C++, AAA powerhouse
- **[Godot](https://godotengine.org/)** — Open-source, GDScript, rapidly growing
- **[Roblox](https://www.roblox.com/create)** — Luau scripting, massive multiplayer platform

But you're not limited to these—BMGD works with any platform.
```

#### Current Quick Start table (lines 38–44):

```markdown
| Workflow | Purpose |
|----------|---------|
| [Your first game project](tutorials/first-game-project.md) | Create your first prototype in 15-30 minutes |
| [Quick Flow vs Full Production](explanation/quick-flow-vs-full.md) | Choose your development approach |
| [Set up Unity](how-to/setup-unity.md) | Configure Unity for BMGD |
| [Set up Unreal](how-to/setup-unreal.md) | Configure Unreal for BMGD |
| [Set up Godot](how-to/setup-godot.md) | Configure Godot for BMGD |
```

#### After edit — add Roblox row after Godot:

```markdown
| Workflow | Purpose |
|----------|---------|
| [Your first game project](tutorials/first-game-project.md) | Create your first prototype in 15-30 minutes |
| [Quick Flow vs Full Production](explanation/quick-flow-vs-full.md) | Choose your development approach |
| [Set up Unity](how-to/setup-unity.md) | Configure Unity for BMGD |
| [Set up Unreal](how-to/setup-unreal.md) | Configure Unreal for BMGD |
| [Set up Godot](how-to/setup-godot.md) | Configure Godot for BMGD |
| [Set up Roblox](how-to/setup-roblox.md) | Configure Roblox for BMGD |
```

### Exact Edits — `docs/how-to/index.md`

#### Current Engine Setup Guides section (lines 13–18):

```markdown
## Engine Setup Guides

Get started with your preferred game engine:

- **[Set up a Unity project with BMGD](./setup-unity.md)** — Configure Unity for full production development
- **[Set up an Unreal project with BMGD](./setup-unreal.md)** — Configure Unreal for full production development
- **[Set up a Godot project with BMGD](./setup-godot.md)** — Configure Godot for full production development
```

#### After edit — add Roblox entry after Godot:

```markdown
## Engine Setup Guides

Get started with your preferred game engine:

- **[Set up a Unity project with BMGD](./setup-unity.md)** — Configure Unity for full production development
- **[Set up an Unreal project with BMGD](./setup-unreal.md)** — Configure Unreal for full production development
- **[Set up a Godot project with BMGD](./setup-godot.md)** — Configure Godot for full production development
- **[Set up a Roblox project with BMGD](./setup-roblox.md)** — Configure Roblox for full production development
```

### Lint Command

```bash
npm run lint:md   # markdown linting — NOT npm run lint (YAML-only)
```

### Anti-Patterns to Avoid

| DO NOT | DO INSTEAD | REASON |
|--------|-----------|--------|
| Modify `docs/how-to/setup-roblox.md` | Leave it alone | Created and complete in Story 4.2 |
| Modify any YAML or CSV files | Leave them alone | All data files done in Epics 1–3 |
| Modify agent SKILL.md files | Leave them alone | Done in Story 4.1 |
| Use `npm run lint` | Use `npm run lint:md` | `npm run lint` is YAML-only; markdown requires `lint:md` |
| Change the frontmatter `description` in index.md | Leave it as-is | AC does not require it; keep changes minimal |

### Previous Story Intelligence (4.2)

- Story 4.2 created `docs/how-to/setup-roblox.md` — it already exists and is complete.
- Story 4.2 explicitly scoped OUT `docs/index.md` and `docs/how-to/index.md`, noting "Story 4.3 handles those files."
- Lint command confirmed: `npm run lint:md` for markdown.
- Scope discipline is critical in this epic — only change the two listed files.

### Context: Existing Engine Entry Style

From `docs/index.md` Supported Engines list — match this exact pattern:

```
- **[EngineName](URL)** — Short description phrase
```

From `docs/how-to/index.md` Engine Setup Guides list — match this exact pattern:

```
- **[Set up a {Engine} project with BMGD](./setup-{engine}.md)** — Configure {Engine} for full production development
```

### References

- Story 4.2 dev notes: `docs/how-to/setup-roblox.md` — created at that path, confirmed passing lint
- Epic 4 FR15: "Update `docs/index.md` to list Roblox in supported engines section and add setup guide link"
- `docs/index.md` current state: lines 16–18 (Supported Engines bullets), lines 38–44 (Quick Start table)
- `docs/how-to/index.md` current state: lines 16–18 (Engine Setup Guides bullets)

## Dev Agent Record

### Agent Model Used

claude-sonnet-4-6

### Debug Log References

### Completion Notes List

- Added Roblox to Supported Engines bullet list in `docs/index.md` after Godot entry
- Added Roblox row to Quick Start table in `docs/index.md` after Godot row
- Added Roblox to Engine Setup Guides list in `docs/how-to/index.md` after Godot entry
- `npm run lint:md` passed with 0 errors across 369 files

### File List

- `docs/index.md`
- `docs/how-to/index.md`

### Review Findings

- [x] [Review][Defer] Site taglines and README omit Roblox (`website/astro.config.mjs` tagline, `README.md` supported engines section) — deferred, pre-existing
- [x] [Review][Defer] `project-overview.md` engine support table may be inconsistent with completed Roblox stories — deferred, pre-existing
- [x] [Review][Defer] `first-game-project.md` tutorial next-steps omits Roblox setup guide link — deferred, pre-existing
- [x] [Review][Defer] Reference docs cross-link sections omit Roblox alongside Unity/Unreal/Godot — deferred, pre-existing
