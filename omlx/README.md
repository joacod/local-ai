# oMLX

Run MLX-compatible language models on Apple Silicon with [oMLX](https://github.com/jundot/omlx), an OpenAI-compatible server with continuous batching, tiered KV caching, model management, and a macOS menu-bar app.

This is the operational guide for installing oMLX, loading a model, starting a local server, and choosing memory, context, cache, and concurrency settings. The current reference machine is a **MacBook Pro — Apple M4 — 48 GB unified memory**. Values described as recommendations are starting points, not universal optima; measured values must include the machine, runtime, model, quantization, and cache state.

After the server and model work, use the [runtime tuning and qualification guide](../docs/tuning.md) to choose settings for a stated workload.

## Requirements

- Apple Silicon Mac
- macOS 15 or newer
- Apple Silicon-compatible MLX model files
- enough disk space and unified-memory headroom for the selected model

## Guide boundaries

- **In scope:** installation, model loading, server startup, memory and context settings, cache behavior, concurrency, API checks, and troubleshooting.
- **Out of scope:** model intelligence rankings, prompt-quality comparisons, and cross-model leaderboards.

The macOS app is the simplest path. The Homebrew and source paths are useful when the server should be managed from a terminal.

## Install

### macOS app

Download the [latest oMLX release](https://github.com/jundot/omlx/releases), open the `.dmg`, and drag oMLX to Applications. The app includes a CLI shim at `~/.omlx/bin/omlx` and walks through the model directory and first download.

### Homebrew

```sh
brew tap jundot/omlx https://github.com/jundot/omlx
brew install jundot/omlx/omlx
omlx --help
omlx serve --help
```

Start and manage the Homebrew service with:

```sh
omlx start
omlx stop
omlx restart
```

Use the foreground command below when you want the exact server flags visible in a terminal or when collecting tuning evidence.

### From source

The upstream source install requires Python 3.11–3.13 and Apple Silicon:

```sh
git clone https://github.com/jundot/omlx.git
cd omlx
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -e .
```

A plain source install is enough for the baseline. Native custom kernels are an optional path for affected model families; they require full Xcode and are not required for every `oMLX` model:

```sh
OMLX_WITH_CUSTOM_KERNEL=1 python -m pip install -e .
```

## First server

Use the default model directory and keep the server local:

```sh
mkdir -p "$HOME/.omlx/models"
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --host 127.0.0.1 \
  --port 8000
```

The server discovers model subdirectories under `~/.omlx/models`. Open the admin dashboard at <http://127.0.0.1:8000/admin> and use its Hugging Face model downloader to select a model, inspect its files, and download it. The dashboard also exposes built-in chat, model status, settings, and performance measurements.

## Select a model

Use an MLX-format model that the installed oMLX release supports. A catalog label is not necessarily a Hugging Face repository ID, so record the repository, revision, quantization, and API model ID returned by `/v1/models`.

For a Qwen3.8 runtime-configuration workload on the 48 GB reference machine, the current catalog may include `Qwen3.8-27B-MLX-oQ4e-mtp`. Treat that as an example workload for testing server settings, not as a model-quality recommendation or a measurement that applies to every 48 GB Mac. If the catalog does not contain a compatible conversion, use a model that the installed oMLX release currently supports rather than assuming that an `MTPLX` checkpoint can use oMLX's MTP path.

## Verify the API

```sh
curl http://127.0.0.1:8000/health
curl http://127.0.0.1:8000/v1/models
```

Use the model ID returned by `/v1/models` for a smoke test:

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

Do not expose the server beyond localhost without configuring an API key and an intentional network boundary.

## Reference hardware

The current primary machine is documented in the [MacBook Pro M4 48 GB starting profile](./hardware/m4-48gb.md). That profile separates upstream defaults, conservative recommendations, and values that still need to be measured locally.

## Conservative baseline

Start with the smallest configuration that makes memory behavior observable:

- **Defaults:** `127.0.0.1:8000`, the balanced memory guard, and one model.
- **Recommendation for the reference machine:** one active request and a 32k context target until memory behavior is measured. This is a target, not a universal hard cap.
- **Cold runtime lane:** add `--no-cache` to disable oMLX's paged SSD cache. `mlx-lm` still manages its internal KV state.
- **Cache lane:** use `--paged-ssd-cache-dir` and label results as warm or SSD-restored.

For a single-request cold baseline:

```sh
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --host 127.0.0.1 \
  --port 8000 \
  --no-cache \
  --max-concurrent-requests 1
```

For a deliberately cached lane:

```sh
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --paged-ssd-cache-dir "$HOME/.omlx/cache"
```

Record whether a result is cold, in-memory cached, or SSD-restored. Cache behavior is an operational variable, not a reason to mix warm and cold numbers.

## Reset the runtime cache

From the admin dashboard, open **Runtime Cache Observability** and clear the Memory or SSD tier. For scripted runs, the authenticated admin endpoints are:

```text
POST http://127.0.0.1:8000/admin/api/hot-cache/clear
POST http://127.0.0.1:8000/admin/api/ssd-cache/clear
```

These endpoints require an admin session when authentication is enabled. They clear runtime cache state, not downloaded model files.

The SSD cache normally uses `~/.omlx/cache` and its automatic size is based on 10% of total disk capacity. Keep SSD caching as a separate warm-cache experiment.

## Stop and troubleshoot

- Stop a foreground server with `Control-C`; use `omlx stop` for a managed service.
- If port `8000` is busy, inspect it before stopping anything: `lsof -nP -iTCP:8000 -sTCP:LISTEN`.
- If memory pressure grows, stop the server, lower the context target, unload other models, or choose a smaller quant. Do not assume a model's weight size is its peak process size.
- Run only one large model backend at a time. The existing MLX and `llama.cpp` launchers use port `8080`; `oMLX` and `MTPLX` use `8000` by default.

## Official references

- [oMLX repository](https://github.com/jundot/omlx)
- [oMLX releases](https://github.com/jundot/omlx/releases)
- [oMLX performance explorer](https://omlx.ai/benchmarks/performance)
- [oMLX quantization notes](https://github.com/jundot/omlx/blob/main/docs/oQ_Quantization.md)
- [Runtime tuning and qualification guide](../docs/tuning.md)
