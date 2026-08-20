# Optional runtime tuning

This guide is for later. You do not need a hardware profile or a tuning run to
start a model. First get one artifact loaded, one server healthy, and one short
request completed.

The goal is a useful setting for one runtime, Mac, artifact, and workload. It is
not to rank models or compare runtimes.

## Start with a working baseline

Before changing settings, confirm:

- the server starts without a model-load error;
- `/health` or the runtime equivalent responds;
- `/v1/models` returns the model ID; and
- a small chat request completes.

Also check disk space and unified-memory headroom. Fix installation, artifact,
or server problems before changing context, cache, batch, concurrency, or MTP
settings.

## Change one useful thing

Choose one question that matters to the workload:

| Question | Example variable | Useful observations |
| --- | --- | --- |
| Need more memory headroom? | Context target or KV-cache type | Peak memory, pressure, swap, load errors |
| Need faster interaction? | Prefill step, batch, or concurrency | TTFT, total time, decode rate |
| Reuse the same prefix? | Cache mode, size, or budget | Cold versus cached TTFT and cache hits |
| Need multiple clients? | Active request count | Per-request latency and aggregate throughput |
| Need MTP/speculative serving? | Serving mode or draft depth | Successful mode, acceptance, memory |

Keep the artifact, revision, prompt shape, response limit, sampling, and other
conditions fixed. Do not change context, cache, concurrency, and quantization in
the same experiment.

## Record enough context

Keep a short record of:

- Mac model, unified memory, macOS, power, and thermal state;
- runtime version and exact server command;
- model repository, revision, artifact variant, and API model ID;
- workload, context target, response limit, and active request count; and
- whether the run was cold, in-memory cached, or restored from disk.

Run a warm-up, then repeat the same request when the runtime supports it. Use a
cache reset or unique prompt prefix for a cold condition. Never label a warm
result as cold.

Useful observations include TTFT, total request time, prefill/decode tokens per
second, token counts, peak memory, memory pressure, swap, timeouts, and load
failures. Stop if requests fail, swap grows materially, or the Mac becomes
unstable. Avoid loading a second copy of a large model just to measure it.

## Use existing profiles carefully

Profiles are optional helpers. Use one only when its Mac, artifact, runtime, and
workload match:

- [MLX profiles and advanced guides](../mlx/docs/README.md)
- [llama.cpp starting profiles](../llama-cpp/hardware)
- [oMLX reference profile](../omlx/hardware/m4-48gb.md)
- [MTPLX known-working profile](../mtplx/hardware/m4-48gb.md)

Promote a result to a profile only when it is useful beyond the one exploratory
run. Keep model compatibility facts in the [local model notes](../local-models/README.md)
and machine settings in the runtime profile. Keep checked-in profiles current;
Git history preserves older versions.

## Runtime entry points

Use the runtime's own documentation for commands and flags:

- [MLX hardware qualification](../mlx/docs/guides/hardware-qualification.md)
- [MLX HTTP measurement client](../mlx/scripts/benchmark-mlx-server.py)
- [llama.cpp parameters and profiles](../llama-cpp/gguf-and-tuning.md)
- [oMLX reference guide](../omlx/README.md)
- [MTPLX reference guide](../mtplx/README.md)

Do not call a setting universal or optimal. A result from another Mac, model
revision, quantization, cache state, or serving mode is evidence for that scope
only.
