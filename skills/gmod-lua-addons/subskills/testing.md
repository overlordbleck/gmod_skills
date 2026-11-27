# Garry's Mod Lua Manual Testing

Use when planning or reporting verification of addon changes. All addon behavior testing is manual and performed in game. Use with the [addon guide](../gmod_skill.md).

## Test addons manually in game

- Do not create automated tests, test harnesses, or mock GMod environments for Garry's Mod Lua addons.
- Test addon behavior manually in Garry's Mod, exercising the relevant client and server realms.
- For loop changes, follow the [loop complexity review and testing guidance](performance.md#review-and-test-loop-complexity) to minimize repeated work and check performance as input sizes grow.
- Provide manual testing steps for the changed behavior and report what was actually tested. If an in-game session is unavailable, state that manual testing remains outstanding. Do not report manual testing steps in README.md.
- Existing lint and format checks may still be used; they do not replace manual in-game testing.

## Verify the behavior that matters

- Check loading in both relevant realms and exercise a representative feature path.
- For networking changes, check unauthorized/malformed input and relevant size limits.
- For asynchronous changes, check delayed readiness, failure/timeout, and objects disappearing before callbacks run.
- For caching changes, check reuse, expiration, invalidation, and cleanup.
- For persistence changes, check write failures, overlapping saves, disconnects during initialization, and the supported shutdown behavior. Exercise every changed storage backend.
- Check reload behavior when supported. Do not assume a clean startup proves reload safety.
- State which checks actually ran and what still requires an in-game client/server session.
