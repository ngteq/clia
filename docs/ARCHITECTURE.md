# Architecture

## Overview

**clia** is the project (build, INI schema, scripts, documentation). **`claid`** is the runtime binary: mesh hub, secondary client, and built-in plugin host.

```
Secondary terminals                Main agent host
┌─────────────────┐               ┌──────────────────────────┐
│ claid secondary │── TCP :4224 ──│ claid hub / main         │
│ slash-command UI│               │ Cursor agent + plugins   │
└─────────────────┘               │ optional rsync :4334     │
                                  └──────────────────────────┘
```

AI and project operations execute on the **main host**. Secondaries send line commands; the hub returns text replies.

## Repository map

| Path | Role |
|------|------|
| `src/` | Core: mesh, config, plugins, Cursor relay, chat |
| `include/` | Public C headers |
| `share/` | Menu registry (`areas.yaml`, `commands.yaml`) |
| `scripts/` | Build, start/stop, rsync install |
| `examples/` | rsyncd and INI references |
| `local/examples/` | Per-platform secondary INI templates |
| `amiga/` | AmigaOS port (separate Makefile) |
| `vendor/hybbx/` | Minimal shared C utilities (GPL) |

## Runtime roles

| Role | INI | Behaviour |
|------|-----|-----------|
| `hub` | `[main] role=hub` | Mesh listener; plugins on main host |
| `main` | `[main] role=main` | Hub + optional interactive desk session |
| `secondary` | `[secondary]` + connect target | TCP client; forwards slash commands |

Connect target: `[secondary] host=` and `port=` in INI, or `HOST PORT [client_id]` on the command line.

## INI sections

| Section | Content |
|---------|---------|
| `[main]` | Hub bind, port, auth, rsync companion |
| `[secondary]` | Remote hub address, client identity |
| `[plugins]` | Enable flags: cursor, openrouter, openai, rsync, proxy |
| `[cursor]` | Local Cursor `agent` invocation |
| `[openrouter]` | OpenRouter HTTP API |
| `[openai]` | OpenAI-compatible HTTP API |
| `[proxy]` | Reserved stub |

## Plugin model

Plugins are **compiled in** (`src/clai_plugins.c`) — no dynamic `.so` loading.

| Plugin | Runs on | Function |
|--------|---------|----------|
| cursor | Main | Spawns Cursor `agent` for workspace-aware chat |
| openrouter | Main | HTTP chat completions relay |
| openai | Main | Generic OpenAI-compatible relay |
| rsync | Main | Optional rsyncd companion for path search |
| proxy | — | Reserved |

Provider priority follows INI `prefer=` flags and `/ai` selection on the client.

## Mesh stack

| Module | File |
|--------|------|
| Protocol encode/decode | `src/vault_mesh.c` |
| Hub (main) | `src/vault_mesh_hub.c` |
| Client (secondary) | `src/vault_mesh_client.c` |
| Async chat output | `src/clai_chat_async.c` |

Wire format: line-oriented `M42/1 CMD key=value …` over TCP (default port **4224**).

## Project discovery

| Command | Scope |
|---------|-------|
| `/index` | All discovered Cursor IDE projects |
| `/projects` | Repos under `$CLIA_CODE_ROOT/$CLIA_PROJECTS_DIR` |
| `/main` | Workspace from `[cursor] workspace=` or `$CLIA_WORKSPACE_ROOT` |

Implementation: `src/vault_cursor_projects.c`.

## Menu registry

Slash commands and help text load from `share/areas.yaml` and `share/commands.yaml` (embedded fallback on Amiga). Renderer: `src/clai_menu_registry.c`.

## State files

Default directory: `~/.config/clia/` — pid files, daemon log, rsync config, last-project cache per client id.

## Build system

CMake project `clia` v0.3.5. Platform glue: `cmake/ClaiTerminalPlatforms.cmake`.

| Build | Output |
|-------|--------|
| `./build.sh` | `build/bin/claid` |
| `./scripts/build-amiga.sh` | `amiga/build/claid`, `amiga/build/clai-terminal` |

## Related

| Topic | Doc |
|-------|-----|
| Mesh protocol | [MESH.md](MESH.md) |
| Plugins | [PLUGINS.md](PLUGINS.md) |
| Chat UI | [CHAT.md](CHAT.md) |
