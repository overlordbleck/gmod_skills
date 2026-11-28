# Garry's Mod Lua Configuration

Use for addon settings, defaults, validation, realm visibility, and applying configuration changes. Use with the [addon guide](../SKILL.md).

## Choose a clear source of truth

- Use ConVars for scalar settings that operators or players should adjust through the console. Use configuration tables for structured settings and related data. Follow existing project conventions.
- Define each setting's owner, type, default, valid range, persistence, and when changes take effect. Avoid multiple writable sources for the same value.
- Prefix ConVar names with the addon name. Document meaningful settings without making every implementation constant configurable.
- Keep server policy authoritative on the server and client preferences local to the client. Shared Lua files do not synchronize table values.
- Replicate only settings clients need. Never put credentials or private server settings in shared files or replicated values.

## Validate and apply changes

- Validate both initial values and later changes before use. Check positive timer intervals, bounded counts, supported enum values, and finite numeric values as appropriate.
- Use explicit defaults that preserve valid `false` and zero values. Report invalid settings with enough context to correct them; choose a safe fallback or disable the affected feature when continuing would be unsafe.
- Choose ConVar flags deliberately: persistence and replication serve different purposes. Confirm realm behavior against the [ConVar flags documentation](https://wiki.facepunch.com/gmod/Enums/FCVAR).
- Apply live changes through one update path. Reconfigure affected timers, caches, or limits without leaving duplicate work running or mixing incompatible old and new state.
- Document settings that require a reload or restart. For networked settings, provide current values to newly ready clients as well as updates to existing clients.
- Restrict server configuration changes to authorized operators using the [security subskill](security.md).

## Manual verification

Use the [manual testing subskill](testing.md). Check defaults, invalid values, valid boundary values, live changes, reconnects, and persistence across restart where supported. Verify clients cannot override server policy.
