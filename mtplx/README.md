# MTPLX

[MTPLX](https://github.com/youssofal/MTPLX) is an Apple Silicon runtime and
OpenAI-compatible local server for complete model artifacts with matching native
Multi-Token Prediction (MTP) components.

Use the native CLI directly. This repository does not add a wrapper because
MTPLX already provides model management and startup commands.

## Install or update

```sh
brew install youssofal/mtplx/mtplx
mtplx --version
mtplx help
```

Update an existing Homebrew installation with:

```sh
brew upgrade youssofal/mtplx/mtplx
```

Check cached packs for updates and apply them when needed:

```sh
mtplx models --check
mtplx models --update
```

## Get and inspect a model

The [Qwen 3.8 model note](../local-models/qwen38.md) records the complete
artifact used in this repository:

```sh
MODEL="Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed"
mtplx pull "$MODEL"
mtplx models
mtplx inspect "$MODEL" --json
```

Inspection should accept the complete artifact before you start the server.

## Start and stop the server

```sh
mtplx start
```

The local server uses `http://127.0.0.1:8000/` and its OpenAI-compatible base
URL is `http://127.0.0.1:8000/v1`. Check the health and model endpoints before
sending a request:

```sh
curl -fsS http://127.0.0.1:8000/health
curl -fsS http://127.0.0.1:8000/v1/models
```

Stop it from another terminal with:

```sh
mtplx stop
```

For an API-only foreground server, the native alternative is
`mtplx serve --host 127.0.0.1 --port 8000`.

## Important compatibility note

The target weights and native MTP components must come from the same complete
artifact. An ordinary MLX conversion or an unrelated MTP sidecar is not a
substitute. Start with the runtime defaults; use the [official MTPLX
quickstart](https://github.com/youssofal/MTPLX/blob/main/docs/quickstart.md) for
current options and behavior.
