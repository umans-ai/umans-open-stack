# Manifest reference

A cloud-agent config lives at `.devcontainer/devcontainer.json` (a standard devcontainer)
with a `customizations.umans` block. An agent that does not want a full devcontainer can
put the same block in `.umans/agent.jsonc`. Generic devcontainer tools ignore the umans
block; umans reads it for the front door, identity, runtime, and house-rules wiring.

This is a human-readable summary. The canonical schema ships with the product and may add
fields over time; treat `customizations.umans` as the source of truth when they differ.

## Top level (devcontainer)

Standard devcontainer keys do the install and configure halves:

- `image` or `build` sets the base. Agents start from the umans agent base image.
- `features` installs devcontainer features (languages, tools).
- `onCreateCommand`, `postStartCommand`, and the other lifecycle hooks clone, build, and
  start your app.

## customizations.umans

| Field | Type | Notes |
|---|---|---|
| `schemaVersion` | number | Manifest version. Use `1`. |
| `target` | `host` \| `container` | `container` runs your devcontainer on the box (the default for community agents). `host` runs directly on the box and is reserved for reviewed agents. |
| `attribution` | object | `kind` is `first-party`, `org`, or `community`. Add `from` (org) or `by` (person) and a `repo` URL. |
| `runtime` | object | `provider` (`umans` or `byok`) and a `model` ID. See [../reference/models.md](../reference/models.md). |
| `requires` | object | `git` (bool), `secrets` (list), repos to pre-clone. Consent-gated at launch. |
| `context` | object | `runtimes` lists the harnesses whose global rules file receives the box briefing (`opencode`, `claude-code`, `pi`). `paths` adds explicit targets. |
| `entries` | list | The exposed surfaces. Exactly one is `main`. See below. |

## entries

Each entry is one named, exposed surface.

| Field | Type | Notes |
|---|---|---|
| `id` | string | Stable slug, used in the subdomain and the switcher. |
| `label` | string | Display name. |
| `main` | bool | At most one entry sets this. It is what the launch button opens. |
| `kind` | `web` \| `terminal` | `web` serves on a port; `terminal` runs a command in a tty. |
| `port` | number | Web entries: the loopback port the surface serves on. |
| `command` | string | Terminal entries: the command to run. |
| `health` | string | Web: an HTTP readiness path. Terminal: process up. |
| `auth` | `umans` \| `basic` \| `passthrough` | Default `umans`: the umans session gates the entry and the app needs no login of its own. |
| `visibility` | `private` \| `team` \| `public` | Default `private`. `team` is your org; `public` needs no session. |

Routing: the bare subdomain opens the `main` entry; named entries are reachable at their own
subdomain. Every entry sits behind the umans session by default, so an app can read the
signed-in user without implementing its own login.

## run (container agents)

For `target: "container"` agents the `run` block is the bring-up recipe: one published
image, started hardened (read-only root, no-new-privileges, memory cap, loopback publish)
by the platform's compose renderer.

| Field | Type | Notes |
|---|---|---|
| `image` | string | The published image to run. Pin a tag, never `latest` — upgrades become deliberate, reviewed manifest bumps. |
| `env` | object | Container env. `${UMANS_AGENT_NAME}` interpolates to the box's agent name. |
| `volume` | string | One named volume as `name:/container/path` — survives restarts; a box recreate starts fresh. |
| `memory` | string | Compose memory cap (e.g. `512m`, `1536m`). Default `512m`; an agent running a CLI runtime inside wants real headroom — under-capping shows up as SIGKILL mid-run. |
| `runtimes` | list | Pinned, sha256-verified binaries installed by an init service before the app starts. Each: `name`, `url`, `sha256`, `archivePath` (the file inside the tarball), `dest` (absolute install path). Max 4. |
| `workspaceMount` | object | `{ "containerPath": "/workspace" }` — the box's project dir (the pre-cloned repo when exactly one exists, else `~/workspace`) bind-mounts there, the app runs as the box's agent uid so files stay agent-owned on the host, and a bring-up oneshot points the app's default project at the mount when the app supports an external root (folder-import/`baseDir` model). |
| `seedLocalStorage` | object | Zero-touch config for apps whose settings live in browser localStorage: `{ "key", "value", "path" }` — the box serves a tiny same-origin seed page (at `path`, default `/umans-seed`) that merges `value` into the stored JSON once (user edits win), then redirects into the app. `${UMANS_AGENT_NAME}` and `${UMANS_GATEWAY_KEY}` interpolate in string values. |

## notes

`notes` is a list of agent-specific house-rules lines (setup steps the agent needs before
it's useful) appended to the box briefing every runtime reads. Keep them short and
factual — they load in every session on the box.

## A full container agent

[manifests/open-design.devcontainer.jsonc](manifests/open-design.devcontainer.jsonc) is the
worked `run`-block declaration: one published image, five entries, a pinned runtime, the
workspace mount, and the localStorage seed — with the gotchas commented inline.
