# MLX documentation

Use the [MLX guide](../README.md) for the fastest working path. These are
optional details for model compatibility, profiles, and later tuning.

## Start here

| Document | Use it for |
| --- | --- |
| [Workflow details](./getting-started.md) | Installation notes, daily use, upgrades, and advanced links |

## Guides

| Document | Use it for |
| --- | --- |
| [Model selection](./guides/model-selection.md) | Compatibility and memory checks before choosing a larger model |
| [Hardware qualification](./guides/hardware-qualification.md) | Optional measurement of a new Mac, model, or workload |
| [Upgrade and benchmark](./guides/upgrade-benchmark.md) | Optional recheck after MLX package upgrades |

## Reference

| Document | Use it for |
| --- | --- |
| [mlx-lm parameters](./reference/mlx-parameters.md) | Server flags, request fields, cache behavior, and launcher passthrough |
| [MLX models and compatibility](./reference/mlx-models.md) | MLX artifact compatibility, conversion boundaries, and Hugging Face cache behavior |

## Hardware profiles

| Document | Use it for |
| --- | --- |
| [M2 16 GB](./hardware/m2-16gb.md) | Known working settings for one base M2 and one model |
| [M2 16 GB measurements](./hardware/m2-16gb-benchmark.md) | Evidence behind that profile |
| [M4 Max 48 GB](./hardware/m4-max-48gb.md) | Known working settings for one M4 Max and one model |
| [M4 Max 48 GB measurements](./hardware/m4-max-48gb-benchmark.md) | Evidence behind that profile |

## Tools

| Tool | Use it for |
| --- | --- |
| [HTTP measurement client](../scripts/benchmark-mlx-server.py) | Advanced HTTP measurements for a running server |

Return to the [MLX README](../README.md) for installation and launcher commands.
