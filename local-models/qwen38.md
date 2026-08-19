# Qwen 3.8 operational notes

Operational notes for running Qwen 3.8 artifacts on Apple Silicon. This is a
model-loading and compatibility note, not a model review, quality ranking, or
cross-runtime benchmark.

## Artifact boundaries

Model artifacts are not automatically interchangeable across runtimes. Keep
the exact repository, revision, quantization layout, tokenizer, serving mode,
and returned API model ID with each run.

The MTPLX artifact used for this baseline is:

```text
Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed
```

This is an MTPLX-specific complete model artifact, not an ordinary MLX
conversion. It contains the target weights, matching native MTP components
(`mtp.safetensors`), and `mtplx_runtime.json` for the `qwen3-next-mtp` runtime
contract. The downloaded variant is the 4-bit dynamic-quantized Optimized Speed
build. The tested baseline used Hugging Face revision
`57c0ede09cec77a02ff05f19cea5d81df7a20da6`; retain a revision when reproducing
that result. Verify the artifact before serving with:

```sh
MODEL="Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed"
mtplx inspect "$MODEL" --json
```

The tested inspection result was `can_run: true`,
`support_level: "verified-native"`, and `mtp_supported: "yes"`. The model was
then served successfully by MTPLX on the [M4 Max 48 GB known-working
baseline](../mtplx/hardware/m4-48gb.md).

Do not reuse this MTPLX checkpoint as the MLX, oMLX, llama.cpp, or GGUF artifact
for another runtime. Select and verify a runtime-specific artifact instead.

## Runtime-specific loading notes

| Runtime | Starting artifact | Operational requirement |
| --- | --- | --- |
| MTPLX | `Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed` | Use the complete artifact with its matching native MTP weights; verify with `mtplx inspect <model> --json`. |
| oMLX | The exact Qwen 3.8 conversion selected by the model downloader | Record the repository and revision returned by the downloader; do not assume an MTPLX checkpoint is compatible. |
| MLX | A Qwen 3.8 MLX conversion supported by the installed `mlx-lm` | Confirm that the model loads through the installed MLX server before tuning parameters. |
| llama.cpp | A Qwen 3.8 GGUF with a verified quantization | Confirm the exact GGUF and chat template before changing server flags. |

## MTPLX API smoke test

The MTPLX server uses port `8000` and exposes the OpenAI-compatible base URL
`http://127.0.0.1:8000/v1`. Use the model ID returned by `/v1/models`, not the
Hugging Face repository name:

```sh
curl -fsS http://127.0.0.1:8000/health
curl -fsS http://127.0.0.1:8000/v1/models
```

Use the returned model ID for a small non-streaming chat request. For the
known-working MTP configuration and smoke-test result, see the [MTPLX hardware
profile](../mtplx/hardware/m4-48gb.md). The [MTPLX guide](../mtplx/README.md)
contains the complete command.

## Hardware qualification

The MTPLX result above is a known-working baseline for one Mac, runtime
version, artifact, and smoke-test workload. It is not a universal Qwen 3.8
recommendation or an optimized profile. Put machine-specific settings and
future measured results in the relevant runtime hardware profile, and keep
model compatibility facts here.
