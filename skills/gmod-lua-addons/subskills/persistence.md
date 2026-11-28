# Garry's Mod Lua Persistence

Use for persistent state, database contracts, periodic saves, transactions, and failure handling. Use the [structure subskill](structure.md#storage-and-compatibility-boundaries) for storage module boundaries. Use with the [addon guide](../SKILL.md).

## Persistence and periodic work

- Keep persistent identifiers separate from transient entity references, and document the shape and ownership of stored state.
- Follow the [configuration subskill](configuration.md) when validating save intervals, backend selection, and live setting changes.
- For accumulated state such as playtime, keep active state in memory and persist on an appropriate interval instead of writing to the database every frame or on every read.
- Compute accumulated time from elapsed timestamps rather than assuming the timer fired exactly on schedule. Choose wall-clock or monotonic/game time according to what the feature measures.
- Group related writes into a transaction where supported. A transaction containing several updates is not necessarily one bulk SQL statement; describe the saving accurately.
- If backends share an API, define success, failure, result types, readiness, and callback timing consistently. Never assume that a callback interface makes a synchronous backend asynchronous.
- Parameterize or appropriately escape SQL values, and allowlist dynamic identifiers. Keep configurable backend names restricted to supported implementations.
- Remove pending persistence state only after confirmed successful storage. Plan for disconnects during initialization, updates still in flight, and shutdown flushing when data loss matters.
- Choose a save interval with its tradeoff in mind: longer intervals reduce write frequency but increase the amount of recent state at risk on a crash.
