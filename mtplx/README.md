# MTPLX

[MTPLX](https://github.com/youssofal/MTPLX) is an Apple Silicon runtime and
OpenAI-compatible local server for models with native Multi-Token Prediction
(MTP) heads. It can use those heads for speculative decoding without a second
draft model.

Use MTPLX with a complete MTPLX-compatible artifact. The model's MTP weights
must match its target model; an ordinary MLX conversion is not a substitute.
A known-working M4 Max setup is recorded in the [hardware
profile](./hardware/m4-48gb.md). This repository does not add a launcher because
MTPLX already provides model management and an interactive startup workflow.

For shared model, artifact, and optional tuning concepts, see [getting
started](../docs/getting-started.md), [Hugging Face and model
artifacts](../docs/hugging-face.md), and [runtime tuning and
optional tuning](../docs/tuning.md).

## Quick start

Use the native MTPLX CLI:

```sh
brew install youssofal/mtplx/mtplx

MODEL="Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed"

mtplx pull "$MODEL"
mtplx inspect "$MODEL" --json
mtplx start
```

The model is a complete MTPLX artifact with matching native MTP weights. The
first pull may take time and disk space; `inspect` should succeed before the
server starts.

## Requirements

- Apple Silicon (M1 or newer)
- A macOS version supported by the installed MTPLX release
- Homebrew, when using the installation path below
- Enough unified memory and disk headroom for the selected artifact, runtime,
  and session cache
- A complete model artifact containing its matching native MTP weights

Use one supported installation path for the first experiment so the executable
and version are unambiguous.

## Install or update

The Homebrew formula installs the `mtplx` command and its isolated runtime:

```sh
brew install youssofal/mtplx/mtplx
which mtplx
mtplx --version
mtplx help
mtplx doctor --summary
```

For an existing Homebrew installation, update it with:

```sh
brew upgrade youssofal/mtplx/mtplx
```

## Get and inspect a model

MTPLX needs a complete artifact with its matching native MTP weights. The
example below uses the Qwen 3.8 artifact documented in the [model
notes](../local-models/qwen38.md):

```sh
MODEL="Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed"

mtplx pull "$MODEL"
mtplx models
mtplx inspect "$MODEL" --json
```

`inspect` is a compatibility check, not a model-quality evaluation. It must
accept the complete artifact before serving it. Do not replace an MTPLX
checkpoint with a generic MLX, oMLX, llama.cpp, or GGUF artifact.

## Start the server

Use the native MTPLX workflow:

```sh
mtplx start
```

If the wizard presents choices, select the downloaded model and a normal or
Auto mode. Start with the default local settings before changing tuning, fan,
Burst, context, or concurrency options.

The local server uses:

```text
http://127.0.0.1:8000/
```

Its OpenAI-compatible base URL is:

```text
http://127.0.0.1:8000/v1
```

Keep the terminal running while using the server. Stop it from another
terminal with:

```sh
mtplx stop
```

For a foreground API-only server, the upstream command is:

```sh
mtplx serve \
  --host 127.0.0.1 \
  --port 8000 \
  --no-stats-footer
```

Keep the server bound to localhost unless you intentionally configure
authentication and a network boundary.

## Verify the API

Check the runtime state and the served model before making a request:

```sh
curl -fsS http://127.0.0.1:8000/health
curl -fsS http://127.0.0.1:8000/v1/models
```

Use the model `id` returned by `/v1/models`; do not infer it from the
Hugging Face repository name. For a small non-streaming Qwen smoke test:

```sh
MODEL_ID='<id-from-v1-models>'
curl -fsS http://127.0.0.1:8000/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "'"$MODEL_ID"'",
    "messages": [
      {
        "role": "user",
        "content": "Reply exactly with: MTPLX is ready."
      }
    ],
    "temperature": 0,
    "max_tokens": 32,
    "enable_thinking": false,
    "stream": false
  }'
```

## Advanced MTP diagnostics

When you need to confirm the serving mode, inspect `/health` and the final
response statistics for fields such as `generation_mode`, `load_mtp`,
`mtp_enabled`, `runtime_mtp_enabled`, `draft_head_installed`, and `depth`. Keep
MTP and target-only autoregressive (AR) observations as separate serving modes.

## Daily use

For daily use, start the server, list cached artifacts when needed, and stop the
server when finished:

```sh
mtplx start
mtplx models
mtplx stop
```

No repository launcher is currently provided because these native commands
already cover installation, model management, startup, and shutdown. For an
optional settings change after the basic run works, use the [runtime tuning
guide](../docs/tuning.md). The [M4 Max 48 GB profile](./hardware/m4-48gb.md)
records one known-working setup; do not copy its values to another Mac without
verification.

## Troubleshooting

- If `doctor` reports a missing dependency, repair the installation before
  diagnosing the model.
- If inspection rejects the checkpoint, choose a supported complete artifact;
  do not bypass the compatibility check.
- If memory pressure or swap grows, stop the server and reduce context or draft
  depth, or choose a smaller quantization.
- If MTP is not active, inspect `/health`, the artifact's MTP files, and the
  installed runtime version before recording an AR result as MTP.
- If port `8000` is busy, inspect it with
  `lsof -nP -iTCP:8000 -sTCP:LISTEN` before stopping anything.

## Official references

- [MTPLX repository](https://github.com/youssofal/MTPLX)
- [MTPLX installation](https://github.com/youssofal/MTPLX/blob/main/INSTALL.md)
- [MTPLX quickstart](https://github.com/youssofal/MTPLX/blob/main/docs/quickstart.md)
- [MTPLX API](https://github.com/youssofal/MTPLX/blob/main/docs/api.md)
- [Qwen 3.8 model note](../local-models/qwen38.md)
- [M4 Max 48 GB known-working profile](./hardware/m4-48gb.md)
- [Optional runtime tuning](../docs/tuning.md)
