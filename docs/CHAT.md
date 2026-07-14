# Chat and commands

The secondary **cliad** client exposes an interactive slash-command interface. Input is expanded from short aliases, sent to the main hub when needed, and printed with optional async streaming for long AI replies.

## Session start

After connect, cliad prints:

```
/ma /main → main workspace (then chat)
/i or /p  →  /pj <n|name>  →  /ct or plain text
/ai  /ai list  /ai <n>  — AI providers (list + select)
/mn /menu for full command list
```

Secondary banner (POSIX) also lists mesh status and F-key hints where supported.

## Command menu

`/menu`, `/mn`, `/cmd`, or `/cm` prints the registry grouped by area:

```
cliad commands /help <cmd> for more
  Projects   /i /index /p /projects /pj /project /ma /main /rc /rsync
  Mesh       /pg /ping /ls /list /st /status /ai /ct /chat (plain text)
  Compose    /mu /multi /sn /send
  Session    /mn /menu /cm /cmd /qu /quit /by /bye
```

Main desk adds a **Cursor** area (`/format`, `/vault`, `/help`). `/index` lists all areas unfiltered.

Per-command help: `/help <cmd>` (example: `/help pj`).

## Typical workflow

### 1. Select project

AI prompts with workspace context require an active project.

| Command | Lists |
|---------|-------|
| `/index` or `/i` | All Cursor IDE projects |
| `/projects` or `/p` | Repos under `$CLIA_CODE_ROOT/$CLIA_PROJECTS_DIR` |
| `/project 2` or `/pj myrepo` | Select by list number or directory name |
| `/main` or `/ma` | Main workspace (`[cursor] workspace=` or `$CLIA_WORKSPACE_ROOT`) |

Status line after selection:

```
project=myrepo  path=/opt/clia/projects/myrepo
```

Clear: `/project clear` or `/pj clear`.

### 2. Select AI provider (optional)

```
/ai list
  [1] openrouter (openrouter/free) *
  [2] openai (gpt-4o-mini)
  [3] cursor (agent)
/ai 2
```

`/ai` without arguments lists providers. `/ai <n>` pins provider `n` for subsequent prompts. Omit selection to use the auto prefer chain (see [PLUGINS.md](PLUGINS.md)).

### 3. Send prompt

| Form | Example |
|------|---------|
| `/chat` | `/chat Summarize the mesh protocol.` |
| `/ct` | `/ct Same as /chat (short alias)` |
| Plain text | `Explain PROJECT command.` (when project active) |

Without an active project, Cursor-backed chat returns a hint to run `/main` or `/index` then `/project`.

## Multi-line compose (POSIX)

```
/multi on
line one
line two
/send
```

`/multi off` cancels the buffer. AmigaOS wraps long single lines at terminal width instead — no `/multi` required.

## Alias expansion

Short forms map to long commands before dispatch (examples):

| Short | Long |
|-------|------|
| `/i` | `/index` |
| `/p` | `/projects` |
| `/pj` | `/project` |
| `/ma` | `/main` |
| `/pg` | `/ping` |
| `/ls` | `/list` |
| `/st` | `/status` |
| `/rc` | `/rsync` |
| `/ct` | `/chat` |
| `/mn` | `/menu` |
| `/qu` | `/quit` |

Full alias table: `share/commands.yaml`.

## rsync path search

```
/rc myrepo
```

Searches configured rsync modules on the main host. Requires `[plugins] rsync = yes` and a valid `rsync_config` path.

## Environment

| Variable | Chat effect |
|----------|-------------|
| `CLIA_CODE_ROOT` | Root for `/projects` discovery |
| `CLIA_PROJECTS_DIR` | Pool directory name (default `projects`) |
| `CLIA_WORKSPACE_ROOT` | Override for `/main` workspace path |
| `OPENROUTER_API_KEY` | OpenRouter auth on main host |
| `OPENAI_API_KEY` | OpenAI auth on main host |

Cursor auth is via `agent login` on the main host — not an env var on secondaries.

## Related

| Topic | Doc |
|-------|-----|
| Plugins | [PLUGINS.md](PLUGINS.md) |
| Mesh | [MESH.md](MESH.md) |
| Install | [INSTALL.md](INSTALL.md) |
