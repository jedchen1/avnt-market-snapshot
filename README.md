# AVNT public market snapshots

Sanitized, read-only AVNTUSDT market data. Source: Binance through the existing Relay V2 and local Snapshot Publisher. The bridge runs every minute and fails closed when source validation fails.

## Read a fresh, consistent generation

GitHub raw branch URLs can cache responses for 300 seconds. Adding a query parameter did not reliably invalidate that cache during acceptance. Do not assume that a successful HTTP response is fresh.

1. Resolve main through the public GitHub API:
   https://api.github.com/repos/jedchen1/avnt-market-snapshot/commits/main
   Use a unique check query parameter per evaluation and validate the returned data.
2. Read both files using the SAME returned commit SHA:
   https://raw.githubusercontent.com/jedchen1/avnt-market-snapshot/<sha>/current.json
   https://raw.githubusercontent.com/jedchen1/avnt-market-snapshot/<sha>/manifest.json
3. Require matching snapshot_id, request_id, generated_at_utc, published_at_utc, and github_bridge_published_at_utc.
4. Require gate=PASS, symbol=AVNTUSDT, cache_used=false, and generated age between 0 and 180 seconds. Validate all required provider, structure, and integrity flags.
5. Reject stale data, mixed generations, missing fields, or failed requests. Never fall back to an old snapshot.

The API and raw reads are public and require no credentials. Public API rate limits apply. Commit-specific URLs preserve consistency but their data naturally ages; resolve the latest commit on each evaluation.

current.json and manifest.json are published together in one Git commit. No portfolio information or credentials belong in this repository. This repository does not itself establish ChatGPT Web or scheduled-runtime compatibility.
