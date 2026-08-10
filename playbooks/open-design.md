# Open Design on umans

> **Status (2026-08-10): operator preview.** Launchable by umans operators
> from the cloud-agents catalogue; GA after the preview validates. Working:
> the studio pre-wired to umans models (seed page), opencode-cli in the
> container (pinned + sha256-verified), the pinned self-hostable image
> (0.16.1). Known gaps: the opencode sibling entry on no-repo boxes has an
> empty project picker; one wrapper regression is under review on the
> platform side. Upstream: PR #6625 (BYOK env defaults, approved) and issue
> #6640 (self-hosted gate bypass) will let this integration drop its
> temporary pieces.

[Open Design](https://github.com/nexu-io/open-design) — the open-source design
studio (prototypes, slides, images, video) — running as a umans cloud agent,
wired to umans inference. This page is the integration contract: what runs
where, the three gotchas that cost us a day, and the two ways the model
wiring becomes zero-touch.

## The architecture in one paragraph

The studio runs as a Docker container on the box (`ghcr.io/nexu-io/od`),
serving the web app and its daemon on port 7456. The umans agent-proxy is the
auth perimeter: it validates the umans session before any traffic reaches the
box, so the studio's own token auth is disabled (`OD_DISABLE_API_AUTH=1` —
upstream blesses exactly this deployment shape). Design *generation* is a
chat loop the daemon proxies to a **BYOK provider that the browser sends in
each request** (base URL + API key + model, kept in the browser's
localStorage). The umans gateway speaks both the Anthropic-compatible and the
OpenAI-compatible shape, so one box-scoped gateway key covers every surface.

## Gotcha 1 — `OD_ALLOWED_ORIGINS`, not `OPEN_DESIGN_ALLOWED_ORIGINS`

The daemon's origin guard reads **`OD_ALLOWED_ORIGINS`**
(`apps/daemon/src/origin-validation.ts`). The `OPEN_DESIGN_*` spelling only
exists in upstream's `deploy/.env.example` as the *host-side* name their
compose file substitutes from. Setting the long name on the container does
nothing — every `/api` call fails with "Cross-origin requests are not
allowed". Set the daemon var to the box's bare origin:

```
OD_ALLOWED_ORIGINS=https://<agent-name>.agents.umans.ai
```

## Gotcha 2 — the vendor image ships no agent runtime at all

Bigger than "no local CLIs detected": the `ghcr.io/nexu-io/od` image contains
**no agent binaries**, and every studio design generation is a run that
spawns one (`AGENT_UNAVAILABLE` without it — reproduced locally on the
official image). The fix that makes the studio actually work in Docker is
installing **opencode-cli** (the binary behind the studio's BYOK OpenCode
runtime) into the container, pinned and sha256-verified, mounted where the
daemon's agent scan probes (`/home/open-design/.local/bin`), with opencode's
XDG dirs pointed at the writable tmpfs (`XDG_*_HOME=/tmp/xdg/*`). The umans
manifest does this for you via the `run.runtimes` block — see the
[example manifest](../agents/examples/open-design.devcontainer.jsonc).
Proven end-to-end: a `byok-opencode` run against the umans gateway produced a
real artifact. Mounting the *host's* baked CLIs in is deliberately avoided —
it would hand the vendor image the host's toolchain and credentials for no
robustness win.

## Gotcha 3 — the chat BYOK config is browser-side

The chat provider travels in the request body from the browser's
`localStorage` (`open-design:config`). The daemon persists onboarding state
and CLI env to `/app/.od/app-config.json`, but never the chat key. There is
no `OD_BYOK_*` env hook today (we checked all of them). So a fresh studio
opens unconfigured until one of the two zero-touch paths below lands.

## Gotcha 4 — `:latest` is a moving, cloud-gated target

