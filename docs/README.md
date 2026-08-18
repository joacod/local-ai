# Shared documentation

Use these notes for concepts that apply across the runtimes. Runtime-specific
commands and flags stay in the runtime guides.

## Start here

| Document | Use it for |
| --- | --- |
| [Getting started](./getting-started.md) | The normal model → runtime → smoke test → profile workflow |
| [Terminology](./terminology.md) | Model, artifact, runtime, memory, and measurement terms |
| [Hugging Face and model artifacts](./hugging-face.md) | Repositories, revisions, conversions, files, and cache concepts |
| [Runtime tuning and qualification](./tuning.md) | Choosing machine-specific settings after a server works |

## Runtime guides

- [MLX](../mlx/README.md) — MLX-compatible models and `mlx_lm.server`
- [`llama.cpp`](../llama-cpp/README.md) — GGUF models and `llama-server`
- [oMLX](../omlx/README.md) — an MLX server with model management and tiered caching
- [MTPLX](../mtplx/README.md) — an MLX server with native MTP support

## Model notes

[Local model notes](../local-models/README.md) hold compatibility and
operational details for a model family or artifact. They are not rankings or a
benchmark archive.

Return to the [repository README](../README.md) for the short navigation path.
