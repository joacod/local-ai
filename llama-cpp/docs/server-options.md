# llama.cpp server options

Use `llama-server --help` from the installed release as the authoritative
reference. These are common controls, not a machine-specific preset.

| Option | Purpose |
| --- | --- |
| `-hf` | Download or select a Hugging Face model repository, optionally with a quantization tag |
| `--offline` | Prevent network downloads during launch |
| `-ngl` / `--gpu-layers` | Number of layers offloaded to Metal |
| `-fa` / `--flash-attn` | Flash Attention path when supported |
| `--cache-type-k` | Representation used for KV-cache keys |
| `--cache-type-v` | Representation used for KV-cache values |
| `-b` | Prompt batch size |
| `-ub` | Upper batch size used during generation |
| `-c` / `--ctx-size` | Context target in tokens |
| `-np` / `--parallel` | Number of parallel request slots |
| `--jinja` | Enable Jinja chat-template processing when a model needs it |
| `--host` | Bind address; keep `127.0.0.1` for local use |
| `--port` | Listening port; this repository uses `8080` |

Higher context, batch, or concurrency settings can use more memory. Start with
the native defaults and the exact model artifact before changing them.
