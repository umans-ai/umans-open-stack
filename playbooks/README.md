# Playbooks

Working configs the community has tried, one topic per file. Each playbook explains the
thing once, then carries a per-harness table you can read for your tool or add a row to.

- [Concurrency](concurrency.md): stay within your in-flight request limit.
- [Images](images.md): stay under the 10-image limit by swapping old images for a description.
- [Vision handoff](vision-handoff.md): use a text-only model on an image by routing the
  image to a vision-capable model first.
- [Caching](caching.md): get more cache hits and a lower token bill.
- [Workflows](workflows.md): repeatable multi-step recipes.

## Maturity

Each entry carries a label: Draft (shared, unconfirmed), Tested (worked for one person on a
real task), Verified (reproduced by more than one person). See
[../CONTRIBUTING.md](../CONTRIBUTING.md).

## Add a playbook

Copy [`../templates/playbook.md`](../templates/playbook.md), fill it in, and open a pull
request, or open a [share-a-config issue](../.github/ISSUE_TEMPLATE/share-a-config.md). If
your config is for an existing topic, add a row to that file's per-harness table instead of
a new file.
