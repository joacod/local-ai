# Getting started

This repository follows a simple journey:

```text
Apple Silicon Mac -> runtime -> model artifact -> local server -> request
```

Use a runtime guide for exact commands. Use these notes when you want to
understand why the steps are separate.

## Before you start

You need:

- an Apple Silicon Mac;
- free disk space for the artifact and its local cache; and
- enough unified-memory headroom for macOS, the server, context, and other apps.

Do not download a large file until you know its format and the runtime that can
load it. A model family can have separate MLX, GGUF, and runtime-specialized
artifacts. See [Hugging Face and model artifacts](./hugging-face.md) if that is
new to you.

## Choose a starting path

The repository has two model/runtime combinations with documented successful
runs:

| Model | Runtime | Start with |
| --- | --- | --- |
| Qwen 3.6 35B-A3B MLX artifact | MLX | [Qwen 3.6 note](../local-models/qwen36.md) and [MLX guide](../mlx/README.md) |
| Qwen 3.8 27B MTPLX artifact | MTPLX | [Qwen 3.8 note](../local-models/qwen38.md) and [MTPLX guide](../mtplx/README.md) |

For a small, disposable first test, the [MLX guide](../mlx/README.md) uses
`mlx-community/Qwen3-1.7B-4bit`.

If you already have a different artifact, choose the guide that matches its
format:

- [MLX](../mlx/README.md) for MLX model directories;
- [llama.cpp](../llama-cpp/README.md) for GGUF files;
- [MTPLX](../mtplx/README.md) for complete artifacts with native MTP weights; or
- [oMLX](../omlx/README.md) for the current reference path.

## The first successful run

### 1. Install the runtime

Follow the selected runtime guide's quick start. MLX uses the repository's
setup script, llama.cpp uses Homebrew, and MTPLX uses its native Homebrew CLI.

### 2. Get or select the artifact

Use the exact repository and variant recorded in a model note when one exists.
The first download may be large. Later launches normally reuse a runtime cache
or model directory.

### 3. Start the server

Use the runtime's normal launcher or native command. Keep the first run at the
runtime defaults unless the guide identifies a tested hardware starting path.

### 4. Make one small request

Confirm:

1. the server starts without a model-load error;
2. the health endpoint responds;
3. the model-list endpoint returns the served model; and
4. a short chat request completes.

The runtime guide gives the endpoint, port, and request shape. Fix a loading or
request problem before changing context, cache, concurrency, batch, or MTP
settings.

### 5. Use it

Connect a browser UI, coding tool, or OpenAI-compatible client to the local API
described by the runtime guide. Stop the server with the runtime's documented
command when you are finished.

## After the first run

- Use a [local model note](../local-models/README.md) to check exact artifact and
  compatibility details.
- Use a matching [hardware profile](../README.md#hardware-and-tuning) as an
  optional starting point, not as a prerequisite.
- Read [Terminology](./terminology.md) when terms such as quantization, GGUF,
  KV cache, or MTP appear in a guide.
- Use [optional runtime tuning](./tuning.md) only when the working setup needs a
  different memory, latency, context, or concurrency trade-off.

## When changing models or Macs

A different model release may need a different artifact, tokenizer, chat
template, or runtime. A different Mac may need more conservative settings. Keep
those facts with the model note or hardware profile rather than treating one
successful run as a universal default.
