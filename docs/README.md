# Shared documentation

These notes explain concepts shared by the runtimes. They are reference material
for people who want to understand a local-model setup; runtime-specific commands
and flags stay in the runtime guides.

## Start here

| Document | Use it for |
| --- | --- |
| [Getting started](./getting-started.md) | The simple path from a Mac to a local server |
| [Terminology](./terminology.md) | Model, artifact, memory, context, and serving terms |
| [Hugging Face and model artifacts](./hugging-face.md) | Repositories, revisions, conversions, files, and caches |
| [Optional runtime tuning](./tuning.md) | Changing settings after a server already works |

You can start with a [known working setup](../README.md#known-working-setups)
without reading these documents first.

## Runtime guides

- [MLX](../mlx/README.md) — MLX-compatible models and `mlx_lm.server`
- [llama.cpp](../llama-cpp/README.md) — GGUF models and `llama-server`
- [oMLX](../omlx/README.md) — a reference path for the managed MLX server
- [MTPLX](../mtplx/README.md) — the native CLI for complete MTP artifacts

## Model notes

[Local model notes](../local-models/README.md) hold verified compatibility and
operational details for model families or artifacts. They are not rankings or a
benchmark archive.

Return to the [repository README](../README.md) for the shortest navigation path.
