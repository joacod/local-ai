# MLX hardware qualification guide

This is optional advanced material for profiling a new Apple Silicon machine or
retuning an MLX setup. A model does not need to be qualified before it can be
used.

For a first-time setup, start with the [MLX guide](../../README.md). For an
optional new-machine profile, use this guide; after a package upgrade, use the
[MLX upgrade and benchmark guide](./upgrade-benchmark.md).

## Goal

Create a useful `mlx_lm.server` profile for one machine, model, and workload.
Record the evidence separately when a measured setting is worth keeping.

## 1. Confirm scope

Establish before editing:

- workload: one agent, concurrent clients, or long-context analysis
- exact model repository and quantization
- current server command and flags
- permission for server restarts
- files that should be updated

Do not reuse another machine's preset without measurement.

## 2. Inspect the system

Read the launcher, setup script, parameter reference, and hardware guides. Check `git status` and preserve unrelated changes.

Collect non-sensitive system details:

```sh
sysctl -n hw.model hw.memsize hw.ncpu
system_profiler SPDisplaysDataType
sw_vers
pmset -g batt
pmset -g custom
```

Query the repository environment:

```sh
mlx/venv/bin/python -c 'import mlx.core as mx; print(mx.device_info())'
mlx/venv/bin/python -m mlx --version
mlx/venv/bin/python -m mlx_lm --version
mlx/venv/bin/mlx_lm.server --help
```

Record the chip, CPU/GPU cores, unified memory, macOS version, power mode, MLX working-set recommendation, and official Apple memory bandwidth.

## 3. Inspect the model

Use Hugging Face metadata, `config.json`, and the installed `mlx-lm` model implementation to identify:

- repository size and weight quantization
- architecture and attention types
- layers, KV heads, head dimensions, and cache dtype
- advertised context range
- cache classes and per-token growth

Weight quantization does not determine KV-cache size. Derive a model-specific estimate when possible:

```text
fixed cache state + bytes per token * cached tokens
```

Verify estimates against runtime cache logs when available.

## 4. Start conservatively

Use this baseline unless the workload requires otherwise:

- `--host 127.0.0.1`
- `--decode-concurrency 1`
- `--prompt-concurrency 1`
- upstream default `--prefill-step-size`
- small prompt-cache count and byte budget
- practical default response limit

Leave substantial memory below the MLX working-set recommendation. `--max-tokens` is a response limit, not context size. `--prompt-cache-bytes` is not a hard memory limit or OOM guard.

## 5. Benchmark safely

Do not run `mlx_lm.benchmark` beside a server holding the same large model; it may load a second copy. Either benchmark the HTTP server or stop it before using the CLI benchmark. Ask before stopping a user-started process.

Confirm every restart's flags with `ps`. Record before and after each matrix:

```sh
memory_pressure -Q
vm_stat
pmset -g batt
```

Stop if requests fail, swap grows materially, memory pressure stays unhealthy, or the machine becomes unresponsive.

## 6. HTTP protocol

Use streaming chat completions so TTFT and API token usage are available. Keep the model, prompt pattern, generation limit, sampling, thinking mode, tools, system prompt, and power mode fixed.

Run one warm-up, then at least three cold trials per setting and report the median. Give cold prompts unique prefixes to prevent cache reuse. Use actual API `prompt_tokens`.

Use the shared HTTP measurement client rather than creating an ad hoc harness:

```sh
mlx/venv/bin/python mlx/scripts/benchmark-mlx-server.py \
  --model <repo-or-local-path> \
  --label <profile-name> \
  --targets 2048 8192 16384 32768 \
  --trials 3 \
  --max-tokens 128
```

The measurement client loads tokenizer files from the local Hugging Face cache and does not download them. Cache the complete model and tokenizer before qualification.

The client does not manage the server process. Restart the server yourself between parameter configurations and verify its final flags.

Recommended matrix:

```text
Prompt tokens: 2k, 8k, 16k, 32k
Generation:    128 or 256 tokens
Sampling:      deterministic
```

Measure:

- TTFT: request start to first content token
- total response time
- prompt, completion, and cached tokens
- decode rate: tokens after the first divided by decode time
- process RSS and system memory pressure

`prompt_tokens / TTFT` is only an approximate prefill rate because TTFT includes tokenization and first-token sampling.

## 7. Tune one variable at a time

| Area | Test | Selection rule |
| --- | --- | --- |
| Prefill | Default plus one smaller and larger value, often `1024/2048/4096` | Best balance across required context sizes |
| Cache reuse | Repeat one identical long prompt | Confirm `cached_tokens` and compare cold/cached TTFT |
| Cache budget | Vary entry count and bytes | Preserve useful prefixes with safe headroom |
| Decode concurrency | Compare `1` and `2` for overlapping clients | Aggregate throughput without unacceptable latency or memory |
| Prompt concurrency | Increase only after measuring prefill peaks | Keep lowest value that meets throughput needs |
| Power | Compare battery and AC under the same mode | Prefer AC for sustained gains; keep settings identical |

Keep concurrency at `1/1` for one active agent. Test High Power Mode only for sustained workloads.

Do not change the wired-memory limit unless MLX reports that the model is too large for the available working set. Any limit must remain below total memory.

## 8. Select the profile

Prioritize:

1. stability and memory headroom
2. target-workload latency
3. prompt-cache reuse
4. aggregate throughput

Do not call a setting optimal without measurements. Record untested areas.

## 9. Produce two documents

Create `mlx/docs/hardware/<machine>.md` with current guidance only:

```md
# Machine name
## Hardware
## Recommended command
## Settings
## Model cache size
## Adjustments
## Operating tips
## References
```

Create `mlx/docs/hardware/<machine>-benchmark.md` with evidence:

```md
# Machine benchmarks
## Environment
## Prefill step
## Power
## Throughput
## Prompt cache
## Memory
```

Keep both documents current-only. Do not include superseded settings, package versions, old benchmark tables, upgrade narrative, or documentation history. Compare against previous results in the work report before replacing them; Git history preserves the old documents.

## 10. Align and verify

Update the launcher profile, parameter guide, relevant model guidance, and README links. Keep experimental server arguments available through launcher passthrough.

Run:

```sh
python3 -m py_compile mlx/scripts/benchmark-mlx-server.py
bash -n mlx/run-mlx-server.sh
mlx/venv/bin/mlx_lm.server --help
git diff --check
```

Verify the printed launcher command, local Markdown links, `/health`, final process flags, and that no second model process remains loaded.

Report files changed, measured conclusions, untested areas, and the final launch command.