The dev channel that `ghcr.io/nexu-io/od:latest` tracks changed onboarding
mid-pilot: current builds open with a mandatory "Sign in to Open Design"
cloud-account step before Local/BYOK are even offered — wrong for a
self-hosted box (and it arrived between two recreates of the same agent,
which is how we noticed). The umans manifest pins
`ghcr.io/nexu-io/od:0.16.1`, the last release whose welcome offers Local
agent / BYOK without a forced account, and upgrades become deliberate,
reviewed manifest bumps. (Raised upstream — see the issue linked below.)
When a first-class self-hosted flag lands, unpin and drop the seed page.

## Wiring umans inference manually (today, 30 seconds, once)

1. Open the studio → Settings → API providers → custom provider.
2. Provider type **OpenAI-compatible**, base URL
   **`https://api.code.umans.ai/v1`** — the `/v1` matters: opencode appends
   `/chat/completions` to whatever you give it.
3. API key: the box's gateway key. On the box (Terminal entry):
   `echo $UMANS_API_KEY` — or read `~/.umans/config.json`.
4. Model: `umans-coder` (see [../reference/models.md](../reference/models.md)).
5. Save. The choice persists in the browser; the box's gateway key is
   per-instance, billed to your plan/wallet, and revoked when the box
   terminates. The studio's BYOK OpenCode runtime then generates with umans
   models (the `opencode-cli` from `run.runtimes` does the work).

## Zero-touch path A — the same-origin seed page (shipped on umans)

The umans box serves a tiny host-side page at `/umans-seed` on the studio's
own origin (same nginx block, same umans session gate). On first visit it
writes the provider config (the `/v1` base URL, the box key, `umans-coder`,
`agentId: byok-opencode`, `onboardingCompleted: true`) into
`localStorage['open-design:config']` — only when no key is already
configured — and redirects into the studio, which opens pre-wired. The Open
button lands there (`openPath` on the manifest's main entry), so the first
click seeds invisibly and later clicks pass straight through. The key
exposure is identical to the manual step (it lands in the same localStorage
entry), the page is only reachable by the box owner behind the umans
session, and no vendor files are touched. Verified end-to-end in a real
browser against the real studio: fresh load gates at onboarding; after the
seed, home opens with umans models ready.

The manifest knobs, reusable by any app with browser-local config:
`run.seedLocalStorage` (key + value template with `${UMANS_AGENT_NAME}` /
`${UMANS_GATEWAY_KEY}` interpolation) and the entry's `openPath`.

## Zero-touch path B — `OD_BYOK_*` upstream (the durable fix)

The contribution to `nexu-io/open-design`, in two parts: (1) the daemon reads
`OD_BYOK_PROTOCOL` / `OD_BYOK_BASE_URL` / `OD_BYOK_API_KEY` /
`OD_BYOK_MODEL` and uses them as the default chat provider when the browser
hasn't configured one, plus a `GET /api/byok-defaults` so the web UI can show
"managed by host environment" (the key never leaves the daemon — the web
learns only a tail); (2) their Dockerfile ships `opencode-cli` so the image
can actually generate designs out of the box (today it cannot). Server
deployments (umans, Railway, any Docker host) then ship fully pre-wired
studios with zero browser state. Once released, the umans manifest just sets
those env keys and path A retires.

## Security posture (unchanged throughout)

- The umans session gates every route (proxy forward-auth + mTLS to the
  box; the studio's own auth disabled per upstream's documented escape
  hatch).
- The gateway key is per-box, rate-limited, billed to the owner, revoked on
  terminate. Seeding writes it from cloud-init on the host side; nothing
  executes inside the vendor container beyond its own image.
- The studio container runs read-only with a named volume for state; a box
  Recreate wipes it (fresh start), restarts keep it.

## Files

- Cloud-agent manifest:
  [../agents/examples/open-design.devcontainer.jsonc](../agents/examples/open-design.devcontainer.jsonc)
- Agent index row: [../agents/index.md](../agents/index.md)
