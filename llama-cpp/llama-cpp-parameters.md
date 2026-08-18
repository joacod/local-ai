# llama.cpp parameters

Use `llama-server --help` from the installed version as the authoritative
reference. These are the parameters used most often in the repository's
starting commands.

| Flag | Purpose |
| --- | --- |
| `-ngl` / `--gpu-layers` | Number of model layers offloaded to the GPU/Metal backend. |
| `-fa` / `--flash-attn` | Flash Attention mode when supported by the installed build. |
| `--cache-type-k` | Representation used for KV-cache keys. |
| `--cache-type-v` | Representation used for KV-cache values. |
| `-b` | Prompt batch size; larger values can improve prefill but use more memory. |
| `-ub` | Upper batch size used during generation. |
| `-c` / `--ctx-size` | Context target in tokens. |
| `-np` / `--parallel` | Number of parallel request slots/streams. |
| `--jinja` | Enables Jinja chat-template processing when the model requires it. |
| `--port` | Listening port; this repository uses `8080`. |
| `--offline` | Prevents network downloads during server startup. |

Common KV-cache values include `q4_0`, `q8_0`, and `f16`. The choice trades
memory, speed, and possible output effects; keep it tied to a measured
workload. See [GGUF, Hugging Face, and tuning](./gguf-and-tuning.md) for the
context and qualification boundary.

The launcher profiles currently use:

- M2 16 GB: `-ngl 99`, Flash Attention, `q8_0` KV cache, `-b 512`, `-ub 512`,
  `-c 16384`, and `--jinja`.
- M4 Max 48 GB: `-ngl 99`, Flash Attention, `q8_0` KV cache, `-b 2048`,
  `-ub 2048`, `-c 131072`, and `--jinja`.

Those are starting/reference values in the llama.cpp hardware documents, not
universal defaults. Use the [runtime tuning guide](../docs/tuning.md) before
promoting a changed value.
