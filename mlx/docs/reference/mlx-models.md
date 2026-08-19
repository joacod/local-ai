# MLX models and compatibility

This reference covers MLX-specific model selection, conversion, and local
cache behavior. For repository, revision, artifact, and memory concepts that
apply to every runtime, see the shared [Hugging Face notes](../../../docs/hugging-face.md)
and [Terminology](../../../docs/terminology.md).

## Choose an MLX artifact

Use a model repository that the installed `mlx-lm` can load. Check:

1. the model architecture and loader support in the installed runtime;
2. the model card's MLX conversion and quantization notes;
3. the tokenizer and chat-template files;
4. whether the artifact is text-only or requires a different package such as
   `mlx-vlm`; and
5. the actual weight-file sizes and available memory on the target Mac.

A repository name, an `mlx` tag, or a working example from an older `mlx-lm`
release is not compatibility proof. Confirm the selected artifact with a small
load and chat request before tuning it.

MLX conversions may use ordinary or mixed quantization. A `4bit` label does not
fully describe the memory behavior of every conversion, and weight
quantization does not quantize the runtime KV cache. Read the exact model card
and record the revision when creating a profile.

## Load a repository or local path

The server accepts a Hugging Face repository or a local MLX model directory:

```sh
mlx_lm.server --model mlx-community/ORG-MODEL
mlx_lm.server --model ./models/my-local-mlx-model
```

The repository's launcher passes `--model` through unchanged. Its interactive
menu lists cached `mlx-community` repositories when it can find them and offers
a custom repository or local path otherwise. Paths beginning with `./` or
`../` are resolved relative to the directory from which the launcher was
invoked.

## Hugging Face cache

On first use, `mlx-lm` downloads a repository into the local Hugging Face
cache. Later runs reuse the files already present there. The usual cache is:

```text
~/.cache/huggingface/hub/
```

`HF_HUB_CACHE` or `HF_HOME` can override this location. The launcher reads the
same environment variables when it builds its cached-model menu.

To remove a cached repository, remove only its matching local directory after
stopping any server that uses it. For example:

```sh
rm -rf ~/.cache/huggingface/hub/models--mlx-community--ORG--MODEL
```

Replace the directory with the exact repository encoding. Cache removal is a
local disk operation; it does not remove the Hugging Face repository.

## MLX conversion boundaries

- MLX model files are not automatically interchangeable with GGUF or
  runtime-specialized checkpoints.
- A vision-language conversion may need `mlx-vlm` and a different serving path;
  the repository's `run-mlx-server` launcher starts the text-only
  `mlx_lm.server`.
- A quantization or architecture supported by one `mlx-lm` version may need a
  newer version. Run `mlx_lm.server --help` and a smoke test after upgrades.
- Keep artifact compatibility notes in the [local model notes](../../../local-models/README.md)
  when they apply to a model family rather than MLX in general.

For current model research, use the [MLX model-selection brief](../guides/model-selection.md).
For runtime flags, use [mlx-lm parameters](./mlx-parameters.md).
