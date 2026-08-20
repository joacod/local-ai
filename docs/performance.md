# Practical performance

You only need a few rules before trying a model:

- Larger models generally need more disk space and unified memory.
- Longer contexts use more memory because the runtime keeps more conversation
  state, including the KV cache.
- Quantization reduces the memory needed for model weights, with a trade-off in
  numeric precision and sometimes output quality.
- Performance depends on the model, artifact, runtime, Mac, workload, and other
  applications using the machine.
- An advertised context length is a limit, not a promise that the whole range is
  practical on every Mac.

Leave headroom for macOS, the server, context state, and your development tools.
Start with a smaller artifact when in doubt. Change advanced settings only after
the basic server and a short request work.
