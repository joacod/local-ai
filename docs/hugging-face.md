# Hugging Face and model artifacts

Hugging Face is a publishing and hosting service used by model authors,
organizations, and community converters. It is useful for finding files and
metadata, but a repository name alone does not tell you whether an artifact
works with the runtime you plan to use.

## What a repository contains

- **Organization or publisher:** the account that publishes and maintains the
  repository. The original model author and a converter may publish different
  repositories for the same family.
- **Model repository:** a versioned collection of weights, configuration,
  tokenizer files, chat-template metadata, documentation, and sometimes custom
  code.
- **Revision:** a commit or tag selecting one exact state of the repository.
  Pin it when recording a reproducible result.
- **Files:** the actual artifact pieces. Inspect their names, sizes, formats,
  and metadata rather than relying only on the repository title.
- **Model card:** the publisher's usage, compatibility, license, and conversion
  notes. Treat it as an important source, not as proof that every runtime can
  load the files.

A model family may look like this:

```text
model family
  ├─ original publisher checkpoint
  ├─ MLX conversion
  ├─ GGUF conversion
  └─ runtime-specialized checkpoint
```

These are related artifacts, not interchangeable downloads. A conversion may
change the file format, quantization, tokenizer packaging, chat template, or
runtime requirements. Check the target runtime's guide before downloading.

## What to check before using an artifact

1. Identify the exact repository and revision.
2. Confirm that the artifact format is supported by the selected runtime.
3. Check the architecture, modality, tokenizer, and chat-template requirements.
4. Record the quantization and actual file sizes.
5. Leave memory and disk headroom for the runtime, context/cache state, macOS,
   and other applications.
6. Read the model card and current runtime documentation for compatibility
   caveats.

The [local model notes](../local-models/README.md) are the place for
repository-specific compatibility facts once you have verified them.

## Cache behavior

A runtime may download an artifact into a local Hugging Face cache or manage it
through its own model directory. A cache is a local copy of repository files;
it is not a new model format and it does not guarantee that a different runtime
can reuse the copy. Cache location, offline behavior, and removal commands are
runtime-specific, so use the relevant guide:

- [MLX model selection and compatibility](../mlx/docs/reference/mlx-models.md)
- [llama.cpp GGUF and model loading](../llama-cpp/gguf-and-tuning.md)
- [oMLX](../omlx/README.md)
- [MTPLX](../mtplx/README.md)

For the conceptual difference between model weights, context, and cache state,
see [Terminology](./terminology.md).
