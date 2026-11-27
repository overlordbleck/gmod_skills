# Garry's Mod Lua Addon Structure

Use this subskill for addon layout, realm separation, loading, API boundaries, and code organization when working with the [addon guide](../gmod_skill.md). For formatting and naming conventions, use the [formatting subskill](format.md).

Apply explicit user instructions first, then the target repository's requirements. Preserve an existing addon's structure unless a change is requested or needed for the feature. Use the following defaults for new addons.

## Before editing

Build addons that are easy to navigate, understand, and change, with predictable client/server behavior and appropriate runtime costs. Match the existing project's conventions before introducing a new structure. Prefer the smallest implementation that meets the actual requirements.

Before editing, inspect the addon's entry point, realm boundaries, existing API, dependencies, and relevant callers. Implement the requested behavior using the existing structure when it is suitable. Explain material performance tradeoffs and report the checks performed.

## Keep loading explicit and separate execution realms

- Keep the autorun entry point small. Use it to start the addon, not to contain its features.
- Put addon-owned code under a uniquely named directory.
- For new addons, organize that directory into `shared/`, `server/`, and `client/` subdirectories as needed.
- Use responsibility-based names inside realm directories, such as `server/time_tracking.lua` or `client/menu.lua`. Realm prefixes are optional there; retain them when following an established project convention.
- For synthesized VGUI, follow the [VGUI subskill](vgui.md): put each registered main panel and functional subpanel in its own file under `myaddon/client/derma/`, such as `myaddon/client/derma/panel.lua`.
- In an existing addon, preserve its established layout unless a structural change is requested.
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

## Expose a small, understandable API

- Use one distinct addon namespace for the public API and intentionally shared state.
- Prefer names that describe the operation: `Register`, `Send`, `Receive`, or feature-specific equivalents.
- Use local functions and variables for implementation details that no other file needs.
- Follow the project's convention for shared internal helpers. A leading underscore communicates intent but does not provide privacy.
- Use `:` when a method needs its namespace/object as `self`; use `.` for functions that do not. Keep calls consistent with definitions.
- Let callers express what they want without rebuilding transport, serialization, or lifecycle logic at every call site.
- Reuse existing helpers and platform APIs before adding an abstraction or dependency.
- A wrapper is useful when it adapts realm behavior or hides a meaningful detail. Do not add wrappers solely for symmetry.

## Make the main flow readable

- Give a function one coherent task. Extract a helper when it removes real duplication or names a meaningful operation.
- Use guard clauses for invalid input, missing callbacks, or work that is no longer needed.
- Keep the normal path easy to follow without deeply nested conditionals.
- Keep named handlers separate from hook/network registration when their logic benefits from a name or independent inspection. Small anonymous callbacks are fine.
- Group related helpers together. Split a growing catch-all helper file by responsibility instead of making every utility depend on it.

## Storage and compatibility boundaries

- Keep business logic separate from database-specific queries. Introduce alternate storage implementations when multiple backends are actually required; use one direct implementation when only one is needed.
- Keep legacy compatibility code in a dedicated module. Add compatibility globals or extend engine metatables only when the integration requires them.
- Keep database migrations and server-only compatibility work on the server, even when the public compatibility API is shared.
- Preserve dependency boundaries and attribution. Reuse a serializer when required; do not copy its binary-format machinery into unrelated feature modules.

## Test addons manually in game

Do not create automated tests, test harnesses, or mock GMod environments for Garry's Mod Lua addons. Test addons manually in game using the [testing subskill](testing.md), including its guidance for loop performance checks.
