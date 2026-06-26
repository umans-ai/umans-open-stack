# Caching

> Status: reference plus an open per-harness table. Add your harness below.

Input tokens you have sent before can be served from cache instead of reprocessed. Cached
reads are billed at a lower rate than fresh input, so the more of your prompt that stays
stable across calls, the less each call costs. Output tokens are billed separately and are
not cached.

This matters most for coding agents, which resend a large, mostly unchanging context
(system prompt, house rules, file contents) on every turn.

## What to do

- **Keep the prefix stable.** Put the parts that do not change (system prompt, instructions,
  reference files) at the front, in the same order every time. A single early edit
  invalidates everything cached after it.
- **Mark cache breakpoints on the Anthropic route.** `/v1/messages` accepts `cache_control`
  on content blocks. Put a breakpoint after large, stable chunks (the system prompt, a
  pinned file) so they are cached as a unit.
- **Do not bust the cache by accident.** Timestamps, per-call IDs, or reordered context near
  the front defeat caching. Keep volatile content late in the prompt.
- **Reuse derived text.** If you described an image once (see
  [vision-handoff.md](vision-handoff.md)) or summarized a file, pass the same text rather
  than regenerating it.

## See the effect

`GET /v1/usage/history` breaks tokens into cached and uncached. Watch the cached share rise
as you stabilize your prefix:

```bash
curl "https://api.code.umans.ai/v1/usage/history?granularity=hour" \
  -H "Authorization: Bearer sk-your-umans-api-key"
```

## Per-harness configs

How a specific harness handles caching, and any knob worth setting. Add a row for yours.

| Harness | Caching behavior | What to set | Maturity |
|---|---|---|---|
| Claude Code | _contributions welcome_ | does it set cache breakpoints automatically? | Draft |
| opencode | _contributions welcome_ | prompt-stability tips for this harness | Draft |

To add a row, see [../CONTRIBUTING.md](../CONTRIBUTING.md).
