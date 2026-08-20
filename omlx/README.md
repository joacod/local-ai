# oMLX

Run MLX-compatible language models with [oMLX](https://github.com/jundot/omlx),
an OpenAI-compatible server with model management, continuous batching, and
tiered KV caching. The macOS app is convenient when you want a managed model
directory and dashboard; the CLI is useful when you need the exact server flags
visible in a terminal.

This repository currently provides reference notes only: there is no oMLX
wrapper, custom configuration format, or verified model setup here. Use oMLX's
native app or CLI and treat the profile below as a starting reference.

For shared model, artifact, and optional tuning concepts, see the repository's
[getting-started](../docs/getting-started.md), [terminology](../docs/terminology.md),
[Hugging Face](../docs/hugging-face.md), and [tuning](../docs/tuning.md) notes.

## Quick start (reference path)

Use the native Homebrew installation and managed service:

```sh
brew tap jundot/omlx https://github.com/jundot/omlx
brew install jundot/omlx/omlx
omlx start
```

Open `http://127.0.0.1:8000/admin`, configure the model directory, and download
an MLX-compatible model through the dashboard. The upstream app and CLI own
model management; this repository does not add another layer.

## Requirements

- Apple Silicon Mac
- the macOS and Python versions supported by the installed oMLX release (the
  source path below currently documents Python 3.11–3.13)
- an MLX-format model supported by that release
- enough disk and unified-memory headroom for the selected model and cache

## Install or update

### macOS app

Download the [latest oMLX release](https://github.com/jundot/omlx/releases), open
the `.dmg`, and drag oMLX to Applications. The app provides a CLI shim at
`~/.omlx/bin/omlx` and guides the model-directory setup.

### Homebrew

```sh
brew tap jundot/omlx https://github.com/jundot/omlx
brew install jundot/omlx/omlx
omlx --help
omlx serve --help
```

For a managed service:

```sh
omlx start
omlx stop
omlx restart
```

Use the foreground command in the next section when you need server flags
visible in a terminal or when doing optional tuning work.

### From source

The upstream source path requires Apple Silicon and Python 3.11–3.13:

```sh
git clone https://github.com/jundot/omlx.git
cd omlx
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -e .
```

Native custom kernels are optional for affected model families and require full
Xcode:

```sh
OMLX_WITH_CUSTOM_KERNEL=1 python -m pip install -e .
```

Use one installation path for the first experiment so the executable and
version are unambiguous.

## Start the server manually

Create the default model directory and keep the server local:

```sh
mkdir -p "$HOME/.omlx/models"
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --host 127.0.0.1 \
  --port 8000
```

Open `http://127.0.0.1:8000/admin` and use the dashboard's model downloader to
inspect and download a compatible MLX artifact. The dashboard also provides
model status, settings, chat, and performance observations.

The model directory contains subdirectories managed by oMLX. Record the exact
repository, revision, quantization, and API model ID returned by `/v1/models`.
A catalog label is not automatically a repository ID, and an MLX artifact made
for another server is not automatically compatible.

## Verify the API

```sh
curl http://127.0.0.1:8000/health
curl http://127.0.0.1:8000/v1/models
```

Use the model ID returned by `/v1/models` for a small request:

```sh
curl http://127.0.0.1:8000/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "<id-from-v1-models>",
    "messages": [{"role": "user", "content": "Reply with: oMLX is ready."}],
    "temperature": 0,
    "max_tokens": 32,
    "stream": false
  }'
```

The OpenAI-compatible base URL for coding tools is:

```text
http://127.0.0.1:8000/v1
```

Do not expose the server beyond localhost without an API key and an intentional
network boundary.

## Daily use and optional profile

oMLX does not currently have a repository launcher or a checked-in reusable
qualification command. Use `omlx serve`, the managed service, or the app for
daily use. The [M4 Max 48 GB starting profile](./hardware/m4-48gb.md) records a
conservative baseline; its values are not a completed measurement report.

When no profile matches the machine, model, and workload, use the shared
[optional runtime tuning guide](../docs/tuning.md). Confirm the server
and one chat request first, then change one setting at a time. Add a reusable
profile only after the exact environment and workload have been measured.

## Advanced cache examples

Start with one model and one active request so memory behavior is observable:

```sh
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --host 127.0.0.1 \
  --port 8000 \
  --memory-guard balanced \
  --max-concurrent-requests 1 \
  --no-cache
```

For a deliberately cached lane, use a dedicated directory:

```sh
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --host 127.0.0.1 \
  --port 8000 \
  --memory-guard balanced \
  --max-concurrent-requests 1 \
  --paged-ssd-cache-dir "$HOME/.omlx/cache"
```

Keep cold, in-memory cached, and SSD-restored observations separate. A
context target is not a promise that every model fits; weights, activations,
KV state, cache state, macOS, and other applications share unified memory.

## Runtime cache reset

From the admin dashboard, open **Runtime Cache Observability** and clear the
Memory or SSD tier. For scripted runs, the authenticated endpoints are:

```text
POST http://127.0.0.1:8000/admin/api/hot-cache/clear
POST http://127.0.0.1:8000/admin/api/ssd-cache/clear
```

These endpoints require an admin session when authentication is enabled. They
clear runtime cache state, not downloaded model files. Stop a foreground server
with `Control-C`; use `omlx stop` for a managed service.

## Troubleshooting

- **Port 8000 is busy:** inspect it with
  `lsof -nP -iTCP:8000 -sTCP:LISTEN` before stopping anything.
- **The model is rejected:** use an MLX artifact supported by the installed
  release and keep its repository/revision separate from other conversions.
- **Memory pressure grows:** stop the server, lower the context target, unload
  other models, reduce concurrency, or choose a smaller quantization.
- **Cache results are confusing:** clear the relevant tier and label whether a
  run is cold, in-memory cached, or SSD-restored.
- **Network exposure is unexpected:** bind to `127.0.0.1` and inspect any
  intentional API-key/network configuration.

## Official references

- [oMLX repository](https://github.com/jundot/omlx)
- [oMLX releases](https://github.com/jundot/omlx/releases)
- [oMLX performance explorer](https://omlx.ai/benchmarks/performance)
- [oMLX quantization notes](https://github.com/jundot/omlx/blob/main/docs/oQ_Quantization.md)
- [Optional runtime tuning](../docs/tuning.md)
