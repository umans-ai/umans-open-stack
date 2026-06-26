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
