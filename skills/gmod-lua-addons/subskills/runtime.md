# Garry's Mod Lua Runtime Safety and Lifecycle

Use for entity validity, safe simplification, readiness, asynchronous callbacks, hooks, timers, and cleanup. Use with the [addon guide](../gmod_skill.md). For player and entity state transitions, use the [entity lifecycle subskill](entities.md); for hook return contracts and addon integration, use the [hooks subskill](hooks.md).

## Preserve safety while simplifying

- When extracting long conditions into variables, preserve short-circuit evaluation. Check an entity before calling its methods; check an owner or vehicle before dereferencing it. A named condition must not eagerly execute an operation that previously ran only after a guard.
- Use `IsValid` when an entity can genuinely be absent or removed, especially after timers, HTTP, or database callbacks. Avoid repeating it during one uninterrupted path after validity is established and no intervening operation can invalidate the object.
- Rely on a hook/callback's documented guarantees only in the appropriate realm. For example, `net.Receive` provides the sending player on the server; a client receiver does not receive that player argument as a usable sender entity. See the [callback contract](https://wiki.facepunch.com/gmod/net.Receive).
- Retain the checks required by the [security subskill](security.md) even if a validity check is unnecessary. A valid player is not automatically authorized.

## Model readiness explicitly

- Distinguish code being loaded, dependencies being ready, and a client being ready to receive a particular message.
- Use a readiness event or handshake when correctness depends on it. An arbitrary delay is not proof of readiness.
- If requests can arrive before a dependency is ready, queue only the necessary work and drain the queue once readiness is established.
- Give queues a limit and a failure/timeout path when readiness can fail. Clear completed or cancelled work.
- Namespace hook and timer identifiers to avoid collisions with other addons.
- Remove temporary hooks after their job is complete. Use periodic timers only for work that must recur.
- After HTTP responses or timers, re-check any player, entity, or panel that the callback is about to use.
- Handle initialization and cleanup during reloads only when reload support is required. Do not add startup cleanup, compatibility scaffolding, or `hook.Remove` calls solely for autorefresh after a code change. If old state can remain in a running server, notify the user that a server restart may be required or provide an appropriate one-time server console command in the response.
- Bound retries and use a delay appropriate to the failure. Define what happens when the retry budget is exhausted.
- Remove pending callbacks after completion, timeout, cancellation, or relevant disconnection.
