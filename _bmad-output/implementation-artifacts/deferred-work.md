# Deferred Work

## Deferred from: code review of 1-1-add-roblox-to-engine-selection (2026-04-03)

- Downstream Roblox support gaps identified during review: engine knowledge fragment (`gds-game-architecture`), `decision-catalog.yaml` engine entry, `engine-mcps.yaml` section, test framework workflow branches (gds-test-framework, gds-test-automate, gds-e2e-scaffold, gds-performance-test), `roblox-testing.md` knowledge file, `docs/how-to/setup-roblox.md`, agent persona strings in `gds-agent-game-dev` and `gds-agent-game-qa`, and `architecture-patterns.yaml` heuristic rules. All gaps are tracked in Stories 1.2, 1.3 and Epics 2–4.

## Deferred from: code review of 1-3-add-roblox-mcp-server-entries (2026-04-03)

- `general` engine block in `engine-mcps.yaml` is missing the `engine` field that all other blocks have — pre-existing schema inconsistency, not introduced by Story 1.3.
- `workflow.md` (gds-game-architecture) is missing a `knowledge_fragments.roblox` entry pointing to `roblox-engine.md` created in Story 1.2. Without this, the architecture workflow will not load the Roblox knowledge fragment when Roblox is selected as the engine. Should be addressed as a followup to Story 1.2 or in a new story.
