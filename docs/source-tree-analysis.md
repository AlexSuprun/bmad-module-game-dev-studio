# Source Tree Analysis

**Generated:** 2026-04-02

## Annotated Directory Tree

```
bmad-module-game-dev-studio/
├── _bmad/                          # BMad Method installation (core + module config)
│   ├── core/                       # BMad core skills (brainstorming, reviews, etc.)
│   └── bmm/                        # BMad Method Module workflows
│       ├── config.yaml             # Module configuration (project paths, user settings)
│       ├── 1-analysis/             # Research workflows (technical, domain)
│       ├── 2-plan-workflows/       # Planning workflows (PRD, UX, agents)
│       └── 3-solutioning/          # Architecture, epics, project context
│
├── src/                            # ★ MODULE SOURCE — the BMGD module content
│   ├── module.yaml                 # ★ ENTRY POINT — Install-time config (engine selection)
│   ├── module-help.csv             # Help topics for installed module
│   │
│   ├── agents/                     # 7 game development AI agents
│   │   ├── gds-agent-game-architect/
│   │   │   ├── SKILL.md            # Agent persona, expertise, behavior
│   │   │   └── bmad-skill-manifest.yaml
│   │   ├── gds-agent-game-designer/
│   │   ├── gds-agent-game-dev/
│   │   ├── gds-agent-game-qa/
│   │   ├── gds-agent-game-scrum-master/
│   │   ├── gds-agent-game-solo-dev/
│   │   └── gds-agent-tech-writer/  # Also has templates/ and resources/
│   │
│   ├── workflows/                  # Game dev workflow phases
│   │   ├── 1-preproduction/        # Brainstorm, Game Brief, Research
│   │   │   ├── gds-brainstorm-game/
│   │   │   ├── gds-create-game-brief/
│   │   │   └── research/
│   │   │       └── gds-domain-research/
│   │   │
│   │   ├── 2-design/               # Design phase workflows
│   │   │   ├── create-prd/
│   │   │   ├── gds-create-gdd/     # Game Design Document
│   │   │   ├── gds-create-narrative/
│   │   │   └── gds-create-ux-design/
│   │   │
│   │   ├── 3-technical/            # ★ CRITICAL — Architecture & engine knowledge
│   │   │   ├── gds-game-architecture/
│   │   │   │   ├── architecture-patterns.yaml  # Pattern triggers & stacks
│   │   │   │   ├── decision-catalog.yaml       # ★ 14 categories, engine options
│   │   │   │   ├── engine-mcps.yaml            # MCP server configs per engine
│   │   │   │   └── knowledge/                  # ★ ENGINE KNOWLEDGE FILES
│   │   │   │       ├── unity-engine.md
│   │   │   │       ├── unreal-engine.md
│   │   │   │       ├── godot-engine.md
│   │   │   │       └── phaser-engine.md
│   │   │   ├── gds-create-epics-and-stories/
│   │   │   ├── gds-check-implementation-readiness/
│   │   │   └── gds-generate-project-context/
│   │   │
│   │   ├── 4-production/           # Sprint execution workflows
│   │   │   ├── gds-sprint-planning/
│   │   │   ├── gds-sprint-status/
│   │   │   ├── gds-create-story/
│   │   │   ├── gds-dev-story/
│   │   │   ├── gds-code-review/
│   │   │   ├── gds-correct-course/
│   │   │   └── gds-retrospective/
│   │   │
│   │   ├── gametest/               # QA and testing workflows
│   │   │   ├── gds-e2e-scaffold/
│   │   │   ├── gds-performance-test/
│   │   │   ├── gds-playtest-plan/
│   │   │   ├── gds-test-automate/
│   │   │   ├── gds-test-design/
│   │   │   ├── gds-test-framework/
│   │   │   └── gds-test-review/
│   │   │
│   │   ├── gds-document-project/   # Project documentation workflow
│   │   └── gds-quick-flow/         # Rapid prototyping
│   │       ├── gds-quick-dev/
│   │       ├── gds-quick-dev-new-preview/
│   │       └── gds-quick-spec/
│   │
│   └── gametest/                   # ★ TESTING KNOWLEDGE BASE
│       ├── qa-index.csv            # ★ Index mapping test types to knowledge files
│       └── knowledge/              # 17 testing knowledge files
│           ├── unity-testing.md    # Engine-specific
│           ├── unreal-testing.md   # Engine-specific
│           ├── godot-testing.md    # Engine-specific
│           ├── playtesting.md      # Generic
│           ├── qa-automation.md    # Generic
│           ├── performance-testing.md
│           ├── balance-testing.md
│           ├── e2e-testing.md
│           ├── multiplayer-testing.md
│           ├── save-testing.md
│           ├── input-testing.md
│           ├── localization-testing.md
│           ├── compatibility-testing.md
│           ├── certification-testing.md
│           ├── regression-testing.md
│           ├── smoke-testing.md
│           └── test-priorities.md
│
├── docs/                           # Documentation site content (Diataxis)
│   ├── index.md                    # Site homepage
│   ├── tutorials/                  # Getting started guides
│   │   └── first-game-project.md
│   ├── how-to/                     # Engine setup guides
│   │   ├── setup-unity.md
│   │   ├── setup-unreal.md
│   │   ├── setup-godot.md
│   │   └── sprint-planning.md
│   ├── explanation/                # Concept explainers
│   │   ├── bmgd-vs-bmm.md
│   │   ├── game-types.md
│   │   └── quick-flow-vs-full.md
│   ├── reference/                  # Reference docs
│   │   ├── agents.md
│   │   ├── game-types.md
│   │   └── workflows.md
│   └── research/                   # Research documents
│       ├── technical-roblox-game-dev-...
│       └── technical-bmgd-roblox-extension-...
│
├── website/                        # Astro/Starlight docs site
│   ├── astro.config.mjs
│   ├── public/                     # Static assets (favicon, logos)
│   └── src/                        # Site components, styles, plugins
│
├── tools/
│   └── build-docs.mjs              # Docs build script
│
├── _bmad-output/                   # Generated artifacts (gitignored)
│   ├── planning-artifacts/         # Game briefs, GDDs, architecture docs
│   └── implementation-artifacts/   # Sprint status, stories, reviews
│
├── .github/                        # GitHub config
│   ├── workflows/                  # CI: quality, docs, discord, release
│   ├── ISSUE_TEMPLATE/
│   └── scripts/
│
├── package.json                    # npm config, scripts, devDependencies
├── README.md                       # Project readme
├── CONTRIBUTING.md                 # Contribution guide
├── CHANGELOG.md                    # Release history
├── TODO.md                         # Planned work
├── codex-review.md                 # Code review notes
├── install-success-message.md      # Post-install message
├── eslint.config.mjs               # ESLint config
├── prettier.config.mjs             # Prettier config
└── .markdownlint-cli2.yaml         # Markdown lint config
```

## Critical Folders Summary

| Folder | Purpose | Files |
|--------|---------|-------|
| `src/workflows/3-technical/gds-game-architecture/knowledge/` | Engine-specific architecture knowledge | 4 engine files |
| `src/workflows/3-technical/gds-game-architecture/` | Decision catalog, patterns, MCPs | 3 YAML configs |
| `src/gametest/knowledge/` | Testing knowledge base | 17 knowledge files |
| `src/agents/` | AI agent definitions | 7 agents |
| `src/module.yaml` | Module install config | Entry point |
| `docs/` | User-facing documentation site | 18 markdown files |
