# Runtime Configuration Tuning

Use this guide **after one server and one model already work**. The goal is to choose useful parameters for that runtime on one Apple Silicon machine—for example, a practical context size, cache mode, concurrency, batch setting, or MTP depth.

This is not a cross-runtime comparison plan, model-quality benchmark, leaderboard, or research protocol. A model is a fixed workload here; the result is about operating the selected server.

## Preconditions

Before tuning, confirm:

- the runtime starts without load errors;
- `/health` or the runtime's equivalent is healthy;
- `/v1/models` returns the model ID you will use;
- one small chat request completes successfully;
- the Mac has enough free unified memory and disk space for the run.

If any of these checks fail, fix the installation, model, or server first. Do not tune flags around a broken baseline.

## Freeze the baseline

Record these values before changing a parameter:

- Mac model, chip, unified memory, macOS version, power, and thermal state;
- runtime version and exact server command;
- model repository, revision, file size, quantization, tokenizer, and API model ID;
- context target, response cap, sampling, thinking policy, and prompt;
- cache state, active request count, and any fan or scheduler mode;
- the tuning objective, such as single-agent latency, long-context headroom, cache reuse, or throughput.

Keep the model and prompt fixed while tuning server parameters. Cold, warm, and SSD-restored runs are separate conditions.

## Choose one tuning question

| Objective | Change one variable | Keep fixed | Record |
| --- | --- | --- | --- |
| Memory headroom | Context target or KV-cache setting | Model, prompt, concurrency | Peak memory, pressure, swap, errors |
| Interactive latency | Concurrency, prefill, or batch setting | One request, prompt, response cap | TTFT, prefill rate, decode rate |
| Prefix reuse | Cache off/on or cache tier | Prompt prefix and generation policy | Cold versus cached TTFT, cache hits |
| Concurrent throughput | Active request count | Model, prompt set, context, thermal state | Per-request latency and aggregate rate |
| MTP operation | MTP/AR mode or draft depth | Model, prompt, sampling, response cap | Decode rate, acceptance by depth, memory |
| Quantization fit | One supported quantization | Workload and runtime | Load success, memory, context headroom |

Do not change context, cache, concurrency, and quantization in the same run. You will not know which setting caused the result.

## Run a focused benchmark

Use a deterministic request and a small number of repeated trials:

1. Start the server with the baseline configuration.
2. Check health and the returned model ID.
3. Run one warm-up request.
4. Run at least three measured requests with the same prompt and response policy.
5. Change one parameter and repeat the same procedure.
6. Stop when memory pressure, swap, thermal throttling, timeouts, or load failures make the configuration unsuitable.

Use unique prompts for cold rows. Clear or bypass the runtime cache with the runtime's documented procedure; never call a warm result cold. Keep raw JSON, logs, and dashboard exports outside the repository unless a current report explicitly needs them.

Useful measurements include:

- TTFT and total request time;
- prefill and decode tokens per second;
- prompt, completion, and cached-token counts;
- peak process/system memory and memory pressure or swap;
- context rejections, timeouts, load failures, and other errors;
- runtime-specific values such as MLX cache counters, oMLX PP/TG and cache state, or MTPLX `mtplx_stats` and MTP acceptance.

## Runtime entry points

Use the runtime's own guide and machine profile for the actual command:

- [MLX qualification guide](./mlx/docs/guides/hardware-qualification.md) and [HTTP benchmark client](./mlx/scripts/benchmark-mlx-server.py)
- [oMLX M4 48 GB profile](./omlx/hardware/m4-48gb.md)
- [MTPLX M4 48 GB profile](./mtplx/hardware/m4-48gb.md)
- [`llama.cpp` hardware profiles](./llama-cpp/hardware)

These are separate ways to tune each server. They are not instructions to run every runtime against the same model.

## Record and promote the result

Use a compact record such as:

```text
runtime: omlx
hardware: MacBook Pro M4, 48 GB
objective: single-agent latency
model: <repository and resolved revision>
server_flags: <exact command>
context: <value>
cache: cold | hot | ssd-restored
concurrency: <value>
trials: <count and warm-up policy>
ttft_s: <value>
prefill_tok_s: <value>
decode_tok_s: <value>
peak_memory: <value>
recommendation: <what to keep and why>
```

After a configuration is useful, update the current runtime/hardware profile with the recommendation. Put model-specific compatibility or prompt-format details in [`local-models/`](./local-models). Do not append superseded tables; Git history preserves older experiments.

## Interpretation boundary

Choose the setting that is useful for the stated workload, not a universal “best” setting. A result from another Mac, model revision, quantization, or runtime mode is context—not a default for this machine.

Do not infer model intelligence, answer quality, or general superiority from this operational measurement. Those questions are outside this repository's scope.
