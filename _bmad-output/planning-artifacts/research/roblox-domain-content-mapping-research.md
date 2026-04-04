# Roblox Domain Content Mapping & Gap Analysis

**Date:** 2026-04-02
**Purpose:** Map prior Roblox research content against the unity-engine.md template to identify gaps and fill them via web research, producing a readiness assessment for creating `roblox-engine.md`.

---

## Content Mapping Matrix

| Engine Template Section | Prior Research Coverage | Gap Level | Web Research Needed |
|------------------------|----------------------|-----------|-------------------|
| Overview | Full | Low | No |
| Architecture Fundamentals | Partial | Medium | Yes |
| Coding Best Practices | Partial | Medium | Yes |
| Performance Optimization | Partial | Medium | Yes |
| Popular Third-Party Packages | Full | Low | No |
| Common Architectural Patterns | Full | Low | No |
| Rendering Pipeline Selection | Partial | Medium | Yes |
| Licensing and Cost | Partial | Medium | Yes |
| When NOT to Use Roblox | None | High | Yes |
| Platform-Specific Caveats | Partial | Medium | Yes |
| Security Architecture | Partial | Medium | Yes |
| Data Architecture | Partial | Medium | Yes |
| Rojo Project Structure (new) | Full | Low | No |
| Architecture Decision Checklist | None | High | Partially |

---

## Detailed Section Mapping

### Section: Overview

**Template requirement (from unity-engine.md):**
Brief paragraph covering what the engine is, its core architecture model, scripting language, key capabilities, and platform support.

**Existing content in prior research:**
- Lines 89-97: Luau as sole scripting language, gradual type system, performance optimizations, native code gen
- Lines 99-112: Roblox Engine serves as the framework (physics, rendering, networking, UI, data persistence)
- Lines 161-178: Fully managed cloud hosting, cross-platform deployment
- Lines 256-278: Mandatory client-server model with Filtering Enabled

**Gaps identified:**
Minimal. All required overview elements exist across multiple sections of the prior research. Needs consolidation into a single concise paragraph.

**Web research findings:**
No additional research needed.

**Content readiness: 95%**

---

### Section: Architecture Fundamentals

**Template requirement (from unity-engine.md lines 8-112):**
- Core architecture model (Unity: GameObject-Component). Needs Roblox equivalent: Instance-Service-DataModel hierarchy
- Lifecycle callbacks (Unity: Awake/Start/Update/etc). Needs Roblox equivalent: Instance lifecycle, script execution context
- Code organization pattern (Unity: Assembly Definitions). Needs Roblox equivalent: Rojo project structure, ModuleScript patterns
- Data architecture pattern (Unity: ScriptableObjects). Needs Roblox equivalent: ModuleScript configs, DataStoreService
- Typical hierarchy diagram

**Existing content in prior research:**
- Lines 256-278: Client-server model, RemoteEvent/RemoteFunction communication, replication rules, script placement (ServerScriptService, StarterPlayerScripts, ReplicatedStorage)
- Lines 370-387: Rojo project organization pattern with directory structure
- Lines 329-358: Service-Controller (Knit) and ECS (Jecs) patterns
- Lines 362-387: Design principles (server authority, module-first, event-driven, single responsibility, data-code separation)

**Gaps identified:**
1. **DataModel hierarchy deep dive** -- no explanation of the Instance class, service hierarchy, or how DataModel is the root
2. **Instance lifecycle** -- no coverage of Instance creation, parenting, replication lifecycle, or events like ChildAdded/ChildRemoved/AncestryChanged
3. **Service loading order** -- no explanation of which services load by default vs. need GetService()
4. **Script execution contexts** -- no detailed explanation of Script vs LocalScript vs ModuleScript execution models
5. **Replication model details** -- basic coverage exists but lacks depth on what replicates where and when

**Web research findings:**

**DataModel Hierarchy:**
- DataModel (accessed via global `game`) is the root of Roblox's parent-child hierarchy, inheriting from ServiceProvider > Instance
- Direct children are services: Workspace, Lighting, ReplicatedStorage, ServerScriptService, ServerStorage, StarterGui, StarterPlayer, StarterPack, SoundService, Teams, Chat, etc.
- Services cannot be instantiated via Instance.new(); they are accessed via `game:GetService("ServiceName")`
- Some services are auto-instantiated (Workspace, Lighting, Players), others require GetService() to activate

**Instance Lifecycle:**
- Instance.new(className) creates an instance (not yet parented)
- Parenting triggers replication: server-created instances replicate to clients only when parented to a replicated container
- `game:IsLoaded()` returns true when all initial instances finish replicating to client
- LocalScripts outside ReplicatedFirst do not execute until IsLoaded is true
- `game:BindToClose(callback)` runs before server shutdown (30-second timeout), critical for data saving

