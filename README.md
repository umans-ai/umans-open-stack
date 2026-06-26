# Umans Open Stack

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/Q5hdNrk7Rw)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A community knowledge base for getting the most out of umans across coding harnesses.

The goal is simple: collect the configs and patterns that actually work, so the next
person does not have to rediscover them. How to stay within concurrency limits in your
harness. How to hand an image to a vision-capable model and feed the result back to a
text-only one. How to get more cache hits. How to package an agent so anyone can launch
it in one click.

Everything here stays portable. The harnesses are standard tools and the formats are
standard formats (devcontainers, OpenAI-compatible and Anthropic-compatible APIs). You
should be able to use these configs with umans, self-host the tool, or point it at
another provider when that makes sense.

## What you will find

- **[Agents](agents/)**: share a cloud-agent config (a declarative manifest that makes an
  agent launchable in one click) and find others in the [index](agents/index.md).
- **[Playbooks](playbooks/)**: working configs the community has tried.
  [Concurrency](playbooks/concurrency.md), [images](playbooks/images.md),
  [vision handoff](playbooks/vision-handoff.md), [caching](playbooks/caching.md), and
  [workflows](playbooks/workflows.md).
- **[Reference](reference/)**: the facts a harness needs. The
  [API surface](reference/api.md), the [models](reference/models.md), and how to
  [connect each harness](reference/harnesses.md).

## Quick start

Point any harness at the umans API. The endpoint speaks both the OpenAI-compatible and the
Anthropic-compatible shape, so most tools work with two environment variables. See
[reference/api.md](reference/api.md) for the base URL, how to create a key, and the model
IDs you can select.

Then pick a playbook for the thing you are trying to do, and tune the per-harness section
for your tool.

## Maturity

Configs and agents here carry a label so you know how much to trust them:

| Label | Meaning |
|---|---|
| Draft | Shared, not yet confirmed by anyone else |
| Tested | One person got it working for a real task |
| Verified | Reproduced by more than one person |

## Community

Discussion, raw tests, screenshots, and ideas happen on Discord. Clean, reproducible
configs live here on GitHub.

[Join the Umans Discord](https://discord.gg/Q5hdNrk7Rw)

## Contribute

The most useful thing you can do is share a config that worked: which harness, which
model, what you changed, and what the result was. You do not need to write code.

See [CONTRIBUTING.md](CONTRIBUTING.md).
