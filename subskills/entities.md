# Garry's Mod Lua Player and Entity Lifecycle

Use for player sessions, spawning, death, entity ownership, removal, and identity. Use with the [addon guide](../gmod_skill.md); use the [runtime subskill](runtime.md) for general validity and asynchronous cleanup rules.

## Match state to its lifetime

- Distinguish connection, authentication, initial spawn, subsequent spawns, death, and disconnect. Choose hooks by their documented guarantees and realm; do not treat one event as proof that every dependency is ready.
- Initialize session state once per session and spawn state on each applicable spawn. Reset only the state whose lifetime has ended; death does not necessarily end the player's session.
- Keep persistent player identity separate from the current player entity. Use the project's stable account identifier for saved records; do not use entity indexes or display names as persistent keys.
- Treat entity indexes as temporary identifiers that can be reused. For delayed work, retain and validate the intended entity and session rather than assuming an index still identifies the same object.
- Define behavior for bots and unauthenticated players when supported. Do not assume every player has a usable persistent account identity at every lifecycle stage.

## Handle removal and ownership changes

- Establish addon-owned state only when required entity data is ready. An entity's existence does not guarantee that its owner or networked properties are available.
- Define what happens to spawned objects when their owner dies, disconnects, or transfers ownership. Use the project's ownership and cleanup conventions.
- Release entity-associated timers, callbacks, and table entries when their work ends or the entity is removed. Make cleanup safe if multiple lifecycle paths invoke it.
- Prevent an old session's asynchronous result from modifying a new session. Compare session identity or a generation token when reconnects or resets can overlap pending work.
- Capture the identifiers and values needed for persistence before entity access becomes invalid. Coordinate outstanding saves with the [persistence subskill](persistence.md).
- Decide how map cleanup and supported Lua reloads rebuild or discard transient state. Do not recreate already-removed entities from stale callbacks.

## Manual verification

Use the [manual testing subskill](testing.md). Exercise join, death, respawn, disconnect/reconnect, entity removal, ownership changes, and delayed callbacks after removal. Include map cleanup and reloads where supported.
