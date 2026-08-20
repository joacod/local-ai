# GGUF and model loading

llama.cpp loads the GGUF model-file format. A model family may have several
GGUF repositories or several `.gguf` files in one repository. They can differ
in quantization, context metadata, chat-template behavior, and file size.

## Hugging Face models

Use `-hf` with a repository and, when needed, an explicit quantization tag:

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF
llama-server -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M
```

A smaller weight file leaves more room for context and other applications. A
larger quantization may preserve more weight precision, but no quantization is
universally best. Check the model card and exact files before downloading.

The first launch may download the artifact into llama.cpp's local cache. The
repository launcher uses `--offline` so a server launch cannot fetch a missing
model unexpectedly:

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M \
  --offline \
  --port 8080
```

Inspect cached models with:

```sh
llama-server --cache-list
```

## Context and memory

`-c` or `--ctx-size` sets the server's context target. Longer contexts generally
need more KV-cache memory. The installed server's `--help` output is
authoritative for cache-type flags and supported values.

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M --ctx-size 4096
```

For shared explanations of context, quantization, and memory, see the
[terminology reference](../../docs/terminology.md) and [practical performance
notes](../../docs/performance.md).
