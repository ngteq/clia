# Plugins

**claid** loads plugins from a fixed compile-time table. Enable each plugin in `[plugins]` and configure its dedicated INI section on the **main agent host**.

Secondaries never run plugins locally — they request AI and project actions through the mesh hub.

## Enable flags

```ini
[plugins]
cursor = yes
openrouter = yes
openai = yes
rsync = yes
proxy = no
```

## Cursor agent

Runs the Cursor **`agent`** CLI on the main host. Mesh clients send prompts; the hub invokes `agent` in the active project workspace.

```ini
[cursor]
enabled = yes
mode = local_agent
workspace = /opt/clia/workspace
agent_cmd = agent
agent_flags = -p --output-format text --trust
agent_mode = agent
```

Requirements on main host:

| Requirement | Check |
|-------------|-------|
| `agent` in `$PATH` | `which agent` |
| Authenticated session | `agent login` once |

Project commands (`/index`, `/projects`, `/project`, `/main`) set the workspace before chat. See [CHAT.md](CHAT.md).

## OpenRouter

OpenAI-compatible chat completions via [OpenRouter](https://openrouter.ai/docs).

```ini
[openrouter]
enabled = yes
prefer = yes
base_url = https://openrouter.ai/api/v1
model = openrouter/free
api_key_env = OPENROUTER_API_KEY
http_referer = https://github.com/ngteq/clia
app_title = clia
```

Authentication: set `OPENROUTER_API_KEY` in the environment, or `api_key=` in INI.

**License note:** OpenRouter usage is governed by [OpenRouter terms](https://openrouter.ai/terms). API keys are operator credentials — do not commit them to INI files in shared repos.

## OpenAI-compatible API

Works with OpenAI, Groq, local vLLM, or any Chat Completions endpoint.

```ini
[openai]
enabled = yes
prefer = no
base_url = https://api.openai.com/v1
model = gpt-4o-mini
api_key_env = OPENAI_API_KEY
```

Authentication: `OPENAI_API_KEY` or INI `api_key=`.

**License note:** Provider terms apply per endpoint. See vendor documentation for the chosen `base_url`.

## rsync companion

Optional rsyncd on the main host for module path search (`/rc /rsync <name>`).

```ini
[main]
rsync_enabled = yes
rsync_port = 4334
rsync_protocol = 26
rsync_config = examples/rsyncd.conf.example
```

Install a local config:

```bash
./scripts/install-clai-rsync-conf.sh
```

Override path with `$CLAI_RSYNC_CONFIG`. Example modules: [../examples/rsyncd.conf.example](../examples/rsyncd.conf.example).

## proxy

Reserved — keep `enabled = no`. No behaviour in v0.3.5.

## Provider selection

On any client with chat UI:

| Input | Effect |
|-------|--------|
| `/ai` or `/ai list` | Numbered list of ready providers |
| `/ai <n>` | Pin provider for next prompts |
| `/ct <text>` or plain text | Send prompt via selected or auto chain |

Auto chain order: plugins with `prefer = yes` first, then OpenRouter → OpenAI → Cursor.

## Amiga secondary

Amiga clients use mesh relay only. AI provider list and execution happen on the main host; the Amiga binary includes relay stubs for local `/ai list` display.

## Related

| Topic | Doc |
|-------|-----|
| Chat workflow | [CHAT.md](CHAT.md) |
| Main INI example | [../clai-terminal.main.ini.example](../clai-terminal.main.ini.example) |
