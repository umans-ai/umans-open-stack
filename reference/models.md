# Models

Select a model by its ID in the `model` field of your request. The default is `umans-coder`.

`GET /v1/models/info` is the live source of truth for the current list, capabilities, and
pricing. The table below is a snapshot to orient you. The IDs are stable, but which base
model an alias points at, and the exact capabilities, can change as models evolve.

| Model ID | Best for | Images |
|---|---|---|
| `umans-coder` | Default. Routes to the current best coding model, chosen for you. | Yes |
| `umans-kimi-k2.7` | Your hardest coding tasks: deep, multi-step changes. Reasons first. | Yes |
| `umans-glm-5.2` | Latest GLM, largest context window in the lineup. | Via smart composition on `/v1/messages`; text-only on `/v1/chat/completions` |
| `umans-glm-5.1` | Previous GLM for text-first work. Being wound down. | Same as GLM 5.2 |
| `umans-flash` | Light, high-interactivity model. A complement to the coder, not a replacement. | Check `/v1/models/info` |

## Notes

- **Pick by task.** Use `umans-coder` as the default. Reach for `umans-kimi-k2.7` on the
  hardest changes, a GLM model when you want the largest context, and `umans-flash` for
  cheap, fast steps (it also counts for less against your
  [concurrency](../playbooks/concurrency.md) limit).
- **Images.** If your model or route will not take an image, see
  [vision handoff](../playbooks/vision-handoff.md).
- **Weights change.** Request weights and base models are tuned over time. The Usage tab and
  `/v1/models/info` always reflect what is actually applied.
