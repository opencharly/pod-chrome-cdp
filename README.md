# pod-chrome-cdp

The `chrome-cdp` candy of the OpenCharly candy library, as a standalone repo (the
candy de-submodule cutover, kind-prefixed naming). It is a Chrome DevTools
Protocol automation suite layered on `/charly-selkies:chrome`.

## What it provides

CDP on published port `9222` via the `cdp-proxy` service, plus the helper scripts
that make a containerized Chrome drivable.

| Property | Value |
|---|---|
| Port | `9222` (published; forwards to Chrome's internal `9223`) |
| Service | `cdp-proxy` (`restart: always`) |
| Requires | `layer-chrome`, `layer-supervisord`, `plugin-cdp` |
| Composes | `pod-chrome-devtools-mcp` |
| Env | `BROWSER=browser-open`; `env_provide` `BROWSER_CDP_URL` |
| Scripts | `chrome-wrapper`, `chrome-restart`, `browser-open`, `cdp-proxy` in `~/.local/bin` |
| Extension | `chrome-windows` staged under `~/.local/share` |

`chrome-wrapper` launches Chrome with `--remote-debugging-port=9223` and spoofs a
Windows user-agent (`Windows NT 10.0`); `cdp-proxy` forwards published `9222` to
the internal `9223` with Host-header rewriting so the `cdp:` check verb is
reachable host-side. Container-only — every feature assumes a headless context.

## How to use it

Compose the candy into a headless desktop box:

```yaml
my-browser-box:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-chrome-cdp:<tag>'
```

Then `charly box build` + `charly start`, and drive Chrome via the `cdp:` check
verb or a CDP client against `http://127.0.0.1:9222`.

## Layout

- `charly.yml` — the `chrome-cdp` candy entity (description, `require`, `candy`,
  `path_append`, env, port, service, plan).
- `chrome-wrapper`, `chrome-restart`, `browser-open`, `cdp-proxy` — the installed
  scripts.
- `chrome-windows/` — the staged browser extension (`manifest.json`,
  `rules.json`, `content.js`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:chrome` — the parent Chrome layer this candy
  builds on; the closest owning procedure for the browser surface.
- `/charly-check:cdp` — the declarative `cdp:` check verb served by `plugin-cdp`
  (a related check-verb skill, not the owning skill).
- `/charly-selkies:chrome-devtools-mcp` — the MCP server composed by this candy.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
