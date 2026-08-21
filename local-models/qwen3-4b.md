# Qwen 3 4B

## Setup I used

- **Artifact:** `mlx-community/Qwen3-4B-Instruct-2507-4bit`
- **Runtime:** MLX / `mlx-lm`
- **Variant:** 4-bit MLX artifact
- **Machine:** base Apple M2 with 16 GB unified memory
- **Experience:** I used this for one interactive coding or tool-using agent
- **Memory note:** prompts up to about 16k tokens were usable in the recorded
  setup; a 32k prompt caused substantial swap and is not a practical starting
  point on this machine.

Use the [MLX README](../mlx/README.md) to install the runtime and start the
server. The [M2 16 GB machine index](./machines/m2-16gb.md) links this
combination with the other setups I've used for that Mac.

## Compatibility

This is an MLX artifact. It is not documented here as tested with llama.cpp,
oMLX, or MTPLX; those runtimes need their own compatible artifacts.

## Links

- [Qwen 3 4B MLX artifact](https://huggingface.co/mlx-community/Qwen3-4B-Instruct-2507-4bit)
- [MLX runtime](../mlx/README.md)
- [M2 16 GB machine index](./machines/m2-16gb.md)
