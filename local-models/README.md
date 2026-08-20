# Models I've used

This is a small collection of model artifacts that were actually used while
developing this repository. It is not a catalog, review site, leaderboard, or
cross-runtime comparison.

Each note records the exact artifact, runtime, machine, memory, experience, and
compatibility caveats that are useful before a first run. Untested runtime/model
combinations are not listed.

## Model notes

| Model | Runtime used | Start here |
| --- | --- | --- |
| [Qwen 3 4B](./qwen3-4b.md) | MLX | A small known-used artifact for a 16 GB M2 Mac |
| [Qwen 3.6 35B-A3B](./qwen36.md) | MLX | A known-used artifact for an M4 Max with 48 GB |
| [Qwen 3.8 27B](./qwen38.md) | MTPLX | A complete native-MTP artifact used on an M4 Max with 48 GB |

## Machine indexes

- [Apple M2 with 16 GB](./machines/m2-16gb.md)
- [Apple M4 Max with 48 GB](./machines/m4-max-48gb.md)

A machine page is a reverse index, not duplicated runtime settings. Follow the
linked model note for artifact details and the linked runtime README for commands.
