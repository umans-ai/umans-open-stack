# API surface

Point any harness at the umans API. It speaks both the Anthropic-compatible and the
OpenAI-compatible shape from one base URL, so most tools work with a base URL and a key.

## Endpoint

| Setting | Value |
|---|---|
| Base URL | `https://api.code.umans.ai` |
| Anthropic-compatible | `POST /v1/messages` |
| OpenAI-compatible | `POST /v1/chat/completions` |
| Default model | `umans-coder` |

## Get a key

1. Log in at [app.umans.ai/billing](https://app.umans.ai/billing).
2. Go to Dashboard, then API Keys.
3. Generate a key. It is shown once, so copy it immediately. Keys start with `sk-`.

Keys are account-scoped: usage and limits add up across every key and machine on your
account. See [../playbooks/concurrency.md](../playbooks/concurrency.md).

## Anthropic-compatible

```bash
curl -N -X POST https://api.code.umans.ai/v1/messages \
  -H "Content-Type: application/json" \
  -H "x-api-key: sk-your-umans-api-key" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "umans-coder",
    "messages": [{"role": "user", "content": "Hello!"}],
    "max_tokens": 4096,
    "stream": true
  }'
```

`max_tokens` is required by the Anthropic Messages API. An `Authorization: Bearer sk-...`
header works here too, in place of `x-api-key`.

## OpenAI-compatible

```bash
curl -N -X POST https://api.code.umans.ai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-umans-api-key" \
  -d '{
    "model": "umans-coder",
    "messages": [{"role": "user", "content": "Hello!"}],
    "stream": true
  }'
```

## Environment variables

Most harnesses read one of these pairs. Use the one that matches the shape your tool speaks:

```bash
# Anthropic-style tools
export ANTHROPIC_BASE_URL=https://api.code.umans.ai
export ANTHROPIC_AUTH_TOKEN=sk-your-umans-api-key

# OpenAI-style tools
export OPENAI_BASE_URL=https://api.code.umans.ai/v1
export OPENAI_API_KEY=sk-your-umans-api-key
```

See [harnesses.md](harnesses.md) for tool-by-tool setup.

## Other endpoints

- `GET /v1/models/info`: the live model list with capabilities and pricing. Source of truth;
  see [models.md](models.md).
- `GET /v1/usage`: your live limits and counters. See
  [../playbooks/concurrency.md](../playbooks/concurrency.md).
- `GET /v1/usage/history`: bucketed history, including cached vs uncached tokens. See
  [../playbooks/caching.md](../playbooks/caching.md).
