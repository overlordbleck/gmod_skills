# Garry's Mod Lua Hooks and Interoperability

Use when registering hooks, exposing addon events, integrating another addon, or considering an override. Use with the [addon guide](../SKILL.md).

## Respect hook contracts

- Check the hook's arguments, realm, timing, and documented return semantics before choosing it. Use a hook that represents the required lifecycle event instead of repeatedly polling unrelated events.
- Return nothing from an observational callback. In the standard hook system, a non-nil return, including `false`, stops later callbacks and prevents the fallback gamemode method from running. Return a value only when intentionally implementing that hook's contract. See [hook.Add](https://wiki.facepunch.com/gmod/hook.Add).
- Do not rely on registration order between independent hooks. Put operations that require an order in an explicitly sequenced function or documented readiness flow.
- Use distinct addon-owned identifiers. Reuse the intended identifier during reload and remove only hooks the addon owns.
- Do not add `hook.Remove` calls for obsolete or renamed hooks solely to support autorefresh. Avoid startup cleanup or migration code whose only purpose is repairing state left by a previous code version. Instead, tell the user a server restart may be required, or provide the specific one-time Lua cleanup to run through a server console command when appropriate. Keep these operator instructions in the response, outside the addon code and README. Retain hook removal required by normal runtime behavior, such as completing a temporary hook's job or disabling a feature.
- For custom events, document arguments, realm, timing, and whether listeners may veto or replace a result. Do not design an event as both a guaranteed broadcast and a short-circuit query.

## Integrate through supported boundaries

- Prefer documented hooks and public APIs to replacing globals, gamemode methods, or engine metatable methods.
- When an override is actually required, preserve the original calling convention, arguments, and return values. Handle repeated installation without stacking wrappers; restore it only if the installed wrapper is still yours.
- Keep optional integrations in a small, explicit module. Check the capability being used and wait for documented readiness rather than assuming installation implies initialization.
- Preserve external API names and data contracts. Keep compatibility behavior localized instead of adding conditional integration code throughout unrelated features.
- Do not remove another addon's hooks or overwrite shared state to resolve a conflict. Identify the competing behavior and use a supported extension point or an explicit operator setting.
- Follow the [distribution subskill](distribution.md) for required versus optional dependencies.

## Manual verification

Use the [manual testing subskill](testing.md). Check operation with supported integrations enabled and absent, relevant hook return behavior, and repeated reloads only when reload support is required. Confirm the addon does not suppress unrelated callbacks or accumulate wrappers. Do not add autorefresh support just to satisfy a reload check.
