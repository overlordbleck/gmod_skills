# Garry's Mod Lua Documentation and Diagnostics

Use for comments, API documentation, configuration explanations, logging, and error reporting. Use with the [addon guide](../gmod_skill.md).

## Explain decisions and report useful failures

- Comments should explain constraints, assumptions, unusual timing, or compatibility decisions.
- Document public function arguments, realm differences, return values, callbacks, and required dependencies.
- Include short examples of the normal integration path.
- Use named constants or configuration for meaningful limits and environment-dependent settings. Do not make every literal configurable.
- Give errors enough context to identify the operation and cause; avoid logging secrets or entire sensitive payloads.
- Treat expected network/service failures as recoverable where appropriate. Reserve assertions for conditions that should not occur in correct operation.
- Keep protocol compatibility and dependency expectations explicit when integrating an external API.

## Debug output

- Pass separate values to `print` for ordinary debugging instead of building a long concatenated string. Use explicit formatting when exact spacing or structured output matters.
- When a concatenated value is known to be numeric, explicit `tostring` is unnecessary. Keep deliberate conversion or formatting for values of unknown type or special display requirements.
