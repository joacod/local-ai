# MLX

Run MLX-compatible language models on Apple Silicon with
[mlx-lm](https://github.com/ml-explore/mlx-lm). This repository keeps two small
helpers for setup and cached-model selection while leaving server options to
`mlx_lm.server`.

## Quick start

From this directory:

```sh
./setup-mlx.sh
./run-mlx-server.sh --model mlx-community/Qwen3-1.7B-4bit
```

The first launch downloads a small model and starts a local API on
`http://127.0.0.1:8080`. Leave it running while you use another terminal or
client.

## Install or update

```sh
./setup-mlx.sh
```

The script creates `venv` and installs or upgrades `mlx-lm` and MLX. To verify
the entry point without starting a server:

```sh
venv/bin/mlx_lm.server --help
```

## Get or select a model

Pass an MLX-compatible Hugging Face repository or local model directory:

```sh
./run-mlx-server.sh --model ORG/MODEL
./run-mlx-server.sh --model ./models/my-local-mlx-model
```

Without `--model`, the helper lists cached `mlx-community` models and offers a
custom repository or local path. The first repository launch downloads its
files; later launches reuse the local cache.

Read the [MLX model notes](./docs/models.md) and the shared
[artifact guide](../docs/hugging-face.md) before downloading a larger model.

## Run the server

The helper starts `mlx_lm.server` bound to `127.0.0.1:8080`:

```sh
./run-mlx-server.sh --model ORG/MODEL
```

For a direct upstream command:

```sh
source venv/bin/activate
mlx_lm.server --model ORG/MODEL --host 127.0.0.1 --port 8080
```

Check the local API with:

```sh
curl -fsS http://127.0.0.1:8080/health
curl -fsS http://127.0.0.1:8080/v1/models
```

Stop the server with `Control-C`. For repeated use, an optional `zsh` alias
can point directly to the helper:

```sh
printf "\nalias run-mlx-server='%s/run-mlx-server.sh'\n" "$PWD" >> "$HOME/.zshrc"
source "$HOME/.zshrc"
```

## More MLX help

- [Model compatibility and cache behavior](./docs/models.md)
- [Server options and passthrough](./docs/server-options.md)
- [Troubleshooting](./docs/troubleshooting.md)
- [Shared terminology](../docs/terminology.md)
- [Qwen 3.6 model note](../local-models/qwen36.md)

The [official mlx-lm server documentation](https://github.com/ml-explore/mlx-lm/blob/main/mlx_lm/SERVER.md)
is authoritative for current flags and behavior.