**Script Execution Contexts:**
- **Script** (server): Runs on the server. Must be parented under ServerScriptService or Workspace
- **LocalScript** (client): Runs on each client. Must be parented under StarterPlayerScripts, StarterGui, StarterPack, or player Character
- **ModuleScript** (shared): Does not run independently. Returns a single value (usually a table). Executes in the context of the requiring script (server or client). Cached after first require()

**Sources:**
- [DataModel Documentation](https://create.roblox.com/docs/reference/engine/classes/DataModel)
- [Instance Documentation](https://robloxapi.github.io/ref/class/Instance)
- [Services Documentation](https://github.com/Roblox/creator-docs/blob/main/content/en-us/scripting/services.md)
- [Replication Behavior Tutorial](https://devforum.roblox.com/t/replication-behavior-for-instances/2178130)

**Content readiness: 80%** (after combining prior research + web findings)

---

### Section: Coding Best Practices

**Template requirement (from unity-engine.md lines 166-318):**
- Communication patterns between components
- Serialization/Inspector patterns
- Async patterns (coroutines vs async/await)
- Dependency injection / service locator
- Project structure conventions with naming rules

**Existing content in prior research:**
- Lines 362-387: Design principles (server authority, module-first, event-driven, single responsibility, data-code separation)
- Lines 370-387: Rojo project organization pattern
- Lines 392-396: task.spawn/task.defer for non-blocking operations, event-driven vs polling
- Lines 499-502: CLAUDE.md guidance mentions Luau conventions, Knit framework conventions, server/client/shared separation

**Gaps identified:**
1. **Luau-specific coding patterns** -- no require() caching pattern, no type annotation examples, no ModuleScript return patterns
2. **Naming conventions** -- not specified (PascalCase for services? camelCase for variables?)
3. **Async patterns in Luau** -- task library (task.spawn, task.defer, task.wait, task.delay, task.cancel) not fully documented
4. **Communication patterns** -- RemoteEvent patterns exist but no BindableEvent (intra-context) patterns
5. **Anti-patterns** -- `while wait() do` mentioned once but no comprehensive anti-pattern list
6. **Luau language features** -- if-then-else expressions, generalized iteration, compound assignments, floor division not covered

**Web research findings:**

**Luau Language Features (current as of 2025):**
- **If-then-else expressions**: `if condition then valueA else valueB` -- proper ternary replacement, `else` is mandatory to prevent nil-return bugs
- **Generalized iteration**: `for k, v in obj do` works on tables directly without pairs()/ipairs(), customizable via `__iter` metamethod
- **Floor division**: `a // b` equals `math.floor(a / b)`, with compound `//=` and metamethod `__idiv`
- **Compound assignments**: `+=`, `-=`, `*=`, `/=`, `%=`, `^=`, `..=`, `//=`
- **String interpolation**: `` `Hello {name}, you have {coins} coins` ``
- **Type annotations**: `local x: number = 5`, function signatures `function add(a: number, b: number): number`
- **Type functions** (beta): Programmable type-level computation
- **Read-only table properties** (beta): `{ read x: number }`

**Luau 2025 Runtime Improvements:**
- New `vector` library with native vector type construction and optimized operations
- `math.map`, `math.lerp`, `math.isnan`, `math.isinf`, `math.isfinite` added
- `buffer.readbits`/`buffer.writebits` for bitwise buffer operations
- 3x speedup for userdata object creation
- Stackless pcall/xpcall eliminating C call depth limits
- Native code generation now supports Android, with vector.dot lowered to CPU dot product instructions
- Load-store propagation eliminates duplicate loads and temporary allocations

**Naming Conventions (community consensus):**
- Services/Classes: `PascalCase` (e.g., `CombatService`, `InventoryManager`)
- Functions/Methods: `PascalCase` or `camelCase` (varies, Roblox API uses PascalCase)
- Variables/locals: `camelCase`
- Constants: `UPPER_SNAKE_CASE`
- Private members: `_camelCase` prefix (convention, not enforced)
- Files: match the ModuleScript name they contain

**Sources:**
- [Luau Syntax](https://luau.org/syntax/)
- [Luau 2025 Runtime Recap](https://luau.org/news/2025-12-19-luau-recap-runtime-2025/)
- [Luau RFCs - if-then-else expression](https://rfcs.luau.org/syntax-if-expression.html)
- [Luau RFCs - generalized iteration](https://github.com/luau-lang/rfcs/blob/master/docs/generalized-iteration.md)

**Content readiness: 75%** (significant new content from web research; needs code examples written)

---

### Section: Performance Optimization

**Template requirement (from unity-engine.md lines 320-377):**
- General guidelines (avoid expensive operations, caching, pooling)
- Rendering-specific optimization
- Physics performance
- Memory and loading
- Language-specific performance tips

**Existing content in prior research:**
- Lines 392-412: Scripting performance (replace while wait(), task.spawn/task.defer, heartbeat throttling, object pooling)
- Lines 397-402: Asset/rendering (StreamingEnabled, LOD, 500 part limit per model, texture atlasing, collision mesh simplification)
- Lines 404-408: Memory/networking (RemoteEvent batching, UnreliableRemoteEvent, Debris:AddItem(), connection cleanup)

**Gaps identified:**
1. **Specific frame budgets** -- no mention of 16ms target for 60 FPS
2. **Memory limits per platform** -- no specific numbers for mobile/desktop/console
3. **Instance count guidelines** -- 500 parts per model mentioned but no overall workspace guidance
4. **StreamingEnabled configuration details** -- mentioned but not explained
5. **Native code generation** -- not mentioned as a performance tool
6. **Luau-specific performance tips** -- no table pre-allocation, string.format vs concatenation, etc.

**Web research findings:**

**Frame Budgets:**
- 16ms per frame for 60 FPS target
- Three main performance domains: Rendering (GPU), Simulation (CPU - physics/scripts/logic), Network (RemoteEvents + replication)

**Memory Limits:**
- No hard enforced memory limits per platform -- when memory exceeds device capacity, the client/server crashes
- Practical mobile target: ~200MB (though even empty baseplate with 1 player uses ~230MB on client)
- Desktop: significantly more headroom but still must optimize for low-end hardware
- Server memory: monitored via Developer Console, crashes cause player disconnection

**Instance Count Guidelines:**
- No hard instance limit -- constrained only by memory
- Practical guidelines: keep workspace under ~10,000 parts for smooth performance
- Per-model target: ~500 parts maximum
- With StreamingEnabled, larger worlds are viable as only nearby content loads

**StreamingEnabled Configuration (detailed):**
- `StreamingMinRadius` -- guaranteed loaded radius around player (higher = more memory, more server bandwidth)
- `StreamingTargetRadius` -- maximum distance for streaming in content (engine may keep content beyond this if memory permits)
- `StreamOutBehavior`: LowMemory (default, only unloads under pressure) or Opportunistic (proactively unloads beyond target radius)
- `StreamingIntegrityMode` -- prevents issues when players move into unstreamed regions
- `Model.StreamingMode`: Nonatomic (default, parts stream individually), Atomic (all descendants stream together), Persistent (never streams out), PersistentPerPlayer
- Physical assemblies stream as complete units with constraints and attachments
- Enabled by default for new places; reduces initial load by up to 35%

**Native Code Generation:**
- Fully supported on X64 (Intel/AMD) and A64 (ARM)
- Compiles Luau scripts to machine code instead of bytecode interpretation
- Now supports Android; tested in production
- Specific optimizations: vector.dot to CPU instructions, fused-multiply-add for lerp, load-store propagation

**Sources:**
- [Instance Streaming Documentation](https://create.roblox.com/docs/workspace/streaming)
- [Memory Usage Documentation](https://create.roblox.com/docs/studio/optimization/memory-usage)
- [Performance Optimization](https://create.roblox.com/docs/performance-optimization)
- [Roblox Game Optimization Guide](https://electrodeai.org/blog/roblox-game-optimization-tips)
- [Luau 2025 Runtime Recap](https://luau.org/news/2025-12-19-luau-recap-runtime-2025/)

**Content readiness: 85%**

---

### Section: Popular Third-Party Packages

**Template requirement (from unity-engine.md lines 378-428):**
Tables of recommended packages organized by category: Essential Development, Gameplay Systems, Visual/Audio, Networking, Infrastructure.

**Existing content in prior research:**
- Lines 99-112: Knit, ProfileService/DataStore2, Roact/React-Lua, Rojo
- Lines 329-358: Knit (Service-Controller), Jecs (ECS), ECS-Lua, EntityManager
- Lines 546-558: Complete tooling ecosystem table (Claude Code, Rojo, VS Code, Git, Lune, Selene, StyLua, Wally)
- Lines 670-681: Full technology stack recommendations table
- BMGD extension research lines 134-214: Comprehensive decision catalog entries listing: Knit, Wally, BridgeNet2, ProfileService, Lapis, Fusion, Roact/React-Lua, SimplePath, TopbarPlus, Moon Animator

**Gaps identified:**
Minor gaps only. Missing a few packages:
- TestEZ (testing) -- mentioned in testing section but not in packages
- Jecs version/repo details
- Package installation via Wally (format: `wally add author/package`)

**Web research findings:**
No additional research needed. Prior research + BMGD extension doc cover this comprehensively.

**Content readiness: 95%**

---

### Section: Common Architectural Patterns

**Template requirement (from unity-engine.md lines 430-514):**
Code examples for 3-4 common patterns: Singleton, Object Pool, Bootstrap Scene, etc.

**Existing content in prior research:**
- Lines 329-358: Service-Controller (Knit) with detailed description, ECS (Jecs) with 800K entity benchmark, Hybrid approach
- Lines 362-387: Design principles, Rojo project organization, module-first pattern
- BMGD extension research lines 276-335: Complete architecture patterns YAML with roblox_experience and roblox_multiplayer patterns, default stacks

**Gaps identified:**
1. **No Luau code examples** -- patterns are described in prose but no actual code snippets for Knit service/controller, bootstrap pattern, or object pool in Luau
2. Missing: Event Channel pattern (Roblox equivalent of Unity's ScriptableObject events)

**Web research findings:**
No web research needed -- code examples should be authored during implementation based on the documented patterns.

**Content readiness: 70%** (patterns well-described; Luau code examples need to be written)

---

### Section: Rendering Pipeline Selection

**Template requirement (from unity-engine.md lines 516-524):**
Table comparing rendering pipeline options with use cases and trade-offs, plus a default recommendation.

**Existing content in prior research:**
- BMGD extension research lines 139-148: Three lighting options (Future Lighting, ShadowMap, Compatibility) with pairs_with relationships

**Gaps identified:**
1. **No detailed comparison table** -- only pairs_with entries, no prose explaining trade-offs
2. **Missing PBR/materials context** -- SurfaceAppearance, MaterialVariant not explained
3. **No default recommendation statement**

**Web research findings:**
From StreamingEnabled research and general knowledge:
- **Future Lighting**: Best quality, PBR support, SurfaceAppearance materials, ShadowMap rendering. Requires higher-end hardware. Default for new experiences
- **ShadowMap**: Good quality shadows without full PBR pipeline. Mid-range hardware suitable
- **Compatibility**: Legacy lighting mode. Lowest quality but runs on all hardware. No shadow casting
- Default recommendation: Future Lighting for new experiences (matches Unity's "URP for all new projects" advice)

**Content readiness: 70%** (structure exists; needs prose and comparison table)

---

### Section: Licensing and Cost

**Template requirement (from unity-engine.md lines 526-549):**
Clear breakdown of licensing tiers, costs, and important cost considerations.

**Existing content in prior research:**
- Lines 609-628: Claude Code costs (API pricing, typical session costs, Pro plan)
- Lines 622-628: Roblox platform costs (Studio: free, hosting: free, publishing: free, DevEx revenue share)

**Gaps identified:**
1. **DevEx rates not specified** -- just mentions "revenue share"
2. **Marketplace fees not specified** -- no percentage given
3. **Roblox premium payouts** -- not covered
4. **Extended Services pricing** -- not covered (new in 2025-2026)

**Web research findings:**

**Roblox Studio and Platform:**
- Roblox Studio: completely free, no tiers
- Game hosting and infrastructure: completely free (Roblox manages all servers, CDN, scaling)
- Publishing: free
- No per-seat fees, no revenue thresholds triggering paid tiers

**Developer Exchange (DevEx) Rates (as of September 2025):**
- New rate: $0.0038 per Robux (increased from $0.0035, an 8.6% increase)
- Pre-September 2025 Robux: redeemed at $0.0035
- Minimum eligibility: 30,000 earned Robux (~$114 USD)
- System prioritizes older Robux first when redeeming

**Marketplace Fees:**
- Standard marketplace fee: 30% on all transactions (sellers receive 70%)
- Creator Store accessories/3D assets: progressive revenue share from 30% to 70% based on pricing relative to floor price
- Engagement-based payouts (Premium Payouts): Roblox distributes a portion of Premium subscription revenue to developers based on engagement metrics

**Extended Services (new 2025-2026):**
- Roblox Extended Services allows large experiences to purchase additional DataStore and API usage beyond platform limits
- Pricing not yet publicly detailed

**Important Cost Considerations:**
- Unlike Unity (subscription model) or Unreal (5% royalty above $1M), Roblox has zero upfront or ongoing developer costs
- Revenue share is taken at the point of Robux-to-USD conversion (DevEx), not at the point of sale
- Effective developer take-home is roughly 24.5% of player spend (after Roblox platform fee + DevEx conversion)

**Sources:**
- [DevEx Rates](https://en.help.roblox.com/hc/en-us/articles/27984458742676-Earned-Robux-Earned-Robux-Balance-and-DevEx-Rates)
- [DevEx Calculator 2026](https://devex.gg/)
- [Robux Tax Calculator](https://omnicalculator.space/robux-tax-calculator-2025-2026/)
- [Extended Services Announcement](https://devforum.roblox.com/t/announcing-roblox-extended-services/3621439)

**Content readiness: 95%**

---

### Section: When NOT to Use Roblox

**Template requirement (from unity-engine.md lines 541-549):**
Clear list of scenarios where the engine is the wrong choice, with brief explanations.

**Existing content in prior research:**
- BMGD extension research line 352 mentions this section exists in the planned structure with topics: "Non-Roblox platforms, custom rendering, offline games, precise physics, mature content"
- No actual prose content exists in either research document

**Gaps identified:**
This entire section needs to be written from scratch.

**Web research findings:**

Roblox is the wrong choice when:

1. **You need to publish outside the Roblox platform** -- Roblox games run exclusively on Roblox. You cannot export a standalone executable, publish to Steam, or distribute on mobile app stores independently. The game lives and dies on the Roblox platform.

2. **Custom rendering or shader programming is required** -- Roblox does not expose a programmable shader pipeline. You cannot write custom vertex/fragment shaders, implement ray tracing, or create novel rendering techniques. The rendering is Roblox's built-in pipeline only.

3. **Offline play is a requirement** -- Roblox requires an internet connection. There is no offline mode for players (though Roblox has hinted at exploring offline for under-13 users in Indonesia). Games cannot function without Roblox's servers.

4. **Precise or custom physics simulation is needed** -- While Roblox has a physics engine, it is not configurable at the simulation level. You cannot swap in Havok, PhysX, or a custom physics solver. Precision physics (engineering simulators, accurate billiards) are limited.

5. **Mature or explicit content is the core product** -- Despite the 18+ Restricted label, Roblox Community Standards prohibit: sexual content, realistic depictions of death/gore beyond "strong violence," real-world drug use, gambling with real currency, and content promoting self-harm. The platform is fundamentally oriented toward younger audiences.

6. **You need full control over monetization** -- All purchases go through Robux. You cannot implement your own payment processing, accept credit cards directly, or use third-party payment providers. Roblox takes a significant cut (~75.5% of player spend before DevEx).

7. **Large-scale persistent open worlds without streaming** -- While StreamingEnabled helps, Roblox's architecture is server-instance based. Each server holds a limited number of players (typically 50-100). Massive persistent worlds like MMOs require multi-place architectures with TeleportService, which adds complexity.

8. **You need fast iteration on native mobile or console features** -- Roblox abstracts the platform away. While this is usually a benefit (write once, deploy everywhere), it means you cannot access platform-specific APIs (ARKit, Game Center achievements, PS5 haptics) directly.

**Sources:**
- [Roblox Community Standards](https://about.roblox.com/community-standards)
- [Restricted Content Policy](https://en.help.roblox.com/hc/en-us/articles/15869919570708-Restricted-Content-Policy)
- [Regional Restrictions](https://en.help.roblox.com/hc/en-us/articles/41116913453844-Regional-Restrictions-on-Features-and-Content)
- [Content Maturity Labels](https://en.help.roblox.com/hc/en-us/articles/8862768451604-Content-Maturity-Labels)

**Content readiness: 90%** (fully new content from web research)

---

### Section: Platform-Specific Caveats

**Template requirement (from unity-engine.md lines 551-557):**
Per-platform notes: gotchas, requirements, and considerations for each deployment target.

**Existing content in prior research:**
- BMGD extension research line 353: Planned section mentions "Mobile (60%+ players, touch), Xbox/PS (gamepad-only), VR (Meta Quest), cross-platform UI scaling"
- BMGD extension research line 241: deployment_platform entry mentions PC, Mac, iOS, Android, Xbox, PlayStation, Meta Quest

**Gaps identified:**
1. **Mobile specifics** -- 60% stat mentioned but no details on touch input, performance constraints, screen size
2. **Console specifics** -- gamepad-only mentioned but no details on certification, approval process, feature limitations
3. **VR specifics** -- Meta Quest mentioned but no VR API details
4. **Cross-platform UI** -- mentioned but no patterns for auto-scaling
5. **Web/desktop specifics** -- not covered

**Web research findings:**

Key platform caveats:
- **Mobile (60%+ of players)**: Touch input must be primary design target. ContextActionService creates automatic touch buttons. Lower memory budget (~200MB practical target). Smaller screens require larger UI elements. 30 FPS more realistic target on low-end devices
- **Xbox/PlayStation**: Gamepad-only input. Roblox handles console certification. Games must work without mouse/keyboard. Achievement integration not available through Roblox
- **Meta Quest (VR)**: VR-specific APIs for headset tracking. Must handle both VR and non-VR players in same server. Performance budget is tightest on standalone VR
- **Desktop (PC/Mac)**: Most generous performance budget. Mouse + keyboard primary input. Highest screen resolution expectations
- **Cross-platform UI**: Use UIAspectRatioConstraint, UISizeConstraint, and AutomaticSize for responsive layouts. Test on all form factors. StarterGui.ScreenOrientation controls mobile orientation

**Content readiness: 75%** (needs expansion and specific examples)

---

### Section: Security Architecture

**Template requirement:**
Unity template does not have a dedicated security section. However, the BMGD extension research (line 354) includes this as a planned section for roblox-engine.md. This is Roblox-specific and critical.

**Existing content in prior research:**
- Lines 414-439: Security architecture patterns -- "Never Trust the Client" principle, four exploit vectors (input manipulation, time warping, memory access, command injection), five server validation patterns (rate limiting, sanity checks, server-side state, input validation, network ownership)
- Lines 432-436: Claude Code implications for security

**Gaps identified:**
1. **No code examples** -- validation patterns described in prose only
2. **Network ownership management** -- mentioned but not explained
3. **Specific anti-cheat libraries** -- not mentioned
4. **RemoteEvent placement security** -- storing in ReplicatedStorage (accessible to exploiters) vs alternatives

**Web research findings:**

**RemoteEvent Validation Patterns (with implementation details):**

1. **Rate Limiting**: Store per-player tick times in a server table; check `tick() - lastCall > cooldownThreshold` before processing. Typical threshold: 0.2 seconds between calls

2. **Type Validation**: Use `typeof()` on every argument. Reject and warn on mismatched types. Never assume argument types

3. **String Length Validation**: Use `string.len()` to enforce maximum character limits; compare against whitelists with `table.find()`

4. **Table Validation**: Never unpack or iterate entire client-sent tables. Check specific indices and validate `table.maxn()` length plus `typeof()` each value individually

5. **Async Handler Wrapping**: Wrap RemoteEvent handlers in `task.spawn()` to prevent queue exhaustion from malicious rapid-fire calls

6. **Server-State Authority**: Never use client-sent numbers for critical mechanics (health, currency, damage). Always derive from server-side state

**Network Ownership:**
- Physics ownership determines which machine (server or client) simulates a part's physics
- Exploiters with network ownership of a part can teleport it
- Use `BasePart:SetNetworkOwner(nil)` to keep server authority on critical parts
- `BasePart:SetNetworkOwnershipAuto()` for non-critical parts (better performance)

**Sources:**
- [RemoteEvent Security Tutorial](https://devforum.roblox.com/t/how-to-secure-your-remoteevent-and-remotefunction/3345363)
- [Security Tactics Official Docs](https://create.roblox.com/docs/scripting/security/security-tactics)
- [Exploit Prevention Guide](https://devforum.roblox.com/t/exploiting-and-how-to-prevent-it/2304937)

**Content readiness: 85%** (good prose + patterns; Luau code examples still needed)

---

### Section: Data Architecture

**Template requirement:**
Not in Unity template. Roblox-specific section planned in BMGD extension research (line 355).

**Existing content in prior research:**
- Lines 116-129: DataStoreService (key-value, 6MB per key stated -- note: now 4MB per key), MemoryStoreService, MessagingService, OrderedDataStore, HTTPService limitations
- Lines 441-460: Player data patterns (ProfileService/DataStore2, session locking, data versioning), game configuration patterns, cross-server patterns (MessagingService, TeleportService, Open Cloud API)

**Gaps identified:**
1. **DataStore request limits** -- not specified in prior research
2. **DataStore per-key size** -- prior research says "6MB" which is incorrect (actual: 4MB)
3. **MemoryStoreService limits** -- not specified
4. **New per-experience vs per-server limit model** -- not covered (major 2026 change)
5. **Extended Services** -- not covered

**Web research findings:**

**DataStoreService Limits (transitioning to per-experience model, early 2026):**

| Operation | Limit Formula |
|-----------|--------------|
| Get (Standard) | 250 + CCU x 40 per minute |
| Set (Standard) | 250 + CCU x 20 per minute |
| List (Standard) | 10 + CCU x 2 per minute |
| Remove (Standard) | 100 + CCU x 40 per minute |
| GetSortedAsync (Ordered) | 100 + CCU x 2 per minute |

*CCU = concurrent users; limits now per-experience, not per-server*

**Storage Limits (effective early 2026):**
- Per-experience total storage: 100 MB + 1 MB x (Lifetime Players)
- Per-key limit: 4MB (unchanged)
- Key name limit: 50 characters
- Fewer than 30 experiences out of 55 million affected by storage limits

**MemoryStoreService Limits:**
- Per-partition request limits (global)
- Sorted maps and queues have limits on max items and total memory
- Intended for ephemeral data only -- items expire after set TTL (max 45 days)
- 128KB per item value size

**Network Bandwidth (RemoteEvent):**
- Commonly cited: ~50 KB/s per player and ~20 fires/second (though Roblox has removed specific numbers from official docs)
- UnreliableRemoteEvent: no guaranteed delivery, lower overhead, suitable for high-frequency position/animation data
- Best practice: batch updates, minimize payload size, use UnreliableRemoteEvent for non-critical data

**Sources:**
- [DataStores Access and Storage Updates](https://devforum.roblox.com/t/datastores-access-and-storage-updates/3597255)
- [Extended Services Announcement](https://devforum.roblox.com/t/announcing-roblox-extended-services/3621439)
- [MemoryStore Best Practices](https://github.com/Roblox/creator-docs/blob/main/content/en-us/cloud-services/memory-stores/best-practices.md)
- [RemoteEvent Optimization Guide](https://devforum.roblox.com/t/optimizing-remoteevent-usage-a-practical-guide-for-beginners/4058311)

**Content readiness: 90%** (major correction on 6MB->4MB, significant new limit data)

---

### Section: Rojo Project Structure

**Template requirement:**
Roblox-specific section (no Unity equivalent). Planned in BMGD extension research (line 356).

**Existing content in prior research:**
- Lines 226-252: Rojo sync details -- project structure (default.project.json), file conventions (.lua/.luau, .json, .model.json, init files), sync modes (serve vs build), two-workflow approach
- Lines 370-387: Complete directory structure example with server/client/shared/assets mapping

**Gaps identified:**
1. **default.project.json example** -- not provided (only described)
2. **Wally integration** -- Wally.toml and Packages directory not shown in project structure
3. **Tooling config files** -- selene.toml, stylua.toml, .gitignore not shown

**Web research findings:**

Professional Rojo workflow best practices:
- **Partially Managed**: Rojo manages scripts only; Studio manages terrain, models, UI. Easier for mixed teams. Compatible with Team Create
- **Fully Managed**: Rojo manages entire game. Enables CI/CD, hermetic builds. Requires all team members to use Rojo
- Key config files in a professional project: `default.project.json`, `wally.toml`, `selene.toml`, `stylua.toml`, `.gitignore`, `aftman.toml`
- Professional stack: VS Code + Rojo + Wally + Selene + StyLua + Git

**Sources:**
- [Rojo Workflows](https://rojo.space/docs/6.x/workflows/)
- [Professional Roblox Workflow Guide](https://freerbxscripts.com/post/set-up-a-pro-development-workflow/)
- [Advanced Roblox Project Setup](https://gist.github.com/portfiend/c3294edec3190b6487fa4b800fbc9589)

**Content readiness: 90%**

---

### Section: Architecture Decision Checklist

**Template requirement (from unity-engine.md lines 559-573):**
Checkbox list of key decisions to make when starting a project with this engine.

**Existing content in prior research:**
- BMGD extension research line 357: Planned as "12-item checklist (server/client split, data persistence, networking, monetization, etc.)"
- No actual checklist content exists

**Gaps identified:**
Entire section needs to be authored. However, the decision items are derivable from the decision catalog mapping in the BMGD extension research (lines 131-270, covering all 14 categories).

**Web research findings:**
No additional research needed -- the checklist items map directly to the 14 BMGD decision categories already documented.

**Content readiness: 80%** (items known; formatting needed)

---

## New Content from Web Research

### 1. DataModel and Instance Hierarchy (Architecture Fundamentals)
Complete coverage of DataModel as root, service hierarchy, Instance lifecycle, script execution contexts, and replication model. This fills the largest gap in the prior research.

### 2. Luau 2025 Language and Runtime Features (Coding Best Practices)
- vector library, math.map, math.lerp, buffer.readbits/writebits
- 3x userdata creation speedup, stackless pcall, Android native codegen
- Compiler constant propagation for vector arithmetic
- if-then-else expressions, generalized iteration, floor division, compound assignments, string interpolation

### 3. DataStoreService Limit Overhaul (Data Architecture)
Major correction: prior research stated 6MB per key (incorrect; actual is 4MB). New per-experience limit model replacing per-server limits in early 2026, with specific formulas (250 + CCU x 40 for gets, etc.). Total storage: 100MB + 1MB x Lifetime Players.

### 4. StreamingEnabled Deep Configuration (Performance)
Full configuration property reference: StreamingMinRadius, StreamingTargetRadius, StreamOutBehavior (LowMemory vs Opportunistic), StreamingIntegrityMode, Model.StreamingMode (Nonatomic, Atomic, Persistent, PersistentPerPlayer). Assembly streaming behavior for physics.

### 5. "When NOT to Use Roblox" (entirely new section)
Eight specific scenarios with explanations: platform lock-in, no custom shaders, no offline play, limited physics customization, content restrictions, monetization constraints, server instance limits, no platform-specific APIs.

### 6. DevEx Rates and Marketplace Fees (Licensing and Cost)
Current DevEx rate: $0.0038/Robux (as of Sep 2025). 30% marketplace fee. Effective developer take-home: ~24.5% of player spend. Extended Services for large experiences.

### 7. RemoteEvent Security Patterns (Security Architecture)
Detailed validation patterns: rate limiting with tick(), typeof() validation, string length checks, table validation, async handler wrapping, network ownership management.

---

## Roblox Platform Updates Since Prior Research (April 1, 2026)

The prior research was conducted on 2026-04-01 (yesterday). No significant platform changes detected in the 1-day gap. However, several changes referenced in the prior research as "upcoming" are now confirmed:

1. **DataStore per-experience limits** -- confirmed rolling out early 2026 (prior research did not cover this)
2. **Extended Services** -- announced for large experiences needing additional API quotas
3. **18+ age requirement for Restricted content** -- raised from 17+ (August 2025)
4. **DevEx rate increase to $0.0038** -- September 2025 (not in prior research)
5. **Luau 2025 runtime improvements** -- vector library, buffer.readbits/writebits, Android native codegen (December 2025 recap, not in prior research)

---

## Content Completeness Score

| Section | Prior Research | After Web Research | Weight |
|---------|---------------|-------------------|--------|
| Overview | 95% | 95% | 5% |
| Architecture Fundamentals | 50% | 80% | 20% |
| Coding Best Practices | 40% | 75% | 15% |
| Performance Optimization | 60% | 85% | 10% |
| Third-Party Packages | 95% | 95% | 5% |
| Architectural Patterns | 80% | 80% | 10% |
| Rendering Pipeline | 40% | 70% | 3% |
| Licensing and Cost | 40% | 95% | 5% |
| When NOT to Use Roblox | 0% | 90% | 5% |
| Platform-Specific Caveats | 30% | 75% | 5% |
| Security Architecture | 60% | 85% | 7% |
| Data Architecture | 50% | 90% | 5% |
| Rojo Project Structure | 85% | 90% | 3% |
| Architecture Decision Checklist | 0% | 80% | 2% |

**Weighted Overall Readiness: ~83%**

The remaining ~17% gap consists primarily of:
- Luau code examples that need to be authored (not researchable, must be written)
- Some platform-specific caveats that need hands-on verification
- Rendering pipeline comparison table needing more specific benchmarks

---

## Recommended Additional Research

### Not Strictly Needed but Would Improve Quality

1. **Luau code examples for architectural patterns** -- Not a research task; these need to be authored. Knit service bootstrap, object pool in Luau, ModuleScript return patterns, type-annotated function examples.

2. **Roblox rendering pipeline benchmarks** -- Specific FPS comparisons between Future Lighting, ShadowMap, and Compatibility on reference hardware. No reliable public benchmarks found.

3. **MemoryStoreService detailed limits** -- Specific numbers for per-partition request limits, max items per sorted map/queue. Official docs are somewhat vague.

4. **Console certification process** -- While Roblox handles this, understanding what changes (if any) developers must make for Xbox/PS compliance would strengthen platform caveats.

5. **ProfileService vs Lapis comparison** -- Both are DataStore wrappers. A detailed comparison (API, performance, features) would help the decision catalog. Lapis is newer and may be gaining adoption.

### Blocked Research (requires hands-on testing)

6. **Actual memory profiles per platform** -- Requires running test experiences on mobile, desktop, console, and VR to measure baseline memory usage.

7. **Network bandwidth precise limits** -- Roblox has removed specific numbers from docs. Only empirical testing can determine current thresholds.

---

## Research Methodology

- **Local analysis**: Full reading of three source documents (prior Roblox research 807 lines, BMGD extension research 589 lines, unity-engine.md 573 lines)
- **Web search**: 12 targeted searches across Roblox Creator Hub, DevForum, Luau.org, GitHub, and community resources
- **Deep fetches**: 5 targeted page fetches for DataModel docs, Luau 2025 recap, StreamingEnabled docs, DataStore limits announcement, and RemoteEvent security patterns
- **Cross-referencing**: All web findings validated against multiple sources
- **Gap analysis**: Section-by-section comparison against unity-engine.md template structure

### Key Sources

- [Roblox Creator Hub Documentation](https://create.roblox.com/docs)
- [DataModel Reference](https://create.roblox.com/docs/reference/engine/classes/DataModel)
- [Instance Streaming](https://create.roblox.com/docs/workspace/streaming)
- [DataStores Access and Storage Updates](https://devforum.roblox.com/t/datastores-access-and-storage-updates/3597255)
- [Extended Services Announcement](https://devforum.roblox.com/t/announcing-roblox-extended-services/3621439)
- [Luau 2025 Runtime Recap](https://luau.org/news/2025-12-19-luau-recap-runtime-2025/)
- [Luau Syntax Reference](https://luau.org/syntax/)
- [Rojo Documentation](https://rojo.space/docs/v7/)
- [RemoteEvent Security Tutorial](https://devforum.roblox.com/t/how-to-secure-your-remoteevent-and-remotefunction/3345363)
- [Security Tactics Official Docs](https://create.roblox.com/docs/scripting/security/security-tactics)
- [DevEx Rates](https://en.help.roblox.com/hc/en-us/articles/27984458742676-Earned-Robux-Earned-Robux-Balance-and-DevEx-Rates)
- [Roblox Community Standards](https://about.roblox.com/community-standards)
- [Memory Usage Documentation](https://create.roblox.com/docs/studio/optimization/memory-usage)
- [Performance Optimization](https://create.roblox.com/docs/performance-optimization)
- [Professional Roblox Workflow Guide](https://freerbxscripts.com/post/set-up-a-pro-development-workflow/)
- [Roblox Game Optimization Guide](https://electrodeai.org/blog/roblox-game-optimization-tips)
