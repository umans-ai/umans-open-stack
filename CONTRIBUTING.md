# Contributing

Thanks for helping build the Umans Open Stack.

You do not need to write code to contribute. The most valuable thing you can share is a
config that worked, with enough detail that someone else can reproduce it.

## Three ways to contribute

1. **Share a working config (a playbook).** You found a setting that keeps your harness
   within concurrency limits, a way to wire vision handoff, a caching setup that cut your
   token bill, or a workflow worth repeating. Copy [`templates/playbook.md`](templates/playbook.md),
   fill it in, and open a pull request under `playbooks/`, or open a
   [share-a-config issue](.github/ISSUE_TEMPLATE/share-a-config.md).
2. **Add an agent to the index.** You packaged an agent as a declarative manifest in a
   public repo. Add a row to [`agents/index.md`](agents/index.md) and open a pull request,
   or open an [add-your-agent issue](.github/ISSUE_TEMPLATE/add-your-agent.md). See
   [`agents/README.md`](agents/README.md) for what a manifest is.
3. **Report what worked or broke.** Tried a harness or model against umans and have
   results? Use the [test report](.github/ISSUE_TEMPLATE/test-report.md) format.

## Maturity labels

Mark each contribution so readers know how much to trust it:

- **Draft**: shared, not yet confirmed by anyone else.
- **Tested**: you got it working for a real task.
- **Verified**: reproduced by more than one person.

A config can graduate. If you reproduce someone else's Draft, say so and bump it to Tested.

## Discord vs GitHub

Discord is for discussion, raw tests, screenshots, errors, and ideas. GitHub is for clean,
reproducible configs and long-term documentation.

If a discussion already exists on Discord, link to it instead of rewriting everything.

[Join the Umans Discord](https://discord.gg/Q5hdNrk7Rw)

## What makes a good contribution

- Reproducible: someone with the same harness can follow it and get the same result.
- Portable when possible: standard tools, standard formats, no fragile hacks.
- Specific: name the harness, the model ID, the exact setting you changed.
- Honest about limits: say what you did not test.

## What we avoid

- Configs that only work through a fragile, undocumented hack.
- Promotion unrelated to the community.
- Duplicated threads for the same project.
