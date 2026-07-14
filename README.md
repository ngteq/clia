# clia v0.3.5

**clia** is a terminal mesh client that connects secondary machines to a main agent host. The compiled binary is **`claid`**.

Repository: [https://github.com/ngteq/clia](https://github.com/ngteq/clia)

## Features

- **Mesh hub** on the main host (TCP port 4224) — multiple secondary clients
- **Cursor agent relay** — AI chat from remote terminals via the main host
- **Built-in plugins** — OpenRouter, OpenAI-compatible API, rsync search
- **Slash-command chat UI** — `/ai`, `/ct`, `/project`, `/menu`, and short aliases
- **AmigaOS port** — secondary client for retro setups

## Quick start

```bash
git clone https://github.com/ngteq/clia.git
cd clia
./build.sh
```

Binary: `build/bin/claid`

Copy and edit the example INI:

```bash
mkdir -p ~/.config/clia
cp clai-terminal.main.ini.example ~/.config/clia/main.ini
# edit workspace= and rsync_config= paths
```

Start the main daemon (mesh + optional rsync companion):

```bash
CLIA_MAIN_INI=~/.config/clia/main.ini ./scripts/start-claid-daemon.sh
```

Connect a secondary client:

```bash
./build/bin/claid -c clai-terminal.secondary.ini.example 192.168.1.100 4224
```

## Configuration

| Path | Purpose |
|------|---------|
| `~/.config/clia/clia.ini` | Default config discovery |
| `~/.config/clia/rsyncd.conf` | rsync companion config (from `examples/rsyncd.conf.example`) |
| `$CLIA_WORKSPACE_ROOT` | Main workspace for `/main` |
| `$CLIA_CODE_ROOT` | Code root for project discovery |
| `$CLIA_PROJECTS_DIR` | Projects pool dir name (default: `projects`) |
| `$CLAI_RSYNC_CONFIG` | Override rsync config path |

See [docs/INSTALL.md](docs/INSTALL.md) for platform builds and [examples/README.md](examples/README.md) for INI templates.

## Documentation

| Doc | Topic |
|-----|-------|
| [docs/INSTALL.md](docs/INSTALL.md) | Build and install |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | Components and layout |
| [docs/PLUGINS.md](docs/PLUGINS.md) | Cursor, OpenRouter, OpenAI |
| [docs/MESH.md](docs/MESH.md) | Hub / secondary protocol |
| [docs/CHAT.md](docs/CHAT.md) | `/ai`, `/ct`, slash commands |
| [amiga/README.md](amiga/README.md) | AmigaOS secondary build |

## License

GPL-3.0-or-later — see [LICENSE](LICENSE).
