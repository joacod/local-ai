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

## Launcher passthrough

Arguments after `--` are appended to `mlx_lm.server`:

```sh
./run-mlx-server.sh \
  --model ORG/MODEL \
  -- --log-level DEBUG
```

This is the escape hatch for current server options that the helper does not
manage directly.
