---
name: gmod-lua-addons
description: Build, modify, refactor, and review Garry's Mod Lua addons, including VGUI panels, using the repository's realm layout, formatting, security, performance, and manual in-game testing conventions. Use for GMod/GLua addon development, not unrelated Lua projects or game administration.
---

# Garry's Mod Lua Addons

Follow the subskills relevant to the task. The index below and linked subskills are the source of truth for addon conventions; do not load unrelated subskills. Apply explicit user instructions first, then the target repository's requirements; use these guides as defaults.

Leave README files unchanged during addon generation, implementation, fixes, refactoring, and general documentation work. Do not create, update, rename, delete, or reformat them as a side effect. README writes require the user to explicitly invoke `$readme-generation` for the current task; a previous invocation does not authorize README changes in later tasks. Reading a README for context is allowed.

For an explicitly invoked README task, follow the separate [README generation skill](../readme-generation/SKILL.md). Do not invoke it automatically. Structure guidance is for implementing the addon, not README content.

For every addon change, read the [manual testing subskill](subskills/testing.md). Do not create automated GMod Lua tests or mock game environments. Report which manual in-game checks were completed and which remain outstanding.

## Subskill index

| Subskill | Use for |
| --- | --- |
| [Structure](subskills/structure.md) | Addon layout, realm separation, loading, API boundaries, and code organization. |
| [Formatting](subskills/format.md) | Naming, syntax, spacing, layout, and lint/format configuration. |
| [VGUI](subskills/vgui.md) | Panel purpose, functional subpanels, registered panel classes, and client Derma file organization. |
| [Runtime safety and lifecycle](subskills/runtime.md) | Entity validity, safe simplification, readiness, callbacks, hooks, timers, and cleanup. |
| [Networking](subskills/networking.md) | Message contracts, transport choices, input validation, and external services. |
| [Security and permissions](subskills/security.md) | Server authority, authorization, ownership, untrusted input, rate limits, and safe file access. |
| [Configuration](subskills/configuration.md) | ConVars, configuration tables, defaults, validation, synchronization, and live changes. |
| [Player and entity lifecycle](subskills/entities.md) | Joining, spawning, death, disconnects, entity removal, ownership, and persistent identity. |
| [Hooks and interoperability](subskills/hooks.md) | Hook contracts, return values, overrides, addon integration, and compatibility. |
| [Distribution and dependencies](subskills/distribution.md) | Lua delivery, required assets, mounted content, packaging, and missing dependencies. |
| [Performance](subskills/performance.md) | Repeated work, caching, loop complexity, and performance measurements. |
| [Persistence](subskills/persistence.md) | Persistent state, database contracts, periodic saves, transactions, and storage failures. |
| [Documentation and diagnostics](subskills/documentation.md) | Comments, API documentation, configuration, logging, and error reporting. |
| [README generation](../readme-generation/SKILL.md) | Separate skill, only when the user explicitly invokes `$readme-generation` for the current task. Otherwise leave README files unchanged. |
| [Manual testing](subskills/testing.md) | Manual in-game verification, relevant feature checks, and reporting untested behavior. Use for every addon change. |
