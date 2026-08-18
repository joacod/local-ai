# Qwen3.8 Operational Notes

Operational notes for running Qwen3.8 artifacts on Apple Silicon. This is a model-loading and server-configuration note, not a model review, quality ranking, or cross-runtime benchmark.

## Scope

The current reference context is a **MacBook Pro M4 with 48 GB unified memory**. Treat every setting as a workload-specific starting point until it has been measured on the exact machine, runtime version, model revision, and quantization.

A shared Qwen name does not make artifacts interchangeable. Record the exact repository, revision, quantization layout, tokenizer, serving mode, and returned API model ID for every run.

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

See the [oMLX guide](../omlx/README.md), [MTPLX guide](../mtplx/README.md), and [runtime configuration tuning guide](../runtime-tuning.md) for runtime-specific setup and parameter tuning.

## Tune a working server

Do not tune Qwen3.8 before the selected runtime can load the model and complete a smoke-test request. Then choose one objective—memory headroom, context size, cache reuse, interactive latency, or concurrency—and change one server parameter at a time.

Keep the model artifact fixed while tuning the server. Record cold versus warm state, exact flags, context, concurrency, memory behavior, and runtime-specific metrics. Promote a successful setting to the relevant hardware profile; keep model compatibility and chat-template notes here.

## Record with every note

- hardware, macOS version, power, and thermal state;
- runtime version and exact server flags;
- model repository, resolved revision, file size, quantization, and tokenizer;
- returned API model ID and serving mode;
- context, response cap, sampling, and thinking policy;
- cold or cached state, TTFT, prefill rate, decode rate, and peak memory;
- runtime-specific metrics such as MTPLX `mtplx_stats` or oMLX dashboard values.
