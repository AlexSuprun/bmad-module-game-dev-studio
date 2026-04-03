# Deferred Work

## Deferred from: code review of 1-1-add-roblox-to-engine-selection (2026-04-03)

- Downstream Roblox support gaps identified during review: engine knowledge fragment (`gds-game-architecture`), `decision-catalog.yaml` engine entry, `engine-mcps.yaml` section, test framework workflow branches (gds-test-framework, gds-test-automate, gds-e2e-scaffold, gds-performance-test), `roblox-testing.md` knowledge file, `docs/how-to/setup-roblox.md`, agent persona strings in `gds-agent-game-dev` and `gds-agent-game-qa`, and `architecture-patterns.yaml` heuristic rules. All gaps are tracked in Stories 1.2, 1.3 and Epics 2–4.

## Deferred from: code review of 1-3-add-roblox-mcp-server-entries (2026-04-03)

- `general` engine block in `engine-mcps.yaml` is missing the `engine` field that all other blocks have — pre-existing schema inconsistency, not introduced by Story 1.3.
- `workflow.md` (gds-game-architecture) is missing a `knowledge_fragments.roblox` entry pointing to `roblox-engine.md` created in Story 1.2. Without this, the architecture workflow will not load the Roblox knowledge fragment when Roblox is selected as the engine. Should be addressed as a followup to Story 1.2 or in a new story.

## Deferred from: code review of 2-1-add-roblox-options-to-decision-catalog-categories (2026-04-03)

- `wwise` entry in `audio_system` has incorrect `pairs_with` containing Roblox/SoundService data (pre-existing corruption); `roblox_soundservice` is a character-for-character duplicate — two catalog entries share an identical payload, producing ambiguous matches. Pre-existing bug in decision-catalog.yaml.
- `godot_resource_loader` in `asset_pipeline` lists `"Roblox"` as first pairs_with element instead of `"Godot"` — pre-existing corruption now actively reachable by Roblox engine matching. Fix: replace `"Roblox"` with `"Godot"` in that entry.
- `roblox_avatar_abilities` category entry has no beta/stability marker despite AvatarAbilities being Studio Beta (launched March 2026). Schema only supports `pairs_with`; a convention like adding `"Studio Beta"` to pairs_with could signal instability.
- `roblox_standard` key name is shared between `common_stacks` and `starter_templates` — spec intentionally named both the same. Any workflow tooling that flattens or cross-references both sections using bare key names could encounter ambiguity.
- `workflow.md` knowledge_fragments still missing `roblox` registration (also deferred from Story 1.3 review above).
