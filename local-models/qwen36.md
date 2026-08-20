# Qwen 3.6

Operational notes for the Qwen 3.6 35B-A3B artifact used with MLX in this
repository. This is a compatibility note, not a model review or benchmark
comparison.

## Tested setup

- **Runtime:** MLX / `mlx-lm`
- **Artifact:** `mlx-community/Qwen3.6-35B-A3B-4bit-DWQ`
- **Variant:** 4-bit DWQ MLX artifact
- **Hardware:** MacBook Pro `Mac16,5`, M4 Max, 48 GB unified memory
- **Status:** Known working; the matching MLX hardware profile and measurements
  record the successful run.
- **Recorded revision:** `73c707af4243243b18193444467872d20cff9399`

## Recommended starting path

Use the [MLX guide](../mlx/README.md) and its launcher. The tested M4 Max
starting command is documented in the [M4 Max 48 GB profile](../mlx/docs/hardware/m4-max-48gb.md).

## Runtime notes

This is an MLX-specific artifact. The repository does not record this exact
artifact as tested with MTPLX, oMLX, or llama.cpp; use the artifact format
required by those runtimes instead.

## References

- [Qwen 3.6 MLX artifact](https://huggingface.co/mlx-community/Qwen3.6-35B-A3B-4bit-DWQ)
- [MLX runtime guide](../mlx/README.md)
- [M4 Max 48 GB MLX profile](../mlx/docs/hardware/m4-max-48gb.md)
