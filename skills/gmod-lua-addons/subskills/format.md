# Garry's Mod Lua Formatting

Use this subskill for formatting, naming, syntax conventions, and lint/format configuration when working with the [addon guide](../gmod_skill.md).

Apply explicit user instructions first, then the target repository's requirements. Use these conventions as defaults for new code. Preserve established public API names and external data formats; do not rename them just to match a style preference.

- Match surrounding whitespace, indentation, and naming. Consistency improves readability; whitespace does not make code run faster.
- Use descriptive names for significant data. Short names such as `ply` are appropriate where their meaning is established.

## Default naming and syntax

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
- Use double-quoted strings for new code in this guide's default style. Preserve the surrounding project's choice.
- Avoid redundant parentheses, but keep grouping that makes arithmetic or boolean precedence easier to understand.
- Write fractions with a leading zero, such as `0.15`. Avoid unnecessary leading zeros on integers and trailing zeros on decimals, except where aligned precision helps explain the values.

## Default spacing and layout

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

## Lint and format checks

- Use the existing GLua lint/format configuration. Do not create a competing style setup.
- For new projects without a configuration, choose tooling compatible with GLua and configure it to match the agreed conventions. Verify tool availability and configuration before claiming checks ran.
- Automated style checks reinforce consistency; they do not demonstrate behavioral test coverage.

## Applying style safely

- Formatting and naming reduce reading and maintenance effort; indentation, quote choice, and blank lines do not improve runtime speed.
- Native Lua syntax improves compatibility with Lua-aware tooling; GMod API calls still require GMod.
- When simplifying expressions, preserve short-circuit evaluation and keep validity guards before entity, owner, or vehicle method calls. Style changes must preserve behavior.
- Use the [structure subskill](structure.md) for realm directories and code organization.
