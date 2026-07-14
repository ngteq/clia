# Mesh

The mesh connects **secondary** `claid` instances to a **main hub** over TCP. All project, AI, and rsync operations run on the main host; secondaries exchange line-framed commands and text replies.

## Topology

```
                    ┌─────────────────────────────┐
                    │ Main host (agent)           │
                    │  claid  [main] role=hub    │
                    │  listen 0.0.0.0:4224        │
                    │  plugins + Cursor agent     │
                    └──────────────┬──────────────┘
                                   │ TCP M42/1
              ┌────────────────────┼────────────────────┐
              │                    │                    │
        ┌─────┴─────┐        ┌─────┴─────┐        ┌─────┴─────┐
        │ Linux     │        │ FreeBSD   │        │ AmigaOS   │
        │ secondary │        │ secondary │        │ secondary │
        └───────────┘        └───────────┘        └───────────┘
```

## Ports

| Port | Service | Required |
|------|---------|----------|
| 4224 | Mesh hub | yes |
| 4334 | rsync companion (protocol 26) | no |

## Main hub configuration

```ini
[main]
role = hub
bind_host = 0.0.0.0
port = 4224
client_id = claid-main
platform = linux
max_clients = 32
auth_token =
```

Start:

```bash
./scripts/start-claid-daemon.sh
```

Verify:

```bash
./scripts/status-claid-main.sh
```

## Secondary configuration

```ini
[secondary]
host = 192.168.1.100
port = 4224
client_id = claid-linux
platform = linux
auth_token =
```

Connect:

```bash
claid -c secondary.ini
claid -c secondary.ini 192.168.1.100 4224 claid-linux
```

Platform templates: `local/examples/secondary.*.ini.example`.

## Wire protocol

| Property | Value |
|----------|-------|
| Transport | TCP |
| Framing | One line per message, newline-terminated |
| Prefix | `M42/1` |
| Max line | 8192 bytes |

Example request:

```
M42/1 PING
```

Example reply:

```
M42/1 OK text="pong"
```

Command tokens (hub parser): `HELLO`, `OK`, `ERR`, `PING`, `PONG`, `LIST`, `AI`, `AI_REPLY`, `PROJECT`, `RSYNC`, `STATUS`, `BYE`.

Implementation: `src/vault_mesh.c`, `src/vault_mesh_hub.c`, `src/vault_mesh_client.c`.

## Secondary slash commands

| Long | Short | Hub action |
|------|-------|------------|
| `/ping` | `/pg` | Reachability test |
| `/list` | `/ls` | Connected clients |
| `/status` | `/st` | Hub status line |
| `/index` | `/i` | List Cursor projects |
| `/projects` | `/p` | List repos in projects pool |
| `/project <n\|name>` | `/pj` | Select active project |
| `/main` | `/ma` | Select main workspace |
| `/rsync <name>` | `/rc` | Search rsync modules |
| `/ai` | — | List or select AI provider |
| `/chat <text>` | `/ct` | Send chat prompt |
| `/menu` | `/mn` | Print command menu |
| `/quit` | `/qu` | Disconnect |
| `/bye` | `/by` | Disconnect (alias) |

Full registry: `share/commands.yaml`. Menu layout: `share/areas.yaml`.

## Authentication

Optional shared secret on both sides:

```ini
auth_token = your-secret
```

Mismatch closes the connection with an error reply.

## Client identity

Each secondary sends `client_id` and `platform` at hello. The hub tracks up to **32** simultaneous clients (minimum design target: 5).

Last selected project can persist per `client_id` under `~/.config/clia/`.

## AmigaOS notes

| Constraint | Detail |
|------------|--------|
| IP address | Dotted IPv4 only in `host=` |
| DNS | Not available in Amiga TCP stub |
| Console | Word-wrap at Shell width; lines up to 4096 chars |
| AI relay | Executed on main host (F11 shortcut in banner) |

Build: `./scripts/build-amiga.sh`. Stage: `./scripts/stage-amiga.sh`.

## Related

| Topic | Doc |
|-------|-----|
| Chat workflow | [CHAT.md](CHAT.md) |
| Install | [INSTALL.md](INSTALL.md) |
| Amiga port | [../amiga/README.md](../amiga/README.md) |
