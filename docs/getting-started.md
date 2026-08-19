# Getting started

This repository helps you run a local language model on Apple Silicon without
having to learn every runtime's flags first. Use this path for the first
working server; use the runtime guide for exact commands.

## Before you start

You need:

- an Apple Silicon Mac;
- enough free disk space for the selected model artifact and its cache;
- enough unified-memory headroom for macOS, the server, context, and your other
  applications; and
- a model artifact supported by the runtime you choose.

If terms such as *artifact*, *quantization*, or *KV cache* are unfamiliar, read
[Terminology](./terminology.md) and [Hugging Face and model artifacts](./hugging-face.md)
first.

## The normal workflow

### 1. Choose a model and runtime

A model family can be published as several runtime-specific artifacts. Confirm
the artifact format and the runtime's compatibility before downloading a large
file. See the [model notes](../local-models/README.md) for repository-specific
operational details.

Choose a runtime guide:

- [MLX](../mlx/README.md)
- [llama.cpp](../llama-cpp/README.md)
- [oMLX](../omlx/README.md)
- [MTPLX](../mtplx/README.md)

Do not choose a runtime because it is universally fastest. Choose the one that
supports the artifact and serving features your workload needs.

### 2. Install the runtime and obtain the artifact

Follow the selected runtime guide's requirements and installation steps. It
should explain how to select or download a compatible artifact and how local
cache behavior works.

### 3. Run a smoke test

Start with one model and one request. Confirm, in this order:

1. the server starts without a model-load error;
2. its health endpoint reports healthy;
3. its model-list endpoint returns the model ID; and
4. a small chat request completes.

The runtime guide documents the endpoint, port, and request shape. Fix load,
health, model-list, or chat failures before changing context, cache,
concurrency, batch, or speculative-decoding settings.

### 4. Use a matching machine profile or qualify the machine

A hardware profile is a measured or explicitly classified starting
configuration for a runtime, machine, workload, and sometimes a model. Use it
only when the hardware and workload match its scope.

If no profile matches, follow [Runtime tuning and qualification](./tuning.md).
The [MLX qualification guide](../mlx/docs/guides/hardware-qualification.md)
contains the repository's most complete example. Other runtimes can add
profiles when their settings have actually been measured; do not create a
profile just to make the directory trees look identical.

### 5. Launch normally

Once the server is healthy and a useful configuration is recorded, use the
runtime's launcher or normal command. Keep the exact model repository, revision,
quantization, runtime version, workload, and selected settings with the profile
or model note.

## When changing models or Macs

- A new model may require a different artifact or chat-template behavior even
  when its family name is familiar.
- A new Mac needs its own qualification unless a profile explicitly covers it.
- A package upgrade can change defaults or memory behavior; requalify the
  affected profile when necessary.
- Keep cold, in-memory cached, and disk-restored runs separate.

For model-specific details, add or update a note in the
[local model notes](../local-models/README.md) rather than changing this shared
workflow.
