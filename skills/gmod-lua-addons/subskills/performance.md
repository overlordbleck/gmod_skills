# Garry's Mod Lua Performance

Use for repeated work, caching, loop complexity, and manual performance measurements. Use with the [addon guide](../gmod_skill.md).

## Optimize repeated work and bound caches

- Identify the expensive step before optimizing: lookup, allocation, encoding, compression, upload, download, or rendering.
- Cache work only when reuse is likely and invalidation is understood. Define expiration and cleanup alongside insertion.
- Expiration alone is not a strict memory bound. Add an entry/byte cap when high input volume can make the cache large before cleanup.
- Document exactly what a cache avoids. A lookup performed after encoding and hashing still pays those CPU costs.
- Keep callback timing consistent across cache hits and misses when callers rely on asynchronous behavior.
- Asynchronous HTTP avoids waiting synchronously for the response. Lua encoding, hashing, compression, and decoding around it can still take time on the game thread.
- Use local aliases, lookup tables, reusable buffers, or specialized encodings in demonstrated hot paths. Do not spread serializer-level optimization across ordinary addon code without evidence.
- Measure realistic payload sizes, repetition, concurrency, and failure conditions. Record the environment and tradeoffs with performance claims.

## Review and test loop complexity

- Keep loop complexity as low as the feature requires while preserving correctness and readable code. Prefer a single pass over repeated scans when it can produce the same result.
- Review how work grows with input size, including helper calls inside loops. A scan of `n` items is O(n); scanning `m` items for each of them is O(n * m), or O(n^2) when both collections grow together. Separate sequential passes are still O(n), and fixed-size inner loops do not automatically make a loop quadratic.
- Avoid nested searches when a table keyed by the needed identifier can provide direct lookups. Account for the cost of building, storing, and maintaining that table; do not add an index without a useful reduction in work.
- Move calculations and allocations that do not depend on the current item outside the loop. Stop searching once the required result is found, and avoid sorting or rebuilding collections on every iteration.
- Consider execution frequency as well as input size. Repeated full scans in frame/tick hooks can be expensive even when each scan is linear. Use events or bounded batches when suitable, preserving required timing and behavior.
- Test changed loops manually in game with representative and larger realistic player, entity, or record counts. Compare execution time and frame/tick impact under equivalent conditions, and verify that results remain correct. Timing measurements support performance assessment; they do not prove asymptotic complexity.
- Report the input sizes, execution frequency, and observed results. If in-game testing is unavailable, provide the manual steps and mark the measurements outstanding. Do not create automated tests or benchmark harnesses for this review.
