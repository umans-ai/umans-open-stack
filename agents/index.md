# Agent index

Cloud agents the community has packaged as a declarative manifest. Each links to a public
repo you can read, fork, and launch.

Cloud agents are in early access. A row can be listed here before it is launchable, so
people can find the repo and follow along. Open Design is generally available —
launchable by everyone (see its
[example manifest](manifests/open-design.devcontainer.jsonc)).

| Agent | By | Repo | Main surface | Maturity |
|---|---|---|---|---|
| opencode UI | umans (first-party) | first-party | Browser IDE (with Pi, Claude Code, and terminal siblings) | Verified |
| Open Design | [Nexu](https://github.com/nexu-io) | <https://github.com/nexu-io/open-design> | Design studio web UI (with opencode, Pi, and terminal siblings) | Tested |

## Add yours

1. Package your agent as a manifest in a public repo (see [README.md](README.md)).
2. Copy the table row above and fill in your agent: name, who made it, the repo URL, the
   `main` surface, and a maturity label.
3. Open a pull request, or open an
   [add-your-agent issue](../.github/ISSUE_TEMPLATE/add-your-agent.md) and we will add it.

Row format:

```txt
| <agent name> | <your name or org> | <https://github.com/...> | <what main opens> | Draft |
```

Keep it to agents whose repo is public and whose manifest someone else can actually read.
