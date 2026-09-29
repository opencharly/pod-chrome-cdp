# AGENTS.md — pod-chrome-cdp

Standalone candy repo for the `chrome-cdp` candy — a Chrome DevTools Protocol
automation suite (CDP on `9222` via `cdp-proxy`) layered on
`/charly-selkies:chrome`. The candy lives in `charly.yml` at the repo root, plus
the helper scripts it installs (`chrome-wrapper`, `chrome-restart`,
`browser-open`, `cdp-proxy`) and the staged `chrome-windows/` extension.

Canonical files:

- `charly.yml` — the `chrome-cdp:` candy entity (description, `require`, `candy`,
  `path_append`, env, port, service, plan).
- `chrome-wrapper`, `chrome-restart`, `browser-open`, `cdp-proxy` — the scripts
  copied into `~/.local/bin`.
- `chrome-windows/` — the browser extension staged under `~/.local/share`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-selkies:chrome` — the parent Chrome layer this candy builds on; the
  closest owning procedure for the browser surface. Load before editing,
  building, deploying, or troubleshooting this candy.
- `/charly-check:cdp` — the declarative `cdp:` check verb served by
  `plugin-cdp`; the plan authors `cdp:`-adjacent `http:`/`addr:` probes.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the sibling
`/charly-selkies:chrome` covers the surface. The gap is routed to the named
skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is a composing box's `check` bed; the candy's own
  `check:` steps assert the wrapper/CDP flags, the staged extension, the running
  `cdp-proxy`, and that `/json/version` returns `200` with a
  `webSocketDebuggerUrl` through the proxy.

## Modify this repo

- Edit the `chrome-cdp:` candy entity in `charly.yml` and the script files it
  copies. Keep the `--remote-debugging-port=9223` and Windows-UA strings the
  plan's `check:` steps lock in.
- The published port `9222` and the internal `9223` are a two-part contract; a
  change must keep `cdp-proxy`'s `LISTEN_PORT` / `TARGET_PORT` and the plan's
  checks in step.
- Container-only: do not add a `target: local` install path.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
