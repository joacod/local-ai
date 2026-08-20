# Qwen 3.8 27B

## Known-used setup

- **Artifact:** `Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed`
- **Runtime:** MTPLX
- **Variant:** 4-bit dynamic-quantized Optimized Speed build with matching
  native MTP weights
- **Machine:** Apple M4 Max with 48 GB unified memory
- **Experience:** known working for a local OpenAI-compatible server with native
  MTP serving
- **Recorded revision:** `57c0ede09cec77a02ff05f19cea5d81df7a20da6`

Use the [MTPLX README](../mtplx/README.md) to install, pull, inspect, and start
the artifact. The [M4 Max 48 GB machine index](./machines/m4-max-48gb.md) links
this known-used combination.

## Compatibility

This is a complete MTPLX-specific artifact. Its target weights and native MTP
components must stay together; an ordinary MLX conversion is not a substitute.
It is not documented here as tested with MLX, oMLX, or llama.cpp.

## Links

- [Qwen 3.8 MTPLX artifact](https://huggingface.co/Youssofal/Qwen3.8-27B-MTPLX-Optimized-Speed)
- [MTPLX runtime](../mtplx/README.md)
- [M4 Max 48 GB machine index](./machines/m4-max-48gb.md)
