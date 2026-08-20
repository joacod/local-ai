# Hugging Face and model artifacts

Hugging Face hosts model files and the metadata around them. The practical
question is not only "which model is this?" but also:

> Which exact artifact can this runtime load?

## Repositories, files, and revisions

A repository can contain weights, configuration, tokenizer and chat-template
files, documentation, license information, and multiple revisions. The
publisher may be the original model author or a community converter.

Inspect the actual files and model card. Record a revision when a result needs
to be reproduced. A repository name or model-card example does not prove that
every runtime supports its files.

## Why runtimes need different artifacts

A family may be published as:

```text
model family
  -> original checkpoint
  -> MLX conversion
  -> GGUF conversion
  -> runtime-specialized artifact
```

These are related but not interchangeable downloads:

- MLX servers expect an MLX-compatible model directory;
- llama.cpp expects GGUF files;
- MTPLX expects a complete artifact with matching native MTP components; and
- oMLX uses its own supported MLX loading path.

## Before downloading

1. Identify the exact repository and, when useful, its revision.
2. Confirm the format and architecture are supported by the runtime.
3. Check tokenizer, chat-template, modality, and runtime-specific files.
4. Record the quantization or variant and leave disk and memory headroom.
5. Read the model card and the current runtime documentation for caveats.

The [local model notes](../local-models/) record repository-specific facts after
they have been verified in this repository.

## Caches

A runtime may use a shared Hugging Face cache or its own model directory. A
cache is only a local copy of repository files; it does not convert them for
another runtime. Cache locations, offline behavior, and removal commands are
runtime-specific, so use the selected [runtime README](../README.md#choose-a-runtime)
for those details.
