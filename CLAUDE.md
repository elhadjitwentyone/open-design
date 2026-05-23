@AGENTS.md

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

The primary agent guide is `AGENTS.md` (auto-loaded above). Key orientation below.

## Lifecycle entry points

```bash
pnpm tools-dev run web              # foreground dev server (daemon + web)
pnpm tools-dev start web            # background
pnpm tools-dev status / logs / stop / check
pnpm tools-dev inspect desktop screenshot --path /tmp/od.png

pnpm guard                          # repo-wide lint + style policy
pnpm typecheck                      # full workspace typecheck

pnpm --filter @open-design/web test
pnpm --filter @open-design/daemon test
pnpm --filter @open-design/daemon build
```

Never use `pnpm dev`, `pnpm start`, `pnpm build`, or `pnpm test` at the repo root.

## Architecture in one paragraph

The daemon (`apps/daemon/`) is an Express + SQLite server that owns all `/api/*` routes, spawns the user's installed agent CLI (`claude`, `codex`, `gemini`, etc.) as a child process with CWD set to the project folder under `.od/projects/<id>/`, and streams SSE back to the Next.js 16 SPA (`apps/web/`). The SPA renders design artifacts in a sandboxed `srcdoc` iframe. The optional Electron shell (`apps/desktop/`) discovers the web URL through sidecar IPC from `tools/dev/`. Shared API contracts live in `packages/contracts/`; web must never import daemon's private `src/`.

## Three rules Claude Code must not forget

1. Every capability ships in both the web UI **and** the `od` CLI — UI-only is a regression.
2. New capabilities are a 3-file closure: daemon route + `packages/contracts` type + `od` subcommand, all in one PR.
3. Git commits must not include `Co-authored-by` trailers.
