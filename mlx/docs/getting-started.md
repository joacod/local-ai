# MLX workflow details

Use the [MLX guide](../README.md) for the shortest install and daily-use path:
`./setup-mlx.sh`, then `./run-mlx-server.sh` or the optional
`run-mlx-server` alias. This page keeps a compact reference for users who need
more detail; it is not a second required setup path.

## Install and update

From the `mlx` directory:

```sh
./setup-mlx.sh
```

The script creates `venv`, installs or upgrades `mlx-lm` and MLX, verifies
`mlx_lm.server`, and prints the resolved versions. PyTorch is not required for
MLX model execution.

To inspect the installed entry point:

```sh
source venv/bin/activate
mlx_lm.server --help
```

Running `./setup-mlx.sh` again is the normal update path. The current versions
can be compared without changing the environment with:

```sh
venv/bin/python -m pip index versions mlx-lm
venv/bin/python -m pip index versions mlx
venv/bin/python -m pip index versions mlx-metal
```

## Select a model

The server accepts an MLX-compatible Hugging Face repository or local model
directory:

```sh
./run-mlx-server.sh --model ORG/MODEL
./run-mlx-server.sh --model ./models/my-local-mlx-model
```

The [MLX model reference](./reference/mlx-models.md) explains compatibility and
cache behavior. The [model selection checklist](./guides/model-selection.md) is
optional research material for a larger or unfamiliar model.

## Daily use

The launcher activates the repository environment, offers cached model choices,
and starts `mlx_lm.server` locally on port `8080`:

```sh
run-mlx-server
run-mlx-server --model ORG/MODEL
```

The optional hardware flags and passthrough syntax are documented in the
[MLX guide](../README.md#daily-use). A manual alternative is:

```sh
source venv/bin/activate
mlx_lm.server --model ORG/MODEL --host 127.0.0.1 --port 8080
```

## Optional profiles and tuning

Use a hardware profile only when its Mac, model, and workload match:

- [M2 16 GB profile](./hardware/m2-16gb.md)
- [M4 Max 48 GB profile](./hardware/m4-max-48gb.md)
- [MLX hardware qualification](./guides/hardware-qualification.md)
- [MLX upgrade checks](./guides/upgrade-benchmark.md)
- [Shared optional tuning guide](../../docs/tuning.md)

These are follow-up references, not prerequisites for the first successful run.

## Troubleshooting

- If the alias is missing, run `source "$HOME/.zshrc"` and check its absolute path.
- If port `8080` is busy, inspect it with `lsof -nP -iTCP:8080 -sTCP:LISTEN`.
- If a model fails to load, confirm the artifact is supported by the installed
  `mlx-lm` and that the Mac has enough memory.
- If memory pressure grows, stop the server before trying longer contexts or
  additional concurrency.

See the [MLX guide](../README.md) for the complete first smoke test and official
references.
