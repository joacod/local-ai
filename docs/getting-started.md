# Getting started

The repository's basic journey is:

```text
Apple Silicon Mac -> runtime -> model artifact -> local server -> request
```

You do not need to understand every model setting before trying the first one.

## Before you start

You need:

- an Apple Silicon Mac;
- enough free disk space for the artifact and its cache; and
- unified-memory headroom for the model, server, context, macOS, and other apps.

A model family can have separate MLX, GGUF, and runtime-specific artifacts. Do
not download a large file until you know which runtime can load it. See
[Hugging Face and model artifacts](./hugging-face.md) if that distinction is new.

## Choose a starting path

Pick the runtime that matches the artifact you want to use:

- [MLX](../mlx/README.md) for an MLX model directory;
- [llama.cpp](../llama-cpp/README.md) for a GGUF file;
- [oMLX](../omlx/README.md) for its managed MLX model directory; or
- [MTPLX](../mtplx/README.md) for a complete artifact with matching native MTP components.

The [local model notes](../local-models/) contain a few exact combinations used
in this repository. They are useful starting points, not a complete catalog.

## The first successful run

1. **Install the runtime.** Follow the selected runtime README.
2. **Get the artifact.** Use the exact repository and variant from a model note
   when one exists. The first download may be large.
3. **Start the server.** Use the runtime's normal command and keep the first run
   at its defaults.
4. **Make one small request.** Confirm that the server starts, its health check
   responds, its model list contains the served model, and a short chat request
   completes. Each runtime README shows the relevant URL and request shape.
5. **Experiment.** Connect a browser UI, coding tool, or OpenAI-compatible client
   to the local API described by the runtime.

Fix an installation, artifact, or load problem before changing context, cache,
concurrency, or other advanced settings.

## After the first run

Read [Terminology](./terminology.md) when an unfamiliar term appears. Use the
[model notes and machine indexes](../local-models/) to find examples from
setups I've used,
and read [practical performance](./performance.md) before choosing a
substantially larger artifact.
