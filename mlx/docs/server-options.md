# MLX server options

Run `mlx_lm.server --help` from the installed environment for the complete,
version-specific list. The launcher always binds to `127.0.0.1` and port `8080`
unless an option passed after `--` changes them.

## Common options

| Option | Purpose |
| --- | --- |
| `--model` | Hugging Face repository or local model directory |
| `--host` | Bind address; keep `127.0.0.1` for local use |
| `--port` | Listening port |
| `--max-tokens` | Default response limit; it does not set context size |
| `--prompt-cache-size` | Maximum number of reusable in-memory cache entries |
| `--prompt-cache-bytes` | Cache budget in server paths that support it; not a hard memory limit |
| `--decode-concurrency` | Requests decoded together |
| `--prompt-concurrency` | Prompts prefilling together; higher values use more memory |
| `--prefill-step-size` | Tokens per prefill step |
| `--trust-remote-code` | Allow code from a trusted model repository |

Use a setting only when the installed server supports it and the model/workload
needs it. Start with the defaults before changing advanced options.

## Optional M4 Max preset

The launcher keeps an explicit `--m4-48gb` preset for the M4 Max with 48 GB
unified memory. It comes from the measured
`mlx-community/Qwen3.6-35B-A3B-4bit-DWQ` run recorded in this repository:

```sh
./run-mlx-server.sh \
  --m4-48gb \
  --model mlx-community/Qwen3.6-35B-A3B-4bit-DWQ
```

It applies:

```text
--max-tokens 8192
--prompt-cache-size 4
--prompt-cache-bytes 4000000000
--decode-concurrency 1
--prompt-concurrency 1
--prefill-step-size 4096
```

The preset is opt-in, warns on a different Mac, and is a starting point rather
than a universal setting. Arguments passed after `--` are appended last, so
they can override these values when the installed server supports that option.

## Launcher passthrough

Arguments after `--` are appended to `mlx_lm.server`:

```sh
./run-mlx-server.sh \
  --model ORG/MODEL \
  -- --log-level DEBUG
```

This is the escape hatch for current server options that the helper does not
manage directly.
