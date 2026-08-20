# Local AI

Practical notes and small helpers for running open-source language models
locally on Apple Silicon. This repository is for technical developers who want
to learn by installing a runtime, starting a server, and trying a model.

## Choose a runtime

- [MLX / mlx-lm](./mlx/README.md) — use MLX model artifacts with a small helper
  for setup and cached-model selection.
- [llama.cpp](./llama-cpp/README.md) — use GGUF artifacts with the native
  `llama-server` and a cache-aware launcher.
- [oMLX](./omlx/README.md) — try a managed MLX server with a model directory and
  dashboard.
- [MTPLX](./mtplx/README.md) — serve complete artifacts that include matching
  native MTP components.

The maintained platform scope is Apple Silicon. The repository is a starting
path, not a benchmark suite, model leaderboard, or exhaustive model catalog.

## Learn the basics

Start with the [shared documentation](./docs/), especially the
[getting-started guide](./docs/getting-started.md) and
[terminology reference](./docs/terminology.md), if local-model terms are new.

## Find models I have used

The [local model notes](./local-models/) contain exact artifacts and runtimes
that were actually used here, with short machine indexes alongside them. They
are practical starting points, not rankings or universal recommendations.
