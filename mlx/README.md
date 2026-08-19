# MLX

Run MLX-compatible language models on Apple Silicon with
[mlx-lm](https://github.com/ml-explore/mlx-lm).

MLX is a good fit when you want MLX-native model conversions, an
OpenAI-compatible local API, and a launcher that can apply a measured machine
profile without hiding the underlying server options.

## Requirements

- Apple Silicon Mac
- macOS and Python 3 available as `python3`
- Git and enough disk space for the selected model artifact
- unified-memory headroom for the model, context/cache, macOS, and other apps

For shared model and memory terminology, read the repository's
[getting-started](../docs/getting-started.md), [terminology](../docs/terminology.md),
and [Hugging Face artifact](../docs/hugging-face.md) notes.

## Install or update

From this directory:

```sh
./setup-mlx.sh
```

The script creates `mlx/venv`, installs or upgrades `mlx-lm`, `mlx`, and
`mlx-metal`, verifies `mlx_lm.server`, and prints the resolved versions. Model
execution uses MLX; PyTorch is not required.

## Verify the installation

```sh
source venv/bin/activate
mlx_lm.server --help
```

If this command works, the server entry point is installed.

## Select an MLX model

Pass an MLX-compatible Hugging Face repository or local model directory to
`--model`:

```sh
mlx_lm.server --model mlx-community/Qwen3-1.7B-4bit
```

MLX conversions are commonly published by
[mlx-community](https://huggingface.co/mlx-community), but an `mlx` label does
not guarantee support from the installed `mlx-lm`. Check the
[MLX model compatibility reference](./docs/reference/mlx-models.md) and the
[model-selection research brief](./docs/guides/model-selection.md) before a
large download. Runtime-specific artifact concepts are also covered in the
[shared Hugging Face notes](../docs/hugging-face.md).

The first run downloads from Hugging Face. Later runs reuse the local cache.

## First smoke test

Start the small public smoke-test model through the launcher:

```sh
./run-mlx-server.sh --model mlx-community/Qwen3-1.7B-4bit
```

Leave it running and verify it from another terminal:

```sh
curl http://127.0.0.1:8080/health
curl http://127.0.0.1:8080/v1/models
curl http://127.0.0.1:8080/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{
    "messages": [{"role": "user", "content": "Reply with: MLX is ready."}],
    "temperature": 0,
    "max_tokens": 32,
    "stream": false
  }'
```

Stop the server with `Control-C`. `mlx_lm.server` exposes an API, not a browser
chat UI.

## Daily launcher

The launcher activates `mlx/venv`, lets you choose a cached model from an
interactive menu, accepts a Hugging Face repository or local path, and starts
`mlx_lm.server` on `127.0.0.1:8080`.

For a convenient `zsh` alias, add this line while inside this directory:

```sh
printf "\nalias run-mlx-server='%s/run-mlx-server.sh'\n" "$PWD" >> "$HOME/.zshrc"
source "$HOME/.zshrc"
```

Then use:

```sh
run-mlx-server
run-mlx-server --model ORG/MODEL
run-mlx-server --model ./models/my-local-mlx-model
```

The launcher preserves these existing options:

```sh
run-mlx-server --m2-16gb
run-mlx-server --m4-48gb --model ORG/MODEL
run-mlx-server --model ORG/MODEL -- --log-level DEBUG
```

- `--model` skips the menu and accepts a Hugging Face repository or local path.
- `--m2-16gb` applies the measured base-M2 16 GB single-agent settings and
  warns when the current Mac does not match that hardware.
- `--m4-48gb` applies the measured M4 Max 48 GB single-agent settings and warns
  when the current Mac does not match that hardware.
- `--` passes remaining options to `mlx_lm.server`; later scalar options can
  override earlier launcher values.

Use only the profile that matches the current hardware and workload. The
launcher always binds locally and uses port `8080` unless a passthrough option
changes it.

## Profiles and qualification

| Profile or guide | What it contains |
| --- | --- |
| [M2 16 GB](./docs/hardware/m2-16gb.md) | Measured single-agent settings for one base M2 and one qualified model |
| [M4 Max 48 GB](./docs/hardware/m4-max-48gb.md) | Measured single-agent settings for one M4 Max and one qualified model |
| [Hardware qualification](./docs/guides/hardware-qualification.md) | How to measure a new machine, model, or workload |
| [Upgrade qualification](./docs/guides/upgrade-benchmark.md) | How to requalify after MLX package changes |

The profiles are reference results, not universal defaults. Qualification first
confirms that the fixed model works, then measures one server setting at a time
and records the current recommendation. The existing
[HTTP benchmark client](./scripts/benchmark-mlx-server.py) measures a
running server through HTTP; it does not start or stop the server.

## Runtime-specific references

- [MLX model compatibility and cache behavior](./docs/reference/mlx-models.md)
- [Server parameters and launcher passthrough](./docs/reference/mlx-parameters.md)
- [Model selection research brief](./docs/guides/model-selection.md)
- [MLX documentation index](./docs/README.md)

## Local cache

The Hugging Face cache usually lives at `~/.cache/huggingface/hub/`; `HF_HUB_CACHE`
or `HF_HOME` can change it. To inspect or remove a cached model, use the cache
location and model-specific directory documented by the runtime. Removing a
cache directory deletes local files; it does not change the remote repository.

## Troubleshooting

- **Alias not found:** run `source "$HOME/.zshrc"` and check the alias's absolute
  path.
- **Port 8080 is busy:** inspect it with
  `lsof -nP -iTCP:8080 -sTCP:LISTEN` before stopping anything.
- **Model does not load:** verify that the artifact is MLX-compatible with the
  installed `mlx-lm` and that enough memory is available.
- **Memory pressure grows:** stop the server, reduce the model or workload,
  close memory-heavy applications, and use only a profile qualified for this
  Mac.

## Official references

- [mlx-lm](https://github.com/ml-explore/mlx-lm)
- [mlx-lm HTTP server](https://github.com/ml-explore/mlx-lm/blob/main/mlx_lm/SERVER.md)
- [MLX](https://github.com/ml-explore/mlx)
- [MLX Community models](https://huggingface.co/mlx-community)
