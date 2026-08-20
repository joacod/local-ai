# llama.cpp

Run local GGUF artifacts with [llama.cpp](https://github.com/ggml-org/llama.cpp).
`llama-server` exposes a local OpenAI-compatible API and the repository helper
selects already-cached models for repeat use.

## Quick start

Install llama.cpp and obtain a small GGUF model:

```sh
brew install llama.cpp
llama-server -hf ggml-org/gemma-3-1b-it-GGUF --port 8080
```

The first `-hf` launch downloads the model if needed. Stop it with
`Control-C`, then use the repository launcher:

```sh
cd llama-cpp
./run-llama-server.sh
```

## Install or update

```sh
brew install llama.cpp
brew upgrade llama.cpp
```

Verify the server:

```sh
llama-server --help
```

## Get or select a GGUF model

llama.cpp loads GGUF files. A repository may contain several quantizations or
files. Use the exact repository and quantization tag that matches your needs:

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M --port 8080
```

Read [GGUF and model loading](./docs/gguf.md) and the shared
[artifact guide](../docs/hugging-face.md) before downloading a larger model.
The server cache can be inspected with:

```sh
llama-server --cache-list
```

## Run the server

The helper lists cached models, asks you to select one, and starts it in offline
mode on `http://127.0.0.1:8080`:

```sh
./run-llama-server.sh
```

For repeated use, an optional `zsh` alias can point to the helper:

```sh
alias run-llama-server='/absolute/path/to/local-ai/llama-cpp/run-llama-server.sh'
source ~/.zshrc
```

The local browser UI is at `http://127.0.0.1:8080` and the OpenAI-compatible
chat API is at `http://127.0.0.1:8080/v1/chat/completions`. To skip the menu:

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M --offline --port 8080
```

## More llama.cpp help

- [GGUF and model loading](./docs/gguf.md)
- [Server options](./docs/server-options.md)
- [Shared terminology](../docs/terminology.md)

Use `llama-server --help` for options supported by the installed release. The
repository helper deliberately does not add hardware-specific presets.
