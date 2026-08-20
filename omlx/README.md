# oMLX

[oMLX](https://github.com/jundot/omlx) is a managed MLX-compatible server for
Apple Silicon. Its app and CLI provide a model directory, dashboard, and local
OpenAI-compatible API.

This repository currently provides a short reference path only; it does not
have a verified oMLX model setup or a custom wrapper.

## Install or update

With Homebrew:

```sh
brew tap jundot/omlx https://github.com/jundot/omlx
brew install omlx
omlx --help
```

Update it with:

```sh
brew update
brew upgrade omlx
```

## Get a model and start oMLX

Start the managed server:

```sh
omlx start
```

Open `http://127.0.0.1:8000/admin`, configure the model directory, and download
an MLX-compatible model through the dashboard. The upstream app and CLI own
model management.

For a foreground server with an explicit model directory:

```sh
mkdir -p "$HOME/.omlx/models"
omlx serve \
  --model-dir "$HOME/.omlx/models" \
  --host 127.0.0.1 \
  --port 8000
```

## Use the local API

The server exposes its OpenAI-compatible API at:

```text
http://127.0.0.1:8000/v1
```

Check it with:

```sh
curl -fsS http://127.0.0.1:8000/health
curl -fsS http://127.0.0.1:8000/v1/models
```

Use the model ID returned by `/v1/models` for a chat request. Stop a managed
server with:

```sh
omlx stop
```

Keep the server bound to localhost unless you intentionally configure
authentication and a network boundary.

## Compatibility

Use an MLX artifact supported by the installed oMLX release. An artifact made
for another server is not automatically compatible. For current installation
and CLI behavior, read the [official oMLX README](https://github.com/jundot/omlx).
