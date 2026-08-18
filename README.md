# Local AI

Practical notes for running local language-model servers and inference tools on Apple Silicon. The current primary reference machine is a **MacBook Pro — Apple M4 — 48 GB unified memory**; machine-specific settings are always labeled as starting points or measured results.

This repository is not a model-quality benchmark, model leaderboard, research notebook, or cross-runtime comparison project. It focuses on getting a useful local server running and tuning that server for a specific model, workload, and Mac.

## Runtime guides

| Runtime | Use it to explore | Guide |
| --- | --- | --- |
| MLX / `mlx-lm` | MLX model workflows and local serving | [`./mlx`](./mlx) |
| `llama.cpp` | GGUF models and Metal inference | [`./llama-cpp`](./llama-cpp) |
| `oMLX` | Continuous batching and tiered KV caching | [`./omlx`](./omlx) |
| `MTPLX` | Native MTP speculative decoding | [`./mtplx`](./mtplx) |

## Local model notes

The [`local-models/`](./local-models) directory holds operational notes for specific model artifacts: compatibility requirements, chat templates, quantization details, context behavior, and runtime-specific loading instructions. These notes are not model reviews or quality rankings.

## Typical workflow

1. Choose the runtime that supports the model format or serving feature you need.
2. Follow its installation, model-selection, server, and API smoke-test instructions.
3. Stop and resolve model-load, health, or memory errors before tuning parameters.
4. Once one server and one model work, use the [runtime configuration tuning guide](./runtime-tuning.md) to test one parameter at a time for your workload.
5. Record the resulting recommendation in the relevant hardware profile or model note, including the exact runtime, model revision, and flags.

## What belongs here

- Installation and dependency notes for local inference runtimes.
- Server launch commands, API examples, aliases, helper scripts, and hardware-specific presets.
- Memory, context, KV-cache, quantization, batching, concurrency, and runtime-mode guidance.
- Small, focused measurements that choose better parameters for one server/model/machine combination.

## What does not belong here

- Best-model lists, intelligence or quality rankings, model-vs-model reviews, prompt or reasoning benchmarks, or cross-runtime leaderboards.
- Large benchmark matrices whose purpose is to compare engines or model quality.
- Historical experiment archives. Keep only current recommendations and clearly labeled operational notes.

## Shared rules

- Run only one large model server at a time. MLX and `llama.cpp` use port `8080`; `oMLX` and `MTPLX` use `8000` by default.
- Keep servers on `127.0.0.1` unless remote access is intentional and authenticated.
- Confirm free disk and memory headroom before downloading a large model.
- Record the exact model repository, revision, quantization, runtime version, serving mode, and tuning objective for every documented result.

## Official references

- [oMLX](https://github.com/jundot/omlx)
- [MTPLX](https://github.com/youssofal/MTPLX)
- [MLX LM](https://github.com/ml-explore/mlx-lm)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
