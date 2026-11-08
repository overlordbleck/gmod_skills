# Garry's Mod Lua Addon Guide for Codex

Status: working draft, based on gm_express, cfc_time, and CFC's GLua style guidelines. Refine this guide as more examples are reviewed. The user's preferred layout is `shared/`, `server/`, and, when needed, `client/` inside the main addon directory.

## Objective

Build addons that are easy to navigate, understand, and change, with predictable client/server behavior and appropriate runtime costs. Match the existing project's conventions before introducing a new structure. Prefer the smallest implementation that meets the actual requirements.

Apply explicit user instructions first, then the target repository's requirements. Use this guide's CFC-derived conventions as defaults for new code. Preserve established public API names and external data formats; do not rename them just to match a style preference. The reference addons illustrate design choices, while the style guidelines provide the default rules for spelling and formatting new code.

## 1. Keep loading explicit and separate execution realms

- Keep the autorun entry point small. Use it to start the addon, not to contain its features.
- Put addon-owned code under a uniquely named directory.
- For new addons, organize that directory into `shared/`, `server/`, and `client/` subdirectories as needed. This is the user's preferred structure, based on cfc_time.
- Use responsibility-based names inside realm directories, such as `server/time_tracking.lua` or `client/menu.lua`. Realm prefixes are optional there; retain them when following an established project convention.
- In an existing addon, preserve its established layout unless a structural change is requested. The flat `sh_`/`sv_`/`cl_` layout in gm_express remains a useful reference, but is not the default for new work.
- Keep authoritative decisions and server-only dependencies on the server; keep presentation on the client.
- Share logic that genuinely runs in both realms. Keep differences in small realm-specific functions.
- Make dependency and include order explicit. Send client/shared files with `AddCSLuaFile` and execute the appropriate files with `include`; these have different purposes.
- Realm folder names organize code; they do not automatically control execution or distribution. The loader must enforce the intended realm.
- Do not create empty realm folders/files or a recursive loading framework for a small addon.

Preferred layout, showing representative files only. Create the folders and features the addon actually needs:

```text
lua/
  autorun/
    sh_myaddon_init.lua
  myaddon/
    shared/
      config.lua
    server/
      storage.lua
      time_tracking.lua
    client/
      menu.lua
```

Load required shared definitions first, then server dependencies before their consumers, and client files on the client. Keep load order explicit in the initializer. Shared files may contain guarded realm-specific sections when a cohesive public API needs them, but substantial server-only work belongs under `server/`.

Split additional files by a concrete responsibility when needed, such as `server/permissions.lua`. File count is a consequence of responsibilities, not a target. Shared configuration code does not imply that server and client values are automatically synchronized; keep database credentials and other server-only settings on the server.

## 2. Expose a small, understandable API

- Use one distinct addon namespace for the public API and intentionally shared state.
- Prefer names that describe the operation: `Register`, `Send`, `Receive`, or feature-specific equivalents.
- Use local functions and variables for implementation details that no other file needs.
- Follow the project's convention for shared internal helpers. A leading underscore communicates intent but does not provide privacy.
- Use `:` when a method needs its namespace/object as `self`; use `.` for functions that do not. Keep calls consistent with definitions.
- Let callers express what they want without rebuilding transport, serialization, or lifecycle logic at every call site.
- Reuse existing helpers and platform APIs before adding an abstraction or dependency.
- A wrapper is useful when it adapts realm behavior or hides a meaningful detail. Do not add wrappers solely for symmetry.

## 3. Make the main flow readable

- Give a function one coherent task. Extract a helper when it removes real duplication or names a meaningful operation.
- Use descriptive names for significant data. Short names such as `ply` are appropriate where their meaning is established.
- Use guard clauses for invalid input, missing callbacks, or work that is no longer needed.
- Keep the normal path easy to follow without deeply nested conditionals.
- Keep named handlers separate from hook/network registration when their logic benefits from a name or independent inspection. Small anonymous callbacks are fine.
- Match surrounding whitespace, indentation, and naming. Consistency improves readability; whitespace does not make code run faster.
- Group related helpers together. Split a growing catch-all helper file by responsibility instead of making every utility depend on it.

