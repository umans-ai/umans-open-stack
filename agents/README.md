# Agents

A cloud agent on umans is a declarative config in a public repo that anyone can launch in
one click. The config is a standard `devcontainer.json` with a small umans block under
`customizations.umans`. The same file runs on your laptop and in the cloud, so you build
locally and ship the exact same thing.

This directory is where the community shares those configs and indexes them.

## What the config declares

The umans block adds five things on top of a standard devcontainer:

- **entries** are the surfaces the box exposes. Each entry is a web UI on a port or a
  terminal command. Exactly one is `main`, the surface that opens when someone launches the
  agent; the rest are reachable from a switcher and their own subdomains.
- **runtime** is how the agent reaches inference: the umans models, or bring your own key.
- **context** lists which harness rules files the platform writes its house rules into, so
  Claude Code, opencode, Pi, or a custom runtime all read the same briefing.
- **requires** is what the launcher must provide before boot: git access, secrets, repos to
  pre-clone. These are consent-gated.
- **attribution** is who made the agent.

See [reference.md](reference.md) for the field-by-field reference, and
[examples/opencode.devcontainer.jsonc](examples/opencode.devcontainer.jsonc) for a worked,
commented example.

## Author one

1. Start from [`../templates/agent-manifest.jsonc`](../templates/agent-manifest.jsonc) or
   the opencode example.
2. Put it at `.devcontainer/devcontainer.json` (or `.umans/agent.jsonc`) in your public repo.
3. Declare your entries, runtime, and anything the agent requires.
4. Run it locally to confirm it boots and the main entry serves.

## Share it

Add a row to [index.md](index.md) and open a pull request, or open an
[add-your-agent issue](../.github/ISSUE_TEMPLATE/add-your-agent.md). Include the repo URL,
what the agent does, and which surface is `main`.

## Where this is today

Cloud agents are in early access. opencode is the live, launchable harness today, and
[Open Design](https://github.com/nexu-io/open-design) is in an operator preview (it
launches for umans operators while we validate it — see
[examples/open-design.devcontainer.jsonc](examples/open-design.devcontainer.jsonc) for
the first `run`-block agent: one published image, four entries). If your agent is not
launchable yet, the index still lists it so people can find the repo and follow along.
