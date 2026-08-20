# MLX models

The installed `mlx-lm` version is the authority for model support. A repository
name or an `mlx` label alone is not compatibility proof.

## Choose an artifact

Before a large download, check:

1. the model architecture and loader support in the installed runtime;
2. the model card's MLX conversion and quantization notes;
3. tokenizer and chat-template files;
4. whether the artifact is text-only or needs another package; and
5. weight-file size against the Mac's available memory.

Weight quantization reduces model-weight memory but does not remove the runtime
KV cache or other allocations. Confirm a new artifact with a small load and
chat request before treating it as a starting point.

## Repository or local path

The server accepts a Hugging Face repository or a local MLX model directory:

```sh
mlx_lm.server --model mlx-community/ORG-MODEL
mlx_lm.server --model ./models/my-local-mlx-model
```

The repository launcher passes `--model` through unchanged. It also lists
cached `mlx-community` repositories and offers a custom repository or local
path. Paths beginning with `./` or `../` are resolved relative to the directory
from which the launcher was invoked.

## Cache

On first use, `mlx-lm` downloads a repository into the local Hugging Face cache.
The usual location is:

```text
~/.cache/huggingface/hub/
```

`HF_HUB_CACHE` or `HF_HOME` can override it. To remove one cached repository,
stop its server and remove only its matching local directory. Cache removal
deletes local files; it does not remove the remote repository.

## Conversion boundaries

- MLX files are not automatically interchangeable with GGUF or
  runtime-specialized checkpoints.
- A vision-language conversion may need `mlx-vlm` and a different serving path;
  this repository's launcher starts the text-only `mlx_lm.server`.
- An artifact supported by one `mlx-lm` release may need a newer release. Run
  `mlx_lm.server --help` and a small request after upgrades.

Keep model-specific compatibility facts in the [local model notes](../../local-models/).
