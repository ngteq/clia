# Install and build

**clia** ships one binary: **`cliad`**. Build from source with CMake, configure with INI files, run a **main hub** on the agent host and **secondary clients** on remote terminals.

## Prerequisites

| Component | Required | Notes |
|-----------|----------|-------|
| CMake | yes | ≥ 3.16 |
| C compiler | yes | C11 (GCC, Clang, MinGW) |
| POSIX threads | yes | Linux, FreeBSD, macOS |
| Cursor `agent` CLI | no | Main host only — `[cursor]` plugin |
| rsync | no | Optional companion on main host |
| Amiga cross-GCC | no | AmigaOS secondary only — `/opt/amiga` toolchain |

## Build

```bash
git clone https://github.com/ngteq/clia.git
cd clia
./build.sh
```

Output: `build/bin/cliad`

Platform wrappers (same CMake tree):

| Script | Target |
|--------|--------|
| `scripts/build-linux.sh` | Native Linux |
| `scripts/build-freebsd.sh` | Native FreeBSD |
| `scripts/build-macos.sh` | Native macOS |
| `scripts/build-windows-mingw.sh` | Windows cross-compile |
| `scripts/build-amiga.sh` | AmigaOS m68k binaries |

Optional install:

```bash
cmake --install build --prefix /usr/local
```

Installs `cliad`, example INI files, and `share/clia/` menu YAML.

## Configuration layout

```bash
mkdir -p ~/.config/clia
cp clia-terminal.main.ini.example ~/.config/clia/main.ini
cp local/examples/secondary.linux.ini.example ~/.config/clia/secondary.ini
cp examples/rsyncd.conf.example ~/.config/clia/rsyncd.conf
```

Edit paths in `main.ini`:

| Key | Purpose |
|-----|---------|
| `[cursor] workspace=` | Default workspace on main host |
| `[main] rsync_config=` | rsyncd config for `/rc` search |
| `[openrouter]` / `[openai]` | API keys via env or INI |

Set `[secondary] host=` to the main host LAN address (example: `192.168.1.100`).

## INI discovery

First readable file wins:

1. `-c FILE` / `--config FILE`
2. `$CLIA_INI`
3. `$CLIA_TERMINAL_INI`
4. `$VAULT_TERMINAL_INI`
5. `~/.config/clia/clia.ini`
6. `./clia.ini`, `./clia-terminal.ini`
7. `./local/main.ini`, `./local/desk.ini`, `./local/secondary.ini`, `./local/hub.ini`

## Environment variables

| Variable | Purpose |
|----------|---------|
| `CLIA_INI` | Explicit INI path |
| `CLIA_CODE_ROOT` | Code tree root for project discovery |
| `CLIA_PROJECTS_DIR` | Subdir under code root for `/projects` (default: `projects`) |
| `CLIA_WORKSPACE_ROOT` | Main workspace override for `/main` |
| `CLIA_RSYNC_CONFIG` | rsyncd config path |
| `CLIA_STATE_DIR` | Runtime state (default: `~/.config/clia`) |
| `CLIA_TERMINAL_SHARE` | Menu YAML search path |
| `OPENROUTER_API_KEY` | OpenRouter plugin |
| `OPENAI_API_KEY` | OpenAI-compatible plugin |

## Run main hub

```bash
CLIA_MAIN_INI=~/.config/clia/main.ini ./scripts/start-cliad-daemon.sh
./scripts/status-cliad-main.sh
./scripts/stop-cliad.sh
```

Interactive desk on the same machine:

```bash
./scripts/start-cliad-desk.sh
```

## Run secondary client

```bash
./build/bin/cliad -c ~/.config/clia/secondary.ini
./build/bin/cliad -c ~/.config/clia/secondary.ini 192.168.1.100 4224 cliad-linux
```

## AmigaOS secondary

```bash
./scripts/build-amiga.sh
MAIN_HOST=192.168.1.100 ./scripts/stage-amiga.sh
```

Copy `amiga/stage/` to the Amiga. Requires dotted IPv4 in `host=` (no DNS in the Amiga TCP layer). Details: [amiga/README.md](../amiga/README.md).

## Related

| Topic | Doc |
|-------|-----|
| Components | [ARCHITECTURE.md](ARCHITECTURE.md) |
| Mesh setup | [MESH.md](MESH.md) |
| INI templates | [../examples/README.md](../examples/README.md) |
