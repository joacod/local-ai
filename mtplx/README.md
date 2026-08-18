# MTPLX

Run Apple Silicon language models with [MTPLX](https://github.com/youssofal/MTPLX), an OpenAI-compatible server that can use a model's native Multi-Token Prediction (MTP) head for speculative decoding.

This is the operational guide for installing MTPLX, selecting a compatible model, starting a local server, and tuning MTP, context, cache, and concurrency settings. The current reference machine is a **MacBook Pro — Apple M4 — 48 GB unified memory**. Values described as recommendations are starting points, not universal optima; measured values must include the machine, runtime, model, quantization, and serving mode.

This repository is not a model-quality or model-ranking benchmark. Use the [shared comparison plan](../benchmarking.md) only for small, reproducible runtime and configuration measurements.

## Requirements

- Apple Silicon Mac (M1 or newer)
- macOS 14 or newer
- Python 3.11+ when using the Python install path
- enough disk space and unified-memory headroom for the selected model

## Guide boundaries

- **In scope:** installation, model compatibility, server startup, MTP/AR mode, context and cache behavior, concurrency, API checks, and troubleshooting.
- **Out of scope:** model intelligence rankings, prompt-quality comparisons, and cross-model leaderboards.

The upstream project recommends Qwen3.8 Optimized Speed on Macs with 32 GB or more. A 48 GB Mac is a reasonable starting target, but context length, other applications, and model allocations still determine whether a run is comfortable.

## Install

Homebrew is the most reproducible terminal path:

```sh
brew install youssofal/mtplx/mtplx
mtplx help
mtplx doctor --summary
```

The [signed macOS app](https://mtplx.com/download) is another supported path. It can set up its own runtime and place `mtplx` on `PATH`. A Python-only alternative is:

```sh
curl -fsSL https://raw.githubusercontent.com/youssofal/MTPLX/main/scripts/install_macos.sh \
  | MTPLX_VENV="$HOME/.mtplx/venv" \
    MTPLX_SKIP_GLOBAL_LAUNCHER=1 \
    bash
```

The official installer creates an isolated virtual environment and a user-local launcher. Do not install `MTPLX` into a shared or global Python environment.

Use one installation path for the first experiment so the executable and version are unambiguous.

## Select and verify a model

MTPLX needs a complete model artifact with its matching MTP weights. `inspect` is a compatibility check for the runtime; it is not a model-quality evaluation.

### Current workload example: Qwen3.8

Start with the model recommended by the upstream `MTPLX` quickstart:

```sh
MODEL='Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed'
mtplx pull "$MODEL"
mtplx inspect "$MODEL" --json
```

The [model card](https://huggingface.co/Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed) describes this as a 4-bit dynamic quantization with the native MTP head retained. It reports about 21.3 GB of model files on disk. The download size is not a runtime-memory guarantee; leave room for macOS, the KV cache, context, and the server.

Other Qwen3.8 candidates are available after the baseline:

- [Bare Speed](https://huggingface.co/Youssofal/Qwen3.8-27B-MTPLX-Bare-Speed) is the burst-speed variant.
- [Optimized Quality](https://huggingface.co/Youssofal/Qwen3.8-27B-MTPLX-Optimized-Quality) uses an 8-bit dynamic quantization and needs more memory.
- Current automatic selection may choose FP16 siblings on M1/M2 hardware. Verify the selected model with `doctor` and `inspect` rather than assuming the 27B Qwen3.8 artifact fits every Mac.

Do not use the `MTPLX`-specific checkpoint in `oMLX` or `llama.cpp` without first verifying that the target runtime supports its layout and quantization. A shared model name is not proof of shared loader behavior.

## Start the API server

Use `mtplx start` (or the signed app) when you want MTPLX to select and load a pulled model interactively:

```sh
mtplx start
```

For an API-only foreground server, use the documented host and port explicitly:

```sh
mtplx serve \
  --host 127.0.0.1 \
  --port 8000 \
  --no-stats-footer
```

Use `serve` after the model selection/configuration is in place. Verify the loaded model through `/v1/models` rather than assuming that the Hugging Face repository name is the API model ID. This keeps the launch command stable as MTPLX's model-management flow changes.

## Verify the server

```sh
curl http://127.0.0.1:8000/health
curl http://127.0.0.1:8000/metrics
curl http://127.0.0.1:8000/v1/models
```

A smoke-test request can use the same OpenAI-compatible shape as the other local servers:

```sh
curl http://127.0.0.1:8000/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "<id-from-v1-models>",
    "messages": [{"role": "user", "content": "Reply with: MTPLX is ready."}],
    "temperature": 0,
    "max_tokens": 32,
    "enable_thinking": false,
    "stream": false
  }'
```

The OpenAI-compatible base URL for coding tools is:

```text
http://127.0.0.1:8000/v1
```

Keep the server on `127.0.0.1`. Non-localhost binds require an API key and an intentional network boundary.

## Reference hardware

The current primary machine is documented in the [MacBook Pro M4 48 GB starting profile](./hardware/m4-48gb.md). That profile separates upstream defaults, conservative recommendations, and values that still need to be measured locally.

## Confirm MTP mode

`MTPLX` exposes its active serving policy through `/health`. Check fields such as `load_mtp`, `mtp_enabled`, `depth`, and `generation_mode` before recording a result.

- Normal `MTPLX` serving uses native MTP when the model contract is verified.
- `generation_mode: "mtp"` explicitly requests MTP for a request.
- `generation_mode: "ar"` uses target-only autoregressive generation while keeping the MTP runtime available for a later request.
- `--no-mtp` selects target-only AR at server startup.

For the first speed comparison, keep MTP enabled and record its acceptance/depth statistics. Run an AR comparison only as a separate, explicitly labeled lane; MTP and AR numbers answer different questions.

## Reset the runtime cache

Clear `MTPLX`'s runtime session cache between cold rows without deleting model files:

```sh
curl -X POST http://127.0.0.1:8000/admin/cache/clear
```

Add `-H 'Authorization: Bearer <api-key>'` when the server is configured with an API key. Use the model-management command or app controls separately when downloaded model files must be removed.

## First benchmark baseline

This is an operational configuration lane for choosing MTP/AR mode, context, cache, and concurrency settings. It is not a model-quality or model-ranking exercise.

Before tuning fan control, scheduler modes, context, or draft depth:

- use one active request and a fixed response cap
- disable thinking for a latency-only lane, then run a separate reasoning lane
- use unique cold prompts and at least three trials per context size
- read the authoritative `mtplx_stats` block from the final response chunk
- record `prefill_tok_s`, `decode_tok_s`, `ttft_s`, cached tokens, peak memory, and `accepted_by_depth` / `drafted_by_depth`

`MTPLX`'s [benchmarking guide](https://github.com/youssofal/MTPLX/blob/main/docs/benchmarking.md) documents cache clearing, capped reasoning requests, server-side timing, and thermal discipline. Use the [cross-runtime plan](../benchmarking.md) to compare those results with MLX, `llama.cpp`, and `oMLX` without mixing cold and cached measurements.

## Troubleshooting

- If `doctor` reports a missing MLX dependency, fix that installation before diagnosing a model.
- If model inspection rejects the checkpoint, do not bypass the compatibility gate; choose a supported model or record the rejection.
- If memory pressure or swap grows, stop the server and lower context or choose a smaller model/quant.
- Run only one large model backend at a time. Existing MLX and `llama.cpp` launchers use port `8080`; `oMLX` and `MTPLX` use `8000` by default.

## Official references

- [MTPLX repository](https://github.com/youssofal/MTPLX)
- [MTPLX installation](https://github.com/youssofal/MTPLX/blob/main/INSTALL.md)
- [MTPLX quickstart](https://github.com/youssofal/MTPLX/blob/main/docs/quickstart.md)
- [MTPLX architectures](https://github.com/youssofal/MTPLX/blob/main/docs/architectures.md)
- [MTPLX API](https://github.com/youssofal/MTPLX/blob/main/docs/api.md)
- [MacBook Pro M4 48 GB starting profile](./hardware/m4-48gb.md)
- [Local AI comparison plan](../benchmarking.md)
