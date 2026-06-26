# Concurrency

> Status: reference plus an open per-harness table. Add your harness below.

Your concurrency limit caps how many requests are being processed at the same instant, not
how many sessions you have open. A session holds a slot only while a model is actually
generating; while it runs tools, edits files, or waits for you, it holds none. So a few
slots cover more parallel sessions than the number suggests. What adds up fast is fan-out:
five subagents thinking at the same moment is five requests in flight.

## The numbers

| Plan | In-flight requests | Request window |
|---|---|---|
| Code Pro | 3 | 200 effective requests per rolling 5-hour window |
| Code Max | 4 | none (concurrency is the only limit) |
| Code Max plus packs | 4, plus 4 per pack (up to 3 packs) | none |
| Team seats | 4 per active seat, pooled across the org | none |

Light models count for less. Today `umans-flash` counts as half a request, so a workflow
that sends small steps (titles, summaries, quick checks) to a light model grows its
effective usage more slowly than its raw request count.

There is headroom above the stated number before hard enforcement (today, roughly double).
Treat it as a safety net for honest mistakes, not as extra capacity: it is slack that gets
tuned over time.

## What you see at the limit

- Past the headroom, requests return **HTTP 429**. Most coding agents back off and retry,
  so brushing the limit feels like a short pause.
- Each 429 also drops your account to lower priority for about 30 minutes. Requests keep
  working, just slower.
- More than ten concurrency 429s in a single day pauses the account for five hours. That
  almost always means something is looping or a stuck runner is hammering the API. You can
  wait it out or reactivate from the dashboard (which rotates your keys).

## Watch where you stand

The limit is account-scoped: every key under the same account shares the counter, which is
what triggers a 429. Check it live:

```bash
curl https://api.code.umans.ai/v1/usage \
  -H "Authorization: Bearer sk-your-umans-api-key"
```

`concurrent_sessions` is your in-flight count, `limit` is the soft cap, `hard_cap` is the
headroom ceiling, and `priority.low` tells you if you are currently deprioritized.
`GET /v1/usage/history` gives bucketed peaks over time. The CLI shortcut is `umans usage`.

## Techniques

- **Cap fan-out.** The single biggest lever. Limit how many subagents or parallel tool
  calls run at once. If your harness lets you set a parallelism ceiling, set it at or below
  your plan's in-flight number.
- **Sequence what does not need to be parallel.** Slots are only held during generation, so
  serial steps rarely collide.
- **Send light steps to a light model.** Route titles, summaries, and quick checks to
  `umans-flash` so they count for less and leave headroom for the real work.
- **Let the agent retry.** Built-in 429 backoff is usually enough; do not stack a second
  retry loop on top that amplifies the burst.
- **Watch peaks, not averages.** A workflow that averages 2 in flight but spikes to 8 is
  what trips the limit. Use the `/v1/usage/history` peaks.

## Per-harness configs

How to cap parallelism in a specific harness. Add a row for yours.

| Harness | How to cap parallelism | Notes | Maturity |
|---|---|---|---|
| Claude Code | _contributions welcome_ | limiting parallel subagents and tool calls | Draft |
| opencode | _contributions welcome_ | limiting parallel sessions and fan-out | Draft |

To add a row, see [../CONTRIBUTING.md](../CONTRIBUTING.md).
