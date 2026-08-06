# Open Design on umans

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

## Gotcha 2 — no local CLIs in the container

The onboarding "CLI local" tab scans PATH **inside the container**. The umans
box's baked CLIs (claude, opencode, pi) live on the *host*, and the
`ghcr.io/nexu-io/od` image ships none. So "no agent detected" is expected,
and mounting host binaries in is deliberately avoided (it would hand the
vendor image the host's toolchain and the box's credentials for no robustness
win). Inference goes through the **API providers** path instead — which is
also the one that keeps traffic on the gateway's audit + rate-limit rails.

## Gotcha 3 — the chat BYOK config is browser-side

The chat provider travels in the request body from the browser's
`localStorage` (`open-design:config`). The daemon persists onboarding state
and CLI env to `/app/.od/app-config.json`, but never the chat key. There is
no `OD_BYOK_*` env hook today (we checked all of them). So a fresh studio
opens unconfigured until one of the two zero-touch paths below lands.

## Wiring umans inference manually (today, 30 seconds, once)

1. Open the studio → Settings → *Fournisseurs d'API* (API providers) →
   *Fournisseur personnalisé* (custom provider).
2. Base URL: `https://api.code.umans.ai` — protocol Anthropic-compatible or
   OpenAI-compatible, both are first-class on the gateway.
3. API key: the box's gateway key. On the box (Terminal entry):
   `echo $UMANS_API_KEY` — or read `~/.umans/config.json`.
4. Model: `umans-coder` (see [../reference/models.md](../reference/models.md)).
5. Save. The choice persists in the browser; the box's gateway key is
   per-instance, billed to your plan/wallet, and revoked when the box
   terminates.

## Zero-touch path A — the same-origin seed page (umans platform)

The umans box serves a tiny host-side page at `/umans-seed` on the studio's
own origin (same nginx block, same umans session gate). On first visit it
writes the provider config (base URL, box key, `umans-coder`) into
`localStorage['open-design:config']` and redirects into the studio, which
then opens pre-wired. The key exposure is identical to the manual step (it
lands in the same localStorage entry), and the page is only reachable by the
box owner behind the umans session. No vendor files are touched.

## Zero-touch path B — `OD_BYOK_*` upstream (the durable fix)

The contribution to `nexu-io/open-design`: the daemon reads
`OD_BYOK_PROTOCOL` / `OD_BYOK_BASE_URL` / `OD_BYOK_API_KEY` /
`OD_BYOK_MODEL` and uses them as the default chat provider when the browser
hasn't configured one, plus a `GET /api/byok-defaults` so the web UI can show
"managed by host environment" (the key never leaves the daemon — the web
learns only a tail). Server deployments (umans, Railway, any Docker host)
then ship fully pre-wired studios with zero browser state. Once released,
the umans manifest just sets those env keys and path A retires.

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
