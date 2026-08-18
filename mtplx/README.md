# MTPLX

Run Apple Silicon language models with [MTPLX](https://github.com/youssofal/MTPLX),
an OpenAI-compatible server that can use a model's native Multi-Token
Prediction (MTP) head for speculative decoding.

Choose MTPLX when the artifact includes a compatible MTP head and you want to
inspect MTP versus target-only autoregressive (AR) serving. For shared model,
artifact, and qualification concepts, see [getting started](../docs/getting-started.md),
[terminology](../docs/terminology.md), [Hugging Face](../docs/hugging-face.md),
and [runtime tuning](../docs/tuning.md).

## Requirements

- Apple Silicon Mac (M1 or newer)
- the macOS version supported by the installed MTPLX release
- Python 3.11+ when using the Python installation path
- a complete model artifact with matching MTP weights
- enough disk and unified-memory headroom for the model and session cache

## Install or update

Homebrew is the reproducible terminal path:

```sh
brew install youssofal/mtplx/mtplx
mtplx help
mtplx doctor --summary
```

The [signed macOS app](https://mtplx.com/download) is another supported path.
It can set up its own runtime and place `mtplx` on `PATH`.

A Python-only alternative uses an isolated environment:

```sh
curl -fsSL https://raw.githubusercontent.com/youssofal/MTPLX/main/scripts/install_macos.sh \
  | MTPLX_VENV="$HOME/.mtplx/venv" \
    MTPLX_SKIP_GLOBAL_LAUNCHER=1 \
    bash
```

Use one installation path for the first experiment so the executable and
version are unambiguous. Do not install MTPLX into a shared or global Python
environment.

## Select and inspect a model

MTPLX needs a complete artifact with its matching native MTP weights. Use the
runtime's compatibility inspection before starting a server:

```sh
MODEL='<complete-mtplx-model-or-repository>'
mtplx pull "$MODEL"
mtplx inspect "$MODEL" --json
```

`inspect` is a compatibility check, not a model-quality evaluation. Record the
exact repository, revision, quantization, and files. A model family name shared
with oMLX, MLX, or llama.cpp does not prove that the checkpoint layout or
quantization is compatible.

See the [Qwen3.8 operational notes](../local-models/qwen38.md) for a concrete
model-family example and its artifact boundary.

## Start the API server

Use `mtplx start` when you want MTPLX to select and load a pulled model
interactively:

```sh
mtplx start
```

For an API-only foreground server after model selection/configuration:

```sh
mtplx serve \
  --host 127.0.0.1 \
  --port 8000 \
  --no-stats-footer
```

Verify the loaded model through `/v1/models` instead of guessing the API model
ID from the repository name.

## Verify the server

```sh
curl http://127.0.0.1:8000/health
curl http://127.0.0.1:8000/metrics
curl http://127.0.0.1:8000/v1/models
```

Use the returned model ID for a small request:

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

Keep the server on `127.0.0.1`. Non-localhost binds require an API key and an
intentional network boundary.

## Daily use, profiles, and qualification

The [M4 48 GB starting profile](./hardware/m4-48gb.md) documents a conservative
serving lane, but it is not a completed benchmark report. MTPLX does not yet
have a repository-wide reusable qualification/profile workflow. Use the
[shared runtime tuning and qualification guide](../docs/tuning.md) to record
one measured workload, and keep MTP and AR observations separate.

Before a reusable profile is added, record the exact chip and memory, macOS,
MTPLX version, model revision, quantization, context, serving mode, depth,
cache state, concurrency, and memory behavior.

## Confirm MTP versus AR mode

MTPLX exposes its active serving policy through `/health`. Check fields such as
`load_mtp`, `mtp_enabled`, `depth`, and `generation_mode` before recording a
result.

- Native MTP is the normal MTPLX lane when the artifact contract is verified.
- `generation_mode: "mtp"` explicitly requests MTP for a request.
- `generation_mode: "ar"` uses target-only autoregressive generation while the
  MTP runtime remains available for a later request.
- `--no-mtp` selects target-only AR at server startup.

MTP and AR results answer different runtime-configuration questions; do not
combine them in one result table.

## Reset the session cache

Clear the runtime session cache between cold rows without deleting model files:

```sh
curl -X POST http://127.0.0.1:8000/admin/cache/clear
```

Add `-H 'Authorization: Bearer <api-key>'` when the server uses an API key. Use
model-management commands or app controls separately when model files must be
removed.

## First tuning lane

Before tuning draft depth, context, fan control, scheduler modes, or
concurrency:

- use one active request and a fixed response cap;
- disable thinking for a latency-only lane, then run a separate reasoning lane;
- use unique cold prompts and repeated trials;
- read the authoritative `mtplx_stats` block from the final response chunk; and
- record `prefill_tok_s`, `decode_tok_s`, `ttft_s`, cached tokens, peak memory,
  and `accepted_by_depth` / `drafted_by_depth`.

The upstream [MTPLX documentation](https://github.com/youssofal/MTPLX/tree/main/docs)
covers runtime-specific measurement details. Use the shared tuning guide to
choose the question and keep cold and cached measurements separate.

## Troubleshooting

- **`doctor` reports a missing dependency:** repair the installation before
  diagnosing a model.
- **Inspection rejects the checkpoint:** choose a supported complete artifact;
  do not bypass the compatibility gate.
- **Memory pressure or swap grows:** stop the server, lower context or draft
  depth, or choose a smaller quantization.
- **MTP is not active:** inspect `/health`, the artifact's MTP files, and the
  installed runtime version before recording an AR result as MTP.
- **Port 8000 is busy:** inspect it with
  `lsof -nP -iTCP:8000 -sTCP:LISTEN` before stopping anything.

## Official references

- [MTPLX repository](https://github.com/youssofal/MTPLX)
- [MTPLX installation](https://github.com/youssofal/MTPLX/blob/main/INSTALL.md)
- [MTPLX quickstart](https://github.com/youssofal/MTPLX/blob/main/docs/quickstart.md)
- [MTPLX architectures](https://github.com/youssofal/MTPLX/blob/main/docs/architectures.md)
- [MTPLX API](https://github.com/youssofal/MTPLX/blob/main/docs/api.md)
- [M4 48 GB starting profile](./hardware/m4-48gb.md)
- [Runtime tuning and qualification](../docs/tuning.md)
