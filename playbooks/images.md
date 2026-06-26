# Images and the 10-image limit

> Status: reference plus an open per-harness table. Add your harness below.

Each request to the umans API can carry up to 10 images. Because a request includes the
conversation so far, a long session that keeps pasting screenshots adds them up and
eventually crosses the cap, and the request is rejected (HTTP 400) rather than silently
dropping anything.

The fix is to keep only the images you still need as images, and replace the older ones
with a short text description of what they showed. You keep the context, you drop back
under the cap, and the text is cheaper and cacheable on top.

## The pattern

1. When a conversation approaches 10 images, take the oldest ones that are no longer the
   active subject.
2. Describe each in text (see [vision handoff](vision-handoff.md)): capture what later
   steps still rely on, not every pixel.
3. Replace the image in the history with that description, and keep recent images as images.

Reuse the description rather than regenerating it (see [caching](caching.md)). The same
describe-an-image step that powers vision handoff gives you the text to swap in here.

## Per-harness configs

How a specific harness manages its image history. Add a row for yours.

| Harness | How it handles the image cap | What to set | Maturity |
|---|---|---|---|
| Claude Code | _contributions welcome_ | does it prune or describe old images? | Draft |
| opencode | _contributions welcome_ | image-history handling | Draft |

To add a row, see [../CONTRIBUTING.md](../CONTRIBUTING.md).
