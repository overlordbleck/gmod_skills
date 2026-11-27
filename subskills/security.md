# Garry's Mod Lua Security and Permissions

Use for privileged actions and untrusted input from players, commands, files, or external services. Use with the [addon guide](../gmod_skill.md) and [networking subskill](networking.md).

## Keep authority on the server

- Make gameplay, economy, ownership, and permission decisions on the server. Client UI restrictions are not authorization checks.
- Derive the caller from the server callback context, not an identity supplied in the payload. Define console behavior separately from player behavior for commands.
- Use the project's permission system and check the requested action against the specific target. A valid player or entity is not automatically authorized.
- Verify ownership through the project's established ownership API or authoritative state. Do not assume an engine owner field represents build or administrative permissions.
- Revalidate authorization and target state before a delayed action commits when they may have changed. Fail closed when required permission data is unavailable.

## Bound and validate input

- Validate types, lengths, numeric ranges, allowed operations, and referenced entities before applying changes. Reject non-finite numbers where finite values are required.
- Apply size and request-rate limits before expensive processing where possible. Bound decoded collections as well as raw input, and clean up rate-limit state when it is no longer needed.
- Prefer fixed operations and allowlisted identifiers to client-selected functions, commands, or code. Do not execute untrusted strings as Lua.
- Keep file operations under an addon-owned directory. Prefer server-selected filenames; reject path traversal, absolute paths, and unexpected separators in user-selected names.
- Keep credentials and private state server-only. Send clients only the fields they need, and avoid secrets in errors or logs.
- Follow the [persistence subskill](persistence.md) for SQL parameterization and dynamic identifier handling.

## Manual verification

Use the [manual testing subskill](testing.md). Check unauthorized callers, another player's targets, malformed and oversized input, repeated requests, and permission or ownership changes during delayed work. Confirm rejected requests leave state unchanged.
