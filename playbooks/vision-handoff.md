# Vision handoff

> Status: reference plus an open per-harness table. Add your harness below.

Some umans models read images, some are text-only. When you want to use a text-only setup
on a task that involves an image (a screenshot, a diagram, a design), you route the image
step to a vision-capable model, get a text description back, and feed that text to your
text-only model. The rest of the work continues on the model you wanted. The same step
also keeps long conversations under the [10-image limit](images.md), by swapping an aging
image for its description.

## Which models read images

The capability depends on the model and the route you call:

- On the **Anthropic-compatible route** (`/v1/messages`), the GLM models accept images via
  smart composition, so you often do not need to do anything yourself.
- On the **OpenAI-compatible route** (`/v1/chat/completions`), the GLM models are
  **text-only**. This is where client-side handoff matters.
- The native vision models (`umans-coder`, `umans-kimi-k2.7`) accept images directly.

Check the live capability surface with `GET /v1/models/info` rather than hardcoding it. See
[../reference/models.md](../reference/models.md).

## The pattern

When your model or route will not take the image:

1. Send the image to a vision-capable umans model (`umans-coder` or `umans-kimi-k2.7`) with
   a tight prompt: describe exactly what the downstream step needs (layout, text content,
   colors, the error message, whatever matters).
2. Take the text it returns.
3. Continue your workflow on your chosen text model, passing that description in place of
   the image.

Keep the description focused. A vision pass that dumps everything costs more and buries the
detail the next step actually needs. Ask for what the task needs.

## When you do not need it

- You are already on a native vision model.
- You are on the Anthropic route with a GLM model (smart composition handles it).

Reach for manual handoff when you are on the OpenAI route with a text-only model, when you
want a specific model to do the describing, or when you want to cache the description and
reuse it across several downstream calls.

## Per-harness configs

How to wire the handoff in a specific harness. Add a row for yours.

| Harness | How to wire the handoff | Notes | Maturity |
|---|---|---|---|
| Claude Code | _contributions welcome_ | for example a hook or tool that describes an image then continues | Draft |
| opencode | _contributions welcome_ | for example a command that does the describe-then-continue step | Draft |

To add a row, see [../CONTRIBUTING.md](../CONTRIBUTING.md).
