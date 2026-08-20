# Local model notes

This is a small record of model versions and artifacts actually tried while
developing the repository. It is not a model catalog, review site, leaderboard,
or benchmark archive.

A note should answer only the practical questions needed to run that artifact:
the exact repository or variant, the runtime that loaded it, important
compatibility requirements, the easiest tested starting path, and links to the
runtime guide and any matching hardware profile.

## Models tried

| Model | Tested runtime(s) | Starting path |
| --- | --- | --- |
| [Qwen 3.6 35B-A3B](./qwen36.md) | MLX | [MLX guide](../mlx/README.md) |
| [Qwen 3.8 27B](./qwen38.md) | MTPLX | [MTPLX guide](../mtplx/README.md) |

Untested runtime combinations are not listed as supported. Keep machine-specific
settings in the relevant [hardware profile](../README.md#hardware-and-tuning).
