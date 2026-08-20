# Local AI

A practical guide and toolkit for running open-source language models locally
on Apple Silicon. It keeps simple runtime setup paths, helper scripts where
they reduce repeated work, notes for model artifacts that were actually tried,
and beginner-friendly references for understanding local inference.

## Start here

The fastest path is:

```text
pick a model and runtime
  -> install the runtime
  -> download or select the artifact
  -> start the server
  -> send a request or open its local UI
```

For a small first MLX run:

```sh
cd mlx
./setup-mlx.sh
./run-mlx-server.sh --model mlx-community/Qwen3-1.7B-4bit
```

The [MLX guide](./mlx/README.md) explains the API smoke test and the optional
`run-mlx-server` alias. If you already have a model in mind, start with the
[known working setups](#known-working-setups) below.

## Known working setups

These are combinations used successfully in this repository, not rankings or
universal recommendations.

| Model and artifact | Runtime | Evidence and starting path |
| --- | --- | --- |
| Qwen 3.6 35B-A3B — [`mlx-community/Qwen3.6-35B-A3B-4bit-DWQ`](https://huggingface.co/mlx-community/Qwen3.6-35B-A3B-4bit-DWQ) | MLX | Tested on an M4 Max with 48 GB; see the [model note](./local-models/qwen36.md) and [MLX guide](./mlx/README.md). |
| Qwen 3.8 27B — [`Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed`](https://huggingface.co/Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed) | MTPLX | Tested on an M4 Max with 48 GB; see the [model note](./local-models/qwen38.md) and [MTPLX guide](./mtplx/README.md). |

The hardware details belong to the linked profiles. A setup working on one Mac
does not make its settings a requirement for another Mac.

## Choose a runtime

| Runtime | Use it when | Guide |
| --- | --- | --- |
| MLX / mlx-lm | You want an MLX artifact and a repository launcher that selects cached models. | [MLX guide](./mlx/README.md) |
| llama.cpp | Your artifact is GGUF and you want a cache-aware `llama-server` launcher. | [llama.cpp guide](./llama-cpp/README.md) |
| MTPLX | You have a complete artifact with matching native MTP weights. | [MTPLX guide](./mtplx/README.md) |
| oMLX | You want its managed app, model directory, or caching features; this repository currently provides reference notes only. | [oMLX guide](./omlx/README.md) |

No runtime is declared universally best. The artifact format and the runtime
that actually loads it determine the sensible starting path.

## Learn the basics

- [Getting started](./docs/getting-started.md) — the simple path from a Mac to a local server
- [Terminology](./docs/terminology.md) — model, artifact, quantization, GGUF, MLX, context, and MTP
- [Hugging Face and model artifacts](./docs/hugging-face.md) — why repositories and runtime artifacts differ

## Models I have tried

[Local model notes](./local-models/README.md) is a small index of model versions
and artifacts used while developing this repository. It records runtime facts,
not model reviews, scores, or speculative compatibility.

## Hardware and tuning

Hardware profiles are optional starting points for a stated Mac, artifact, and
workload. They are not prerequisites for running a model.

- [MLX profiles and advanced guides](./mlx/docs/README.md)
- [llama.cpp hardware profiles](./llama-cpp/hardware)
- [MTPLX known-working profile](./mtplx/hardware/m4-48gb.md)
- [oMLX reference profile](./omlx/hardware/m4-48gb.md)
- [Optional runtime tuning](./docs/tuning.md)

Tune only after the basic setup works. Change one useful variable at a time and
keep machine-specific conclusions with the relevant profile.

## Scope and philosophy

This repository optimizes for the first successful local-model run. It prefers
native runtime tooling when that tooling is already simple, adds scripts when
they materially reduce setup or repeated-use friction, records models that were
actually tried, and treats tuning as optional follow-up work.

It is not a leaderboard, benchmark archive, model catalog, or cross-runtime
competition. Measurements are useful only when they help choose a practical
configuration for a stated model, machine, and workload.

## Official runtime references

- [mlx-lm](https://github.com/ml-explore/mlx-lm)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [oMLX](https://github.com/jundot/omlx)
- [MTPLX](https://github.com/youssofal/MTPLX)
