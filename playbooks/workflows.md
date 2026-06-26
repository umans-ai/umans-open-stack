# Workflows

> Status: seed. Add yours.

A workflow is a repeatable recipe: a way of setting up an agent and driving it through a
sequence of steps that you have found works, and that someone else can follow. Where a
single config tunes one thing, a workflow strings several together for a whole task.

A good workflow write-up usually has:

- **The goal.** What task this gets done, and when to reach for it.
- **The setup.** The harness, the model or models, and any agent config or manifest it
  assumes. Link an [agent manifest](../agents/) if it needs a specific box.
- **The steps.** The ordered sequence, including where work fans out and where it comes back
  together, and which model serves each step (a light model for cheap steps, a stronger one
  for the hard step).
- **The guardrails.** How it stays within [concurrency](concurrency.md) limits and how it
  uses [caching](caching.md).
- **The result.** What you got, and what you did not test.

## Patterns worth sharing

- Routing cheap steps (titles, summaries, triage) to `umans-flash` and the hard step to a
  coding model.
- Fan-out with a parallelism ceiling that respects your in-flight limit.
- A vision pass that describes images once, then a text-only model that does the rest.
- A review pass: one model proposes, another checks.

## Add a workflow

Copy [`../templates/playbook.md`](../templates/playbook.md) (it fits workflows too), write
it up, and open a pull request, or open a
[share-a-config issue](../.github/ISSUE_TEMPLATE/share-a-config.md).
