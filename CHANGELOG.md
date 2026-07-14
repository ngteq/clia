# Changelog

## v0.3.5 — 2026-07-14

Initial public release of **clia** ([ngteq/clia](https://github.com/ngteq/clia)).

### Added

- **`cliad` binary** — mesh hub (main) and interactive secondary client
- **Built-in plugins** — Cursor agent relay, OpenRouter, OpenAI-compatible API, rsync module search
- **Mesh protocol** — TCP port 4224; multiple secondaries connect to one main host
- **Chat commands** — `/ai` provider list and selection, `/ct` chat, project switching (`/index`, `/projects`, `/project`, `/main`)
- **Menu registry** — YAML-driven command help (`share/commands.yaml`, `share/areas.yaml`)
- **rsync companion** — optional rsyncd on port 4334 (protocol 26) with generic `examples/rsyncd.conf.example`
- **AmigaOS port** — secondary client with console word-wrap
- **Cross-platform build** — Linux, FreeBSD, macOS, Windows (MinGW) via CMake; Amiga via `amiga/Makefile`
- **Example INI files** — `clia-terminal.main.ini.example`, `clia-terminal.secondary.ini.example`, `local/examples/*.ini.example`
- **Operator scripts** — `scripts/start-cliad-daemon.sh`, `stop-cliad.sh`, rsync helpers

### Configuration

- Generic defaults: `workspace` name, empty workspace path, env/INI-driven roots
- State directory: `~/.config/clia/`
- No hardcoded operator hostnames, LAN IPs, or private paths in shipped sources or examples
