# llama.cpp

Run local GGUF artifacts from the terminal with
[llama.cpp](https://github.com/ggml-org/llama.cpp).

`llama-cli` runs one-off prompts, while `llama-server` exposes a local
OpenAI-compatible API. Choose this runtime when the model you want is
available as GGUF or when you want llama.cpp's Metal and server controls.

For shared model and memory concepts, see the repository's
[getting-started](../docs/getting-started.md), [terminology](../docs/terminology.md),
and [Hugging Face artifact](../docs/hugging-face.md) notes.

## Install

Install the Homebrew package:

```sh
brew install llama.cpp
```

Verify both entry points:

```sh
llama-cli --help
llama-server --help
```

## Select a GGUF artifact

llama.cpp loads GGUF files. Hugging
Face repositories often publish several GGUF quantizations, so select the
exact repository and quant when necessary:

```sh
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M
```

The `-hf <organization>/<repository>[:quant]` form downloads a compatible file
through llama.cpp. Read [GGUF, Hugging Face, and tuning](./gguf-and-tuning.md)
and the shared [Hugging Face notes](../docs/hugging-face.md) before downloading
a larger artifact.

The first `-hf` use downloads to the local llama.cpp/Hugging Face cache. The
launcher below only starts models already present in the llama.cpp cache.

## First smoke test

Run a small one-off prompt before starting a long-lived server:

```sh
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF -p "Reply with: llama.cpp is ready."
```

Then confirm the selected artifact works with the server path in the next
section.

## Daily server launcher

This repository includes `run-llama-server.sh`. For a `zsh` alias, add this
line with the absolute path to your clone:

```sh
alias run-llama-server='/absolute/path/to/local-ai/llama-cpp/run-llama-server.sh'
source ~/.zshrc
```

Start it with:

```sh
run-llama-server
```

The launcher:

- reads downloaded models from `llama-server --cache-list`;
- presents a numbered selection menu;
- starts the selected model with `llama-server -hf ... --offline --port 8080`;
  and
- prevents accidental network downloads during the server launch.

After startup:

- browser UI: `http://127.0.0.1:8080`
- chat API: `http://127.0.0.1:8080/v1/chat/completions`

Use a hardware flag only when it matches the current Mac:

```sh
run-llama-server --m4-48gb
run-llama-server --m2-16gb
```

The flags and their current starting commands are documented in:

| Hardware | Profile |
| --- | --- |
| M4 Max with 48 GB | [M4 Max 48 GB profile](./hardware/m4-48gb.md) |
| base M2 with 16 GB | [base M2 16 GB profile](./hardware/m2-16gb.md) |

These profiles are starting/reference configurations, not universal defaults.
The launcher behavior and flags are intentionally simple; use the shared
[qualification guide](../docs/tuning.md) to measure settings for a new
machine/workload before promoting them.

## Run the server manually

To skip the menu, use a known cached repository and keep offline mode enabled:

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF --offline --port 8080
```

## Runtime references

- [GGUF, Hugging Face, and tuning](./gguf-and-tuning.md)
- [llama.cpp parameters](./llama-cpp-parameters.md)
- [llama.cpp hardware profiles](./hardware)
- [Runtime tuning and qualification](../docs/tuning.md)

## Troubleshooting

- **No cached models:** run `llama-server --cache-list` and download a GGUF
  artifact with `llama-cli -hf ...` first.
- **Model load failure:** check the exact GGUF file, chat-template support, and
  available memory before changing server flags.
- **Port 8080 is busy:** inspect it with
  `lsof -nP -iTCP:8080 -sTCP:LISTEN` before stopping anything.
- **Memory pressure grows:** lower context or batch settings, choose a smaller
  quantization, or qualify a more conservative profile for this Mac.

## Official references

- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Install documentation](https://github.com/ggml-org/llama.cpp/blob/master/docs/install.md)
- [Build documentation](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md)
