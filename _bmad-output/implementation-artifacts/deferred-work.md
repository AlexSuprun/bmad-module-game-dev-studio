# Deferred Work

## Deferred from: code review of 4-1-update-agent-skill-md-files-with-roblox-expertise (2026-04-03)

- **Platform lists don't reflect Roblox's platform profile** — all 3 agents still claim "mobile, console, and PC" coverage without acknowledging Roblox's distinct platform profile (Roblox client, web, Xbox). Low-severity wording issue.
- **No Roblox-specific behavior/instruction rules in game-dev or solo-dev** — QA agent gained a TestEZ cross-check instruction, but game-dev and solo-dev agents received only engine-name-list updates with no parallel instructions for Luau, Studio toolchain, or Roblox-specific patterns.
- **TestEZ may be deprecated** — QA agent now references `Roblox TestEZ documentation` as authoritative, but TestEZ has been deprecated in favor of newer patterns. Should be revisited when updating Roblox testing knowledge.
- **Workflow files have no Roblox branches** — gds-test-framework, gds-test-automate, gds-e2e-scaffold, gds-test-design, and gds-performance-test all enumerate Unity/Unreal/Godot only; Roblox falls through with no framework initialization, scaffold, or profiling support.
- **gds-generate-project-context omits Roblox engine detection** — step-01-discover.md and step-02-generate.md lack Roblox file signatures (`.rbxl`, `default.project.json`) and engine-specific rules elicitation.
- **Architecture step-03-starter.md omits Roblox recommendation** — engine recommendation table has no Roblox row; social/UGC game queries would not surface Roblox as a candidate.
- **roblox-testing.md not referenced in gds-test-framework workflow load path** — knowledge file exists at `src/gametest/knowledge/roblox-testing.md` but the workflow has no load instruction for it.

## Deferred from: code review of 3-1-create-roblox-testing-knowledge-and-register-in-qa-index (2026-04-03)

- **`rokit trust` security implication undocumented** — unconditionally trusts all binaries in rokit.toml without explaining that this bypasses Rokit's signature verification; practitioners should understand the security trade-off before running in CI.
- **`ok-nick/setup-rokit@v0.1` not pinned to commit SHA** — mutable tag is a supply-chain security risk; all third-party GitHub Actions should be pinned to a full commit digest in production CI templates.
- **GameAssert.nearlyEqual uses absolute tolerance (0.001) not relative** — default tolerance is misleading for large damage values (hundreds/thousands); add a comment noting it's absolute and guidance on choosing tolerance for the domain.
- **TestFactory `(defaults :: any)[k] = v` undermines `--!strict`** — accepted Luau idiom for flexible factory functions; no clean alternative without verbose overloads. The `--!strict` annotation at the file top still applies to non-override code paths.
- **CombatService mock uses plain Lua tables without Instance-method caveat** — valid only when the service is designed for duck-typed inputs; add a one-sentence note that this pattern breaks if the service calls `:FindFirstChild`, `:GetAttribute`, or other Instance methods.

## Deferred from: code review of 2-3-add-roblox-architecture-patterns (2026-04-03)

- **"roblox server" trigger is ambiguous/low-signal** — infrastructure noun that appears in ordinary Roblox prose; could false-positive on single-player GDDs. Consider replacing with a more deliberate keyword.
- **decisions_needed key names don't align with decision_rules keys** — `game_engine` in decisions_needed but `engine_selection` in decision_rules; schema-level mismatch across all patterns.
- **rendering_pipeline has no decision_rules heuristic block** — gap in decision_rules means rendering decisions are never surfaced programmatically.
- **ai_system in roblox_multiplayer.decisions_needed has no suggested_stack guidance** — workflow will flag an AI decision as needed but provide zero stack recommendations; spec-level gap.
- **decision-catalog.yaml still lists profileservice as a valid option** — inline deprecation warning in suggested_stack string is not machine-readable; catalog inconsistency may lead to contradictory recommendations.
- **"roblox co-op" trigger may overlap with bare "co-op" in realtime_multiplayer** — depends on matching strategy (substring vs exact); could produce two competing architecture stacks for the same query.
- **Future Lighting recommendation is unqualified for mobile/low-end devices** — unconditional "best visual fidelity" claim is misleading for mobile-targeted Roblox experiences.
- **MemoryStoreService/MessagingService lack platform quota/rate-limit caveats** — omission could lead to designs that hit Roblox platform limits in production.

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
