# Runtime tuning and hardware qualification

Use this guide **after one runtime and one model artifact already work**. The
goal is to choose useful settings for that runtime on one Apple Silicon machine
and workload—not to rank models or compare engines.

## Preconditions

Before tuning, confirm:

- the runtime starts without a load error;
- `/health` or the runtime's equivalent is healthy;
- `/v1/models` returns the model ID you will use; and
- one small chat request completes.

Also confirm that the Mac has enough disk and unified-memory headroom. Fix
installation, artifact, or server problems before changing tuning parameters.

## Freeze the baseline

Record:

- Mac model, chip, unified memory, macOS version, power, and thermal state;
- runtime version and exact server command;
- model repository, revision, file size, quantization, tokenizer, and API model
  ID;
- context target, response cap, sampling, thinking policy, and prompt;
- cache state and active request count; and
- one tuning objective, such as interactive latency, long-context headroom,
  cache reuse, or concurrent throughput.

Keep the artifact and prompt construction fixed while changing server settings.
Cold, in-memory cached, and disk-restored runs are separate conditions.

## Choose one question

| Objective | Change one variable | Record |
| --- | --- | --- |
| Memory headroom | Context target or KV-cache setting | Peak memory, pressure, swap, errors |
| Interactive latency | Prefill, batch, or concurrency setting | TTFT, prefill rate, decode rate |
| Prefix reuse | Cache mode or cache budget | Cold versus cached TTFT and cache hits |
| Concurrent throughput | Active request count | Per-request latency and aggregate rate |
| Speculative decoding | MTP/AR mode or draft depth | Decode rate, acceptance, and memory |
| Artifact fit | One supported quantization | Load success and available context headroom |

Do not change context, cache, concurrency, and quantization in the same run.

## Measure safely

1. Start one server with the baseline configuration.
2. Check health and the returned model ID.
3. Run one warm-up request.
4. Run at least three measured requests with the same prompt and response
   policy when the runtime supports repeated trials.
5. Change one parameter and repeat the same procedure.
6. Stop if requests fail, memory pressure or swap grows materially, or the
   machine becomes unstable.

Use unique prompts for cold rows and the runtime's documented cache reset or
bypass procedure. Never call a warm result cold. Keep raw output outside the
repository unless a current profile needs a compact result.

Useful measurements include TTFT, total request time, prefill/decode tokens per
second, prompt/completion/cached token counts, peak process/system memory,
memory pressure, swap, timeouts, load failures, and runtime-specific metrics.

A **benchmark** is the measurement tool or run. **Qualification** is the
larger decision process: confirm the server works, measure the fixed
runtime/model/machine/workload, select useful settings, and record the result.

## Runtime entry points

Use the runtime's own guide for commands and flags:

- [MLX qualification](../mlx/docs/guides/hardware-qualification.md) and the
  [HTTP measurement client](../mlx/scripts/benchmark-mlx-server.py)
- [`llama.cpp` hardware profiles](../llama-cpp/hardware)
- [oMLX starting profile](../omlx/hardware/m4-48gb.md)
- [MTPLX starting profile](../mtplx/hardware/m4-48gb.md)

MLX currently has the most complete checked-in qualification workflow. oMLX
and MTPLX have starting guidance here and should gain reusable measured
profiles only when their settings have been tested for a stated workload.

## Record and promote a result

Use a compact record such as:

```text
runtime: <runtime/server>
hardware: <Mac and unified memory>
workload: <stated objective>
model: <repository, revision, quantization>
server_flags: <exact command>
context: <value>
cache: cold | hot | disk-restored
concurrency: <value>
trials: <count and warm-up policy>
ttft_s: <value>
prefill_tok_s: <value>
decode_tok_s: <value>
peak_memory: <value>
recommendation: <what to keep and why>
```

Promote a useful result to the current runtime/hardware profile when it is
reusable. Put artifact compatibility, prompt-format, and model-family details
in [`local-models/`](../local-models/README.md). Keep profiles latest-only;
Git history preserves older documents.

## Interpretation boundary

Choose the setting that is useful for the stated workload, not a universal
“best” setting. Do not infer model intelligence or answer quality from an
operational measurement. A result from another Mac, revision, quantization,
cache state, or serving mode is context—not a default for this machine.
