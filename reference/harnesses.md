# Harnesses

How to point specific tools at the umans API. The setup is almost always a base URL and a
key (see [api.md](api.md)); the differences are which environment variables a tool reads and
which route it speaks.

Once connected, see the playbooks for tuning: [concurrency](../playbooks/concurrency.md),
[vision handoff](../playbooks/vision-handoff.md), [caching](../playbooks/caching.md).

| Harness | Route | How to connect | Maturity |
|---|---|---|---|
| Claude Code | Anthropic | `ANTHROPIC_BASE_URL=https://api.code.umans.ai`, `ANTHROPIC_AUTH_TOKEN=sk-...` | Tested |
| opencode | Anthropic or OpenAI | Add umans as a provider with the base URL and your key | Tested |

## Add a harness

Got another tool working against umans (an OpenAI-compatible or Anthropic-compatible editor,
CLI, or agent)? Add a row with the exact environment variables or config you used, and a
maturity label. See [../CONTRIBUTING.md](../CONTRIBUTING.md).

Most OpenAI-compatible tools work with:

```bash
export OPENAI_BASE_URL=https://api.code.umans.ai/v1
export OPENAI_API_KEY=sk-your-umans-api-key
```

and selecting `umans-coder` as the model.
