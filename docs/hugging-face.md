# Hugging Face and model artifacts

Hugging Face hosts model files and the metadata around them. The important
question for this repository is not only "which model is this?" but also:

> Which exact artifact can this runtime load?

## Repository, files, and revisions

A repository can contain:

- weights and configuration files;
- tokenizer and chat-template files;
- documentation and license information; and
- one or more revisions, selected by commit or tag.

The publisher may be the original model author or a community converter. A
converter can publish a separate MLX or GGUF repository for the same model
family.

Inspect the actual file names, formats, sizes, and model card. Record a revision
when a result needs to be reproduced. A repository name or model card example
does not prove that every runtime supports the files.

## Why runtimes need different artifacts

One model family may be published as:

```text
model family
  -> original checkpoint
  -> MLX conversion
  -> GGUF conversion
  -> runtime-specialized artifact
```

These are related but not interchangeable downloads. They may differ in file
format, quantization, tokenizer packaging, chat template, architecture support,
or extra runtime files.

- MLX servers expect an MLX-compatible model directory.
- llama.cpp expects GGUF files.
- MTPLX expects a complete artifact with matching native MTP weights.
- oMLX uses model directories and its own supported MLX loading path.

That is why the same Hugging Face repository cannot necessarily be given to
every runtime. Use the runtime guide and a repository model note before starting
a large download.

## Before downloading

1. Identify the exact repository and, when useful, its revision.
2. Confirm the artifact format and architecture are supported by the runtime.
3. Check tokenizer, chat-template, modality, and any runtime-specific files.
4. Record the quantization or variant and actual file sizes.
5. Leave memory and disk headroom for weights, activations, context/cache state,
   macOS, and other applications.
6. Read the model card and current runtime documentation for caveats.

The [local model notes](../local-models/README.md) record repository-specific
facts after they have been verified in this repository.

## Cache behavior

A runtime may download files into a shared Hugging Face cache or manage them in
its own model directory. A cache is only a local copy of repository files; it is
not a new model format and does not make the files compatible with another
runtime. Cache locations, offline behavior, and removal commands are runtime
specific:

- [MLX model compatibility and cache behavior](../mlx/docs/reference/mlx-models.md)
- [llama.cpp GGUF and model loading](../llama-cpp/gguf-and-tuning.md)
- [oMLX](../omlx/README.md)
- [MTPLX](../mtplx/README.md)

For the difference between model weights, context, and cache state, see
[Terminology](./terminology.md).
