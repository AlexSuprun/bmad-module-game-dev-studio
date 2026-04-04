# Development Guide

**Generated:** 2026-04-02

## Prerequisites

- **Node.js** >= 22.0.0
- **npm** (included with Node.js)
- **Git** (for pre-commit hooks)

## Installation

```bash
git clone https://github.com/bmad-code-org/bmad-module-game-dev-studio.git
cd bmad-module-game-dev-studio
npm install
```

This installs dev dependencies and sets up Husky pre-commit hooks automatically (`prepare` script).

## Project Scripts

| Command | Purpose |
|---------|---------|
| `npm test` | Run all quality checks (lint + markdown lint + format check) |
| `npm run lint` | ESLint on JS/YAML files |
| `npm run lint:fix` | ESLint with auto-fix |
| `npm run lint:md` | markdownlint on all `.md` files |
| `npm run format:check` | Prettier check on JS/JSON/YAML |
| `npm run format:fix` | Prettier auto-format |
| `npm run docs:dev` | Start Astro dev server for docs site |
| `npm run docs:build` | Build docs site via `tools/build-docs.mjs` |
| `npm run docs:preview` | Preview built docs site |
| `npm run release` | Patch version bump + push tags |
| `npm run release:minor` | Minor version bump |
| `npm run release:major` | Major version bump |

## Pre-Commit Hooks

Husky + lint-staged runs automatically on `git commit`:

- **JS/MJS files**: ESLint fix + Prettier fix
- **YAML files**: ESLint fix + Prettier fix
- **JSON files**: Prettier fix
- **Markdown files**: markdownlint check

## Documentation Site

The docs site uses **Astro + Starlight** and lives in `website/`. Content comes from `docs/` (Diataxis structure: tutorials, how-to, explanation, reference).

```bash
npm run docs:dev      # Local dev server
npm run docs:build    # Production build
npm run docs:preview  # Preview production build
```

## CI/CD Pipelines

| Workflow | File | Trigger |
|----------|------|---------|
| Quality | `.github/workflows/quality.yaml` | PR / push |
| Docs | `.github/workflows/docs.yaml` | Push to main |
| Discord | `.github/workflows/discord.yaml` | Release events |
| Manual Release | `.github/workflows/manual-release.yaml` | Manual trigger |

## Adding a New Engine

The module is designed for multi-engine extensibility. To add a new engine (e.g., Roblox):

1. Create `src/workflows/3-technical/gds-game-architecture/knowledge/{engine}-engine.md`
2. Create `src/gametest/knowledge/{engine}-testing.md`
3. Update `src/module.yaml` — add engine to `primary_platform` multi-select
4. Update `decision-catalog.yaml` — add engine options under categories
5. Update `engine-mcps.yaml` — add engine MCP server block
6. Update `architecture-patterns.yaml` — add engine-specific patterns
7. Update `qa-index.csv` — add testing knowledge row
8. Update relevant agent `SKILL.md` files to mention the engine
9. Create `docs/how-to/setup-{engine}.md`

## Contributing

See [CONTRIBUTING.md](../CONTRIBUTING.md) for the full contribution process. Key points:

- PRs should be under 800 lines
- All YAML must pass linting (`npm run lint`)
- All markdown must pass linting (`npm run lint:md`)
- Discuss new features on Discord first
