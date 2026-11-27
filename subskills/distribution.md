# Garry's Mod Lua Distribution and Dependencies

Use for packaging addons, delivering Lua and assets, mounted content, and dependency availability. Use with the [addon guide](../gmod_skill.md) and [structure subskill](structure.md) for realm loading.

## Deliver the files the addon needs

- Keep packaged files under the correct addon directories and use addon-specific paths to avoid collisions. Match filename case consistently, including on case-sensitive server filesystems.
- Include required runtime files and assets. Exclude credentials, local configuration containing secrets, development output, and unrelated files.
- Distinguish client Lua delivery from execution and asset downloads. `AddCSLuaFile` sends Lua; `include` executes it in the current realm. See [AddCSLuaFile](https://wiki.facepunch.com/gmod/Global.AddCSLuaFile).
- Choose the deployment's supported asset delivery method. Reference the required Workshop content or distribute the required resource files; do not assume server installation alone supplies client assets.
- `resource.AddWorkshop` provides Workshop asset downloads, not a replacement for sending client Lua. It does nothing useful for an addon containing only Lua. See [resource.AddWorkshop](https://wiki.facepunch.com/gmod/resource.AddWorkshop).
- Declare required mounted content and verify the actual models, materials, sounds, or other assets used. Avoid assuming that every client has the server's mounted content.

## Make dependency behavior explicit

- List only dependencies the feature actually uses. Distinguish required dependencies from optional integrations and content-only requirements.
- Check required capabilities before enabling dependent features. Provide an actionable error for a missing required dependency; disable only the affected feature when the rest can operate safely.
- For optional integrations, provide the documented standalone behavior when unavailable. Do not silently substitute behavior with different permissions or persistence guarantees.
- Keep load order and readiness explicit. A dependency being installed or its namespace existing does not prove that its services are ready.
- Document supported dependency versions or capabilities when compatibility depends on them. Keep installation requirements separate from runtime configuration.
- Preserve licenses and attribution when redistributing dependencies or assets. Do not bundle unrelated code or content merely because it is available locally.

## Manual verification

Use the [manual testing subskill](testing.md). Verify installation on a server and client without development copies or previously cached addon assets. Check Lua loading, asset delivery, missing required content, absent optional integrations, and actionable dependency failures.
