# Qwen3.8 Operational Notes

Operational notes for running Qwen3.8 artifacts on Apple Silicon. This is a model-loading and server-configuration note, not a model review, quality ranking, or cross-runtime benchmark.

## Scope

This file contains Qwen3.8-specific operational and compatibility notes that remain useful across machines and runtimes. Machine-specific settings and measured recommendations belong in runtime hardware profiles. Model artifacts are not automatically interchangeable across runtimes; record the exact repository, revision, quantization layout, tokenizer, serving mode, and returned API model ID for every run.

## Runtime-specific loading notes

| Runtime | Starting artifact | Operational requirement |
| --- | --- | --- |
| `MTPLX` | `Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed` | Use a complete checkpoint with its matching native MTP weights; verify with `mtplx inspect <model> --json`. |
| `oMLX` | The exact Qwen3.8 conversion selected by the model downloader | Record the repository and revision returned by the downloader; do not assume an `MTPLX` checkpoint is compatible. |
| MLX | A Qwen3.8 MLX conversion supported by the installed `mlx-lm` | Confirm the model loads through the installed MLX server before tuning parameters. |
| `llama.cpp` | A Qwen3.8 GGUF with a verified quantization | Confirm the exact GGUF and chat template before changing server flags. |

The table documents loading contracts and operational prerequisites. It does not rank the runtimes or the model artifacts.

## API smoke test

Use port `8000` for `oMLX` or `MTPLX` and `8080` for MLX or `llama.cpp`.

```sh
PORT=8000
BASE_URL="http://127.0.0.1:${PORT}"

curl "$BASE_URL/health"
curl "$BASE_URL/v1/models"
```

Use the model ID returned by `/v1/models`; do not guess it from the Hugging Face repository name.

```sh
MODEL_ID='<id-from-v1-models>'
curl "$BASE_URL/v1/chat/completions" \
  -H 'Content-Type: application/json' \
  -d "{\"model\":\"$MODEL_ID\",\"messages\":[{\"role\":\"user\",\"content\":\"Reply with: Qwen3.8 is ready.\"}],\"temperature\":0,\"max_tokens\":32,\"stream\":false}"
```

For `MTPLX`, also inspect `/health` for `load_mtp`, `mtp_enabled`, `depth`, and `generation_mode`. Keep MTP and target-only AR as separate serving modes.

See the [oMLX guide](../omlx/README.md), [MTPLX guide](../mtplx/README.md), and [runtime tuning and qualification guide](../docs/tuning.md) for runtime-specific setup and parameter tuning.

## Hardware qualification

First get Qwen3.8 working with the selected runtime and artifact. If a suitable hardware profile exists, use it as documented; otherwise qualify the exact runtime, model artifact, machine, and workload by following the [runtime tuning and qualification guide](../docs/tuning.md). Put measured machine-specific results in the relevant runtime hardware profile, and keep Qwen3.8 compatibility and other model-specific findings in this file.
