# Qwen 3.8

Operational notes for the Qwen 3.8 artifact used with MTPLX in this repository.
This is a compatibility note, not a model review or benchmark comparison.

## Tested setup

- **Runtime:** MTPLX
- **Artifact:** `Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed`
- **Variant:** 4-bit dynamic-quantized Optimized Speed build with matching native
  MTP weights
- **Hardware:** M4 Max with 48 GB unified memory
- **Status:** Known working; the artifact passed `mtplx inspect` and served
  successfully in the [M4 Max 48 GB profile](../mtplx/hardware/m4-48gb.md).
- **Recorded revision:** `57c0ede09cec77a02ff05f19cea5d81df7a20da6`

## Recommended starting path

Use the [MTPLX guide](../mtplx/README.md). It contains the Homebrew install,
`mtplx pull`, inspection, native startup, and API smoke test.

## Runtime notes

This is a complete MTPLX-specific artifact. Its target weights and native MTP
components must stay together; an ordinary MLX conversion is not a substitute.
The repository does not record this exact artifact as tested with MLX, oMLX, or
llama.cpp.

## References

- [Qwen 3.8 MTPLX artifact](https://huggingface.co/Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed)
- [MTPLX runtime guide](../mtplx/README.md)
- [M4 Max 48 GB MTPLX profile](../mtplx/hardware/m4-48gb.md)
