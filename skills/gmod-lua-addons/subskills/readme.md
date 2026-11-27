# Repository README

Use when writing or updating a repository's README. Use with the [addon guide](../gmod_skill.md). Keep explanations short, direct, and grounded in the current codebase.

## Allowed content

The README should contain only the project title and the following subjects, in this order. Omit subjects that do not apply rather than adding empty sections.

1. **Project summary:** Explain what the project does and its purpose in one short paragraph when possible. Use at most two paragraphs; prefer fewer words over filling the limit.
2. **Dependencies:** List the dependencies actually required by the project. Identify optional integrations as optional, and include version or realm requirements only when they matter for use.
3. **Global methods:** Document the public methods and functions exposed by the codebase, including those on its global addon namespace. Prefer a table with `Method`, `Description`, `Realm`, and `Returns` columns; show arguments in the method signature. Exclude local helpers and internal implementation details.
4. **Examples:** Include short, working examples of interfacing with the project through its public API. Show the normal integration path, with only the context needed to understand arguments, callbacks, realm, and required setup.
5. **ConVars:** List the ConVars added by the project in a table with exactly `ConVar name`, `Description`, and `Default value` columns. Include realm or restrictions in the description when needed.

Do not add separate installation guides, architecture explanations, testing instructions, benchmarks, roadmaps, changelogs, contribution instructions, or other subjects unless the user explicitly requests an exception. Keep detailed development notes outside the README.

## Derive documentation from the code

- Inspect the entry points, public API definitions, relevant callers, dependency checks, and ConVar registrations before writing. Follow wrappers or computed definitions when needed to establish the actual behavior.
- Preserve exact method names, argument order, `:` versus `.` usage, return behavior, and callback contracts. Do not invent API methods or list private helpers merely because they are accessible through a global table.
- Derive ConVar defaults from their registration or initialization code, not a running server's overridden values. Describe a conditional default briefly when there is no single fixed value.
- Verify examples against the current API and loading requirements. Do not claim an example was tested in game unless it was.
- If a fact cannot be established from the available code, report the uncertainty to the user rather than filling the README with guessed values or unfinished placeholders.
- When updating an existing README, remove stale or out-of-scope material so the result follows these content limits. Preserve useful facts within the allowed sections without repeating them.

## Keep it concise

- Use short dependency lists, method tables, and ConVar tables instead of long prose.
- Give each method or ConVar a brief description focused on what a consumer needs to know.
- Keep examples small and avoid repeating the same explanation in the summary, tables, and examples.
- Before finishing, check the two-paragraph summary limit, section scope, API accuracy, and ConVar defaults against the codebase.
