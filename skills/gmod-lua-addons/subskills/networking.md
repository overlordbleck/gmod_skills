# Garry's Mod Lua Networking

Use for message contracts, transport choices, client input validation, and external service failures. Use with the [addon guide](../SKILL.md).

## Keep networking proportional to the feature

- Define a clear message contract: sender, recipient, fields, limits, and callback behavior in each realm.
- Match reads and writes in order and type. Keep message names consistent and addon-specific.
- Send only the necessary data to the necessary recipients.
- Prefer built-in networking for ordinary small messages. Add a bulk-transfer dependency when the actual payloads and measurements justify it.
- Apply the [security and permissions subskill](security.md) to client-originated data, including authorization, ownership, input validation, and request limits. Successful transport or decoding does not authorize an action.
- Document any external service requirement, timeout behavior, and effect of service failure.