### Default naming and syntax

These defaults are adapted from the [CFC GLua style guidelines](https://github.com/CFC-Servers/cfc_glua_style_guidelines/blob/761391c3986dc6ba0f278be643f6183941d34672/README.md).

| Item | Default |
| --- | --- |
| Local variables and local functions | `camelCase`, such as `sessionStart` and `updateTimes` |
| Constants | `SCREAMING_SNAKE_CASE`, such as `MAX_SESSION_DURATION`; keep them local when possible |
| New global addon namespaces | `PascalCase`, such as `MyAddon`; minimize other globals |
| Object methods | `PascalCase`, such as `storage:UpdateBatch()` |
| Ordinary record fields | Descriptive Lua identifiers, such as `session.joinedAt`; use bracket access for genuine dictionary keys, numeric indexes, and external formats |
| Unused return values or loop positions | `_` |
| Hook registration identifiers | `Organization_AddonName_Purpose`, such as `ACME_Playtime_SaveSession` |
| Custom hook events | An organization-prefixed event name, such as `ACME_PlaytimeUpdated` |

Use the actual project or organization prefix; `ACME` is only an example. A hook identifier should describe the work without repeating its event name. Use an addon prefix when no organization name exists. Keep timer and network identifiers distinctive as well.

- Write `and`, `or`, `not`, and `~=` instead of GMod's alternate spellings.
- Use Lua comments: `--` and `--[[ ... ]]`.
- Prefer a simple conditional or a helper with guard clauses over `continue`. Avoid replacing it with deeply nested code. Standard syntax is friendlier to Lua tools; GMod API calls still require GMod.
- Omit optional semicolons. Use commas to separate table entries.
- Use double-quoted strings for new code in this guide's default style. CFC allows either quote style consistently; preserve the surrounding project's choice.
- Avoid redundant parentheses, but keep grouping that makes arithmetic or boolean precedence easier to understand.
- Write fractions with a leading zero, such as `0.15`. Avoid unnecessary leading zeros on integers and trailing zeros on decimals, except where aligned precision helps explain the values.

### Default spacing and layout

- Indent with four spaces, without tabs.
- Put spaces around binary operators and after commas.
- Pad nonempty parentheses and braces: `sendData( ply, { enabled = true } )`. Keep empty pairs compact: `getData()` and `{}`.
- Do not pad square brackets: `sessions[steamID64]`.
- Put a space after a comment marker, and before an inline comment.
- Separate top-level blocks with one or two blank lines; use no more than one blank line between logical groups inside a function. Avoid larger blank runs.
- Separate a return from preceding statements with one blank line. A return-only body or a short inline guard can remain compact.
- For multiline tables and calls, start entries/arguments on the following line, indent one level, and put the closing delimiter on its own line. Keep short, readable forms on one line.
- Aim for about 110 characters per code line. Treat this as a readability target rather than a reason to damage a clear expression.
- Break complicated calculations into meaningfully named steps. Name significant limits and use existing constants or helpers such as `math.pi` and `math.rad` where appropriate.

Example of the default formatting and local naming, assuming timestamps are numeric:

```lua
local function getSessionDuration( joinedAt, departedAt )
    local finishedAt = departedAt or os.time()
    local elapsedSeconds = finishedAt - joinedAt

    return math.max( elapsedSeconds, 0 )
end
```

### Preserve safety while simplifying

- When extracting long conditions into variables, preserve short-circuit evaluation. Check an entity before calling its methods; check an owner or vehicle before dereferencing it. A named condition must not eagerly execute an operation that previously ran only after a guard.
- Use `IsValid` when an entity can genuinely be absent or removed, especially after timers, HTTP, or database callbacks. Avoid repeating it during one uninterrupted path after validity is established and no intervening operation can invalidate the object.
- Rely on a hook/callback's documented guarantees only in the appropriate realm. For example, `net.Receive` provides the sending player on the server; a client receiver does not receive that player argument as a usable sender entity. See the [callback contract](https://wiki.facepunch.com/gmod/net.Receive).
- Retain permission checks and payload validation even if a validity check is unnecessary. A valid player is not automatically authorized.
- Pass separate values to `print` for ordinary debugging instead of building a long concatenated string. Use explicit formatting when exact spacing or structured output matters.
- When a concatenated value is known to be numeric, explicit `tostring` is unnecessary. Keep deliberate conversion or formatting for values of unknown type or special display requirements.

## 4. Model readiness explicitly

- Distinguish code being loaded, dependencies being ready, and a client being ready to receive a particular message.
- Use a readiness event or handshake when correctness depends on it. An arbitrary delay is not proof of readiness.
- If requests can arrive before a dependency is ready, queue only the necessary work and drain the queue once readiness is established.
- Give queues a limit and a failure/timeout path when readiness can fail. Clear completed or cancelled work.
- Namespace hook and timer identifiers to avoid collisions with other addons.
- Remove temporary hooks after their job is complete. Use periodic timers only for work that must recur.
- After HTTP responses or timers, re-check any player, entity, or panel that the callback is about to use.
- Consider initialization and cleanup during reloads when the addon is expected to support them.

## 5. Keep networking proportional to the feature

- Define a clear message contract: sender, recipient, fields, limits, and callback behavior in each realm.
- Match reads and writes in order and type. Keep message names consistent and addon-specific.
- Send only the necessary data to the necessary recipients.
- Prefer built-in networking for ordinary small messages. Add a bulk-transfer dependency when the actual payloads and measurements justify it.
- Validate client-originated data on the server: shape, field ranges, permissions, ownership, and allowed operation.
- Apply size and request-rate limits before expensive work where possible; validate decoded data before applying it.
- A transport library delivering a table does not establish that the requested action is authorized.
- Document any external service requirement, timeout behavior, and effect of service failure.

## 6. Optimize repeated work and control resource lifetime

- Identify the expensive step before optimizing: lookup, allocation, encoding, compression, upload, download, or rendering.
- Cache work only when reuse is likely and invalidation is understood. Define expiration and cleanup alongside insertion.
- Expiration alone is not a strict memory bound. Add an entry/byte cap when high input volume can make the cache large before cleanup.
- Document exactly what a cache avoids. A lookup performed after encoding and hashing still pays those CPU costs.
- Keep callback timing consistent across cache hits and misses when callers rely on asynchronous behavior.
- Bound retries and use a delay appropriate to the failure. Define what happens when the retry budget is exhausted.
- Remove pending callbacks after completion, timeout, cancellation, or relevant disconnection.
- Asynchronous HTTP avoids waiting synchronously for the response. Lua encoding, hashing, compression, and decoding around it can still take time on the game thread.
- Use local aliases, lookup tables, reusable buffers, or specialized encodings in demonstrated hot paths. Do not spread serializer-level optimization across ordinary addon code without evidence.
- Measure realistic payload sizes, repetition, concurrency, and failure conditions. Record the environment and tradeoffs with performance claims.

### Persistence and periodic work

- For accumulated state such as playtime, keep active state in memory and persist on an appropriate interval instead of writing to the database every frame or on every read.
- Compute accumulated time from elapsed timestamps rather than assuming the timer fired exactly on schedule. Choose wall-clock or monotonic/game time according to what the feature measures.
- Group related writes into a transaction where supported. A transaction containing several updates is not necessarily one bulk SQL statement; describe the saving accurately.
- Keep business logic separate from database-specific queries. Introduce alternate storage implementations when multiple backends are actually required; use one direct implementation when only one is needed.
- If backends share an API, define success, failure, result types, readiness, and callback timing consistently. Never assume that a callback interface makes a synchronous backend asynchronous.
- Parameterize or appropriately escape SQL values, and allowlist dynamic identifiers. Keep configurable backend names restricted to supported implementations.
- Remove pending persistence state only after confirmed successful storage. Plan for disconnects during initialization, updates still in flight, and shutdown flushing when data loss matters.
- Choose a save interval with its tradeoff in mind: longer intervals reduce write frequency but increase the amount of recent state at risk on a crash.

## 7. Explain decisions and report useful failures

- Comments should explain constraints, assumptions, unusual timing, or compatibility decisions.
- Document public function arguments, realm differences, return values, callbacks, and required dependencies.
- Include short examples of the normal integration path.
- Use named constants or configuration for meaningful limits and environment-dependent settings. Do not make every literal configurable.
- Give errors enough context to identify the operation and cause; avoid logging secrets or entire sensitive payloads.
- Treat expected network/service failures as recoverable where appropriate. Reserve assertions for conditions that should not occur in correct operation.
- Keep protocol compatibility and dependency expectations explicit when integrating an external API.

## 8. Verify the behavior that matters

- Use the existing GLua lint/format configuration. Do not create a competing style setup. For new CFC-style projects without one, use GLuaLint/GLuaFixer and the [CFC configuration referenced by the guidelines](https://cfc.gg/configs/gluafixer/glualint.json) once it is retrievable and verified; do not invent its contents or claim it ran without checking.
- Check loading in both relevant realms and exercise a representative feature path.
- For networking changes, check unauthorized/malformed input and relevant size limits.
- For asynchronous changes, check delayed readiness, failure/timeout, and objects disappearing before callbacks run.
- For caching changes, check reuse, expiration, invalidation, and cleanup.
- For persistence changes, check write failures, overlapping saves, disconnects during initialization, and the supported shutdown behavior. Exercise every changed storage backend.
- Check reload behavior when supported. Do not assume a clean startup proves reload safety.
- Add focused automated checks where the environment supports them. Linting alone does not prove runtime correctness.
- State which checks actually ran and what still requires an in-game client/server session.

## Reference 1: gm_express

Reviewed: November 8, 2026, from the repository's `main` branch. Links below track that branch and may change. This is a source review, not an in-game benchmark or a complete correctness/security audit.

Express is a bulk networking library. It uploads encoded data to an HTTP service and sends a reference through Garry's Mod networking so the recipient can fetch it. Its transport design addresses a specific large-payload problem; it should not become a mandatory architecture for every addon.

### Observed patterns and why they help

| Evidence | Interpretation |
| --- | --- |
| A two-line [autorun entry point](https://github.com/CFC-Servers/gm_express/blob/main/lua/autorun/gm_express_init.lua) starts a [shared initializer](https://github.com/CFC-Servers/gm_express/blob/main/lua/gm_express/sh_init.lua), which loads helpers and the relevant realm. | Readers can locate startup and follow dependency order directly. |
| [Client](https://github.com/CFC-Servers/gm_express/blob/main/lua/gm_express/cl_init.lua) and [server](https://github.com/CFC-Servers/gm_express/blob/main/lua/gm_express/sv_init.lua) implementations adapt `Send`, `Receive`, and proof handling around shared internals. | The consumer API stays small while realm-specific details have a clear home. |
| The send path funnels through `_send`, `_put`, and `Put` in the [helpers](https://github.com/CFC-Servers/gm_express/blob/main/lua/gm_express/sh_helpers.lua) and shared initializer. | Encoding, cache reuse, upload, and notification are composed once rather than repeated by consumers. |
| `_waitingForAccess` queues requests; client receiver announcements wait for access; [playerload](https://github.com/CFC-Servers/gm_express/blob/main/lua/includes/modules/playerload.lua) emits a player readiness event. | Readiness is represented in state and events rather than assumed from file loading alone. |
| The upload cache keys prepared payloads by hash, expires entries, and periodically prunes them. Cache-hit callbacks are deferred with a zero-delay timer. | Repeated prepared payloads can reuse an upload, stale entries are removed, and callback timing follows the asynchronous path. |
| A temporary `Tick` hook removes itself before startup HTTP work; pre-download receivers can reject a transfer. | Temporary work ends explicitly, and consumers can reject unwanted bulk data before downloading it. |
| The bundled [SFS serializer](https://github.com/CFC-Servers/gm_express/blob/main/lua/includes/modules/sfs.lua) uses local aliases, type dispatch tables, buffered string assembly, and reusable encoding storage. | Optimization is concentrated in a frequently repeated operation behind an encode/decode interface. The implementation belongs to the bundled dependency, not to every feature module. |
| A [GLuaLint workflow](https://github.com/CFC-Servers/gm_express/blob/main/.github/workflows/glualint.yml) checks changes. | Automated style checks reinforce consistency. They do not demonstrate behavioral test coverage. |

The [README](https://github.com/CFC-Servers/gm_express#readme) provides realm-specific examples, callback contracts, lifecycle hooks, and workload-dependent benchmark results. Its results show an advantage for larger transfers and overhead for smaller ones. Use that as an example of explaining tradeoffs, not as a universal crossover threshold.

### Limits to carry forward into new implementations

These are recommendations inferred from the review, not claims that Express already implements them:

- Add failure handling and resource limits around pending access requests and unfulfilled proof callbacks. Removing proof callbacks only after success leaves unanswered requests without an expiry path in the reviewed implementation.
- Preserve the upload-cache idea while recognizing its scope: serialization and hashing happen before the cache lookup, and timed pruning does not enforce a maximum number of entries.
- Preserve useful validation boundaries while adding the feature's own permission checks, rate limits, and decoded-data validation.
- Keep HTTP response handling asynchronous without describing all surrounding Lua processing as nonblocking.
- Preserve dependency boundaries and attribution. Reuse a serializer when required; do not copy its binary-format machinery into unrelated addon code.
- Treat the existing file layout, underscore naming, delays, and constants as examples to understand. Choose them only where the new addon's responsibilities and constraints support them.

## Reference 2: cfc_time

Reviewed: November 8, 2026, from the repository's `master` branch. Links below track that branch and may change. This is a source review; the addon and its database backends have not been run here.

cfc_time tracks player sessions and accumulated playtime, persists sessions through SQLite or MySQL, and exposes a compatibility layer for addons expecting Utime. The user explicitly prefers its realm-directory structure for new addons.

### Observed patterns and why they help

| Evidence | Interpretation |
| --- | --- |
| The [main addon directory](https://github.com/CFC-Servers/cfc_time/tree/master/lua/cfc_time) contains `shared/` and `server/`, with names such as `config.lua`, `storage.lua`, and `time_tracking.lua`. | The directory communicates execution realm; the filename communicates responsibility. This improves navigation without repeating realm prefixes on every file. |
| The [initializer](https://github.com/CFC-Servers/cfc_time/blob/master/lua/autorun/sh_cfc_time_init.lua) loads shared configuration and compatibility code before server storage and tracking. | Load order is visible in one short file, and server files are included inside a server guard. |
| [Tracking](https://github.com/CFC-Servers/cfc_time/blob/master/lua/cfc_time/server/time_tracking.lua) maintains tables keyed by SteamID64 and a separate map to player entities. | Session identity, database identity, and transient player entities have distinct roles; consumers avoid repeated searches for tracked players. |
| `updateTimes` calculates elapsed time, builds pending updates, and passes them to `Storage:UpdateBatch` on a configurable timer. | Recurring persistence is consolidated rather than performed continuously. Each pass still visits tracked sessions and submits their updates; this is not a dirty-entry-only algorithm. |
| [Storage selection](https://github.com/CFC-Servers/cfc_time/blob/master/lua/cfc_time/server/storage.lua) includes one of two actual implementations: [SQLite](https://github.com/CFC-Servers/cfc_time/blob/master/lua/cfc_time/server/storage_options/sqlite.lua) or [MySQL](https://github.com/CFC-Servers/cfc_time/blob/master/lua/cfc_time/server/storage_options/mysql.lua). Both expose `PlayerInit`, `GetTotalTime`, `CreateSession`, and `UpdateBatch`. | An abstraction is justified by real alternate implementations, keeping most tracking logic independent of SQL dialect and driver details. |
| SQLite uses transaction boundaries for grouped updates, while MySQL uses driver transactions and [prepared statements](https://github.com/CFC-Servers/cfc_time/blob/master/lua/cfc_time/server/storage_options/utils/mysql.lua). | Database-specific mechanisms stay near the database code; transaction grouping can reduce repeated commit overhead. |
| [Utime compatibility](https://github.com/CFC-Servers/cfc_time/blob/master/lua/cfc_time/shared/utime_compat.lua) exposes expected methods and reacts to addon hooks; server migration work is guarded. | Legacy integration is concentrated in a named module rather than scattered throughout tracking calculations. New integrations can follow the same hook boundaries. |
| Tracking uses a scoped logger, named lifecycle functions, and comments describing the shapes of its state tables. | Readers can identify ownership and follow initialization, updates, and cleanup without reverse-engineering every table. |

### Limits to carry forward into new implementations

These recommendations extend the useful patterns rather than assuming the reference is a complete implementation standard:

- Keep realm folders as a navigation convention, with explicit loading rules. Add a `client/` folder only when there is client-only code; cfc_time does not need one for its current organization.
- Keep significant migration/database work server-only even when the compatibility API is shared. A shared file can include server guards, so its folder alone does not describe every statement inside it.
- Treat the two storage backends as an intended common interface, not proof of identical behavior. SQLite runs queries and callbacks synchronously here; MySQL completes through driver callbacks. New implementations should define and check their full contract.
- Confirm database success before discarding pending data. The reviewed SQLite batch implementation commits and calls its completion callback without checking each query result; its structure alone does not establish failure-safe persistence.
- Define database readiness separately from files being loaded, and handle saves overlapping with earlier asynchronous writes. Revalidate player state after delayed initialization results.
- Validate configuration values, including positive update intervals and supported backend names. Keep server-only credentials separate from shared defaults and avoid echoing them in logs.
- Keep compatibility globals and Player metatable methods deliberate: they serve this addon's Utime replacement goal and are not a reason for unrelated addons to create extra globals or extend engine metatables.

Across the first two examples, the recurring principles are a small entry point, an addon namespace, explicit realm boundaries, named lifecycle events, and shared operations behind a small API. The chosen folder layout follows the user's cfc_time preference; the amount of transport, storage, or compatibility machinery follows the feature's requirements.

## Reference 3: CFC GLua style guidelines

Reviewed: November 8, 2026, at commit `761391c3986dc6ba0f278be643f6183941d34672`. [Source document](https://github.com/CFC-Servers/cfc_glua_style_guidelines/blob/761391c3986dc6ba0f278be643f6183941d34672/README.md).

This reference describes CFC's intended coding conventions directly. Its central emphasis is consistent presentation: predictable names, familiar Lua syntax, readable spacing, manageable expressions, early returns, and comments that explain something the code does not already say. The default naming and layout sections above adapt those conventions for this guide.

### Interpretation and boundaries

- Formatting and naming mainly reduce reading and maintenance effort. Do not claim a runtime speedup from indentation, quote choice, or blank lines.
- Native Lua syntax improves compatibility with Lua-aware tooling. Facepunch also recommends avoiding GMod's custom operator aliases when portability matters: [Operators & Aliases](https://wiki.facepunch.com/gmod/Specific_Operators).
- Avoiding unnecessary checks, conversions, or string assembly can remove work, but correctness and measured hot paths remain the priority. CFC's validity-check advice is contextual, not a blanket ban on `IsValid`.
- The long-condition examples illustrate naming, not a safe template to copy verbatim. One example computes entity/owner/vehicle expressions before applying its entity-validity boolean. Keep guards ahead of those operations in generated code.
- Some addon code uses naming that differs from the style document, such as lower-case methods or the existing `express` namespace. Preserve those integration contracts; use the style defaults for new APIs rather than mechanically renaming established ones.
- CFC's style document does not establish a realm-directory layout. The `shared/`, `server/`, and optional `client/` preference comes from the user's cfc_time example.
- The guidelines link an external GLuaFixer configuration. Its contents could not be retrieved during this review, so no specific configuration values or lint results are asserted here.

## Instructions when using this guide

Before editing, inspect the addon's entry point, realm boundaries, existing API, dependencies, and relevant callers. Implement the requested behavior using the existing structure when it is suitable. Explain material performance tradeoffs and report the checks performed. As additional reference addons are reviewed, update this guide with evidence and distinguish repeated conventions from project-specific choices.
