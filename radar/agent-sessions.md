# Agent sessions radar

We are looking for open tools and patterns around remote agent sessions.

## What we mean

A session is an environment where an agent can work on a repo, run commands, stream logs, modify code, and let the user resume or take over later.

## What we want to explore

- Start locally, continue in the cloud
- Remote execution environments
- Repo sandboxes
- Agent harnesses
- Session persistence
- Logs, diffs, commands, and approvals
- Self-hostable architecture

## Vision: Umans Sessions

A possible open spec around remote agent sessions:

```txt
Local harness
  -> starts or connects to remote session
  -> gives agent a workspace
  -> streams logs, diffs, tests, commands
  -> lets user take over
  -> can push branch / open PR
```

Not a product, just a spec plus recipes:

- start session
- attach local CLI
- expose repo
- run commands
- persist branch
- stream logs
- resume later
- self-host recipe

## Open questions

- What should run locally?
- What should run remotely?
- What should Umans host?
- What should stay self-hosted by users?
