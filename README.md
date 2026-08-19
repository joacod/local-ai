# Local AI

A practical toolkit and notebook for running open-source language models on
Apple Silicon with different local inference servers. Models, runtimes, and
Macs change over time, so this repository keeps the common workflow separate
from runtime-specific instructions and machine-specific settings.

## Start here

- [Getting started](./docs/getting-started.md) — the model → runtime → smoke
  test → profile path
- [Terminology](./docs/terminology.md) — the concepts behind local inference
- [Hugging Face and model artifacts](./docs/hugging-face.md) — repositories,
  revisions, conversions, and compatibility
- [Runtime tuning and qualification](./docs/tuning.md) — how to measure a
  useful configuration on a particular Mac

If you already know which server you want, go directly to its guide below.

## Choose a runtime

| Runtime | Why you might choose it | Guide |
| --- | --- | --- |
| MLX / mlx-lm | MLX-native model workflows and a profile-aware launcher | [MLX guide](./mlx/README.md) |
| llama.cpp | GGUF artifacts, Metal inference, and a cache-aware server launcher | [llama.cpp guide](./llama-cpp/README.md) |
| oMLX | Model management, continuous batching, and tiered KV caching | [oMLX guide](./omlx/README.md) |
| MTPLX | Native Multi-Token Prediction (MTP) serving when the artifact supports it | [MTPLX guide](./mtplx/README.md) |

No runtime is declared universally best. Artifact compatibility, workload,
memory headroom, and the settings you can measure on your Mac determine the
useful choice.

## Typical workflow

```text
choose a model artifact and runtime
  → install the runtime
  → obtain/select a compatible artifact
  → run a health, model-list, and small chat smoke test
  → use a matching hardware profile or qualify this machine
  → launch normally with the recorded settings
```

## Machine profiles

A hardware profile is a runtime + hardware + workload configuration, with the
model or model family included when it affects the settings. Checked-in values
are measured/reference configurations or clearly labeled starting points; they
are not universal defaults. A profile from an M4 Max with 48 GB does not make that
machine a repository requirement, and its settings should not be copied to a
different Mac without qualification.

- [MLX profiles and qualification](./mlx/docs/README.md)
- [llama.cpp hardware profiles](./llama-cpp/hardware)
- [oMLX M4 Max 48 GB starting profile](./omlx/hardware/m4-48gb.md)
- [MTPLX M4 Max 48 GB starting profile](./mtplx/hardware/m4-48gb.md)

## Model notes

[Local model notes](./local-models/README.md) contain operational notes for
specific model families and artifacts: compatibility facts, chat-template
requirements, context behavior, and runtime-specific loading caveats. Add
model-specific details there rather than making the root README depend on one
release.

## Scope

This is not:

- a model-quality leaderboard or best-model list;
- a model-vs-model benchmark archive; or
- a cross-runtime performance competition.

Measurements exist to answer a practical question: which settings run this
model, through this server, on this machine, for this workload?

## Official runtime references

- [mlx-lm](https://github.com/ml-explore/mlx-lm)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [oMLX](https://github.com/jundot/omlx)
- [MTPLX](https://github.com/youssofal/MTPLX)
