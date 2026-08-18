# GGUF, Hugging Face, and llama.cpp tuning

This guide keeps the llama.cpp-specific parts of model selection and server
configuration. For general repository, revision, conversion, and memory terms,
see [Hugging Face and model artifacts](../docs/hugging-face.md) and
[Terminology](../docs/terminology.md).

## GGUF and `-hf`

`llama.cpp` loads the GGUF format. A Hugging Face model family may have several
GGUF repositories or several `.gguf` files in one repository. They can differ
in quantization, context metadata, chat-template behavior, and file size.

Use `-hf` with a repository and, when needed, an explicit quant:

```sh
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M
```

Check the model card and files before choosing a quantization. A smaller weight
file leaves more room for context and other applications; a larger quant may
preserve more weight precision but is not automatically better for every
workload.

For a one-off prompt:

```sh
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M \
  -p "Explain recursion in simple terms."
```

For a local server using an already downloaded model:

```sh
llama-server -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M \
  --offline --port 8080
```

`--offline` prevents a server launch from downloading a missing file. The
repository launcher uses it by default. Cache discovery and removal commands
can vary with the installed llama.cpp version; confirm the current cache with:

```sh
llama-server --cache-list
```

## Context and KV cache

`-c`/`--ctx-size` sets the context target for the server. It is a runtime
setting, not a property of the model file alone:

```sh
llama-cli -hf ggml-org/gemma-3-1b-it-GGUF:Q4_K_M -c 4096
```

Longer contexts generally require more KV-cache memory. `--cache-type-k` and
`--cache-type-v` select the key/value cache representation when the installed
server supports those options. Lower-memory cache types may increase headroom
but can change the runtime trade-off; measure them for the workload instead of
assuming a universal quality or speed result.

## Common server controls

| Option | Purpose |
| --- | --- |
| `-ngl` / `--gpu-layers` | Choose how many layers to offload to Metal. |
| `-fa` / `--flash-attn` | Enable the installed server's Flash Attention path when supported. |
| `-b` | Prompt batch size; affects prompt processing and memory. |
| `-ub` | Upper batch size used by the server. |
| `-np` / `--parallel` | Number of parallel request slots/streams. |
| `--jinja` | Enable Jinja chat-template handling for models that need it. |
| `--host` | Bind address; keep `127.0.0.1` unless network access is intentional. |
| `--port` | Listening port; this repository uses `8080`. |
| `--offline` | Prevent network model downloads during launch. |

Short and long spellings may vary by llama.cpp release. Treat
`llama-server --help` as authoritative after an upgrade. The repository's
[parameter reference](./llama-cpp-parameters.md) lists the flags used by its
starting profiles.

## Profile and qualification boundary

The repository's M2 and M4 documents contain starting commands for the launcher.
They do not establish a universal context, batch, or cache setting. Before
changing machines or workloads, use the [shared runtime qualification guide](../docs/tuning.md):
confirm health and one chat request, then measure one variable at a time while
recording the exact GGUF, revision, quantization, context, cache type, and
power/thermal conditions.

By default, llama.cpp binds to localhost. Binding `--host 0.0.0.0` can expose
the server to the network; use it only with an intentional, authenticated
boundary.
