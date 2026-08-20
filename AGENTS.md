# Repository guidance

## Purpose

This repository helps a technical developer learn local AI by doing the useful
first thing: choose a runtime, obtain a compatible model artifact, start a
local server, and experiment with it on Apple Silicon.

It is not a benchmark project, model leaderboard, runtime comparison, hardware
benchmark archive, exhaustive model catalog, or research project for finding
universal server settings.

## Information ownership

Keep one canonical home for each fact:

```text
README.md                    short entry point and navigation
docs/                        beginner concepts shared across runtimes
local-models/                artifacts and models actually used here
local-models/machines/       short reverse indexes for setups I've used
<runtime>/                   install, model, server, and runtime behavior
<runtime>/docs/              advanced details that belong to one runtime
```

Before adding an explanation, check the shared docs and local model notes first.
Prefer a short canonical explanation plus a link over copied sections.

## Beginner-first documentation

- Keep the root README short and make every runtime README a direct path from
  install to model to server.
- Use progressive disclosure. Put unfamiliar terminology in `docs/`, exact
  artifact facts in `local-models/`, and advanced flags or troubleshooting in a
  runtime's `docs/` directory.
- Write for developers who may be new to local models. Prefer practical
  explanations and copy/paste commands over exhaustive reference material.
- Keep the four maintained runtimes visible: MLX / `mlx-lm`, llama.cpp, oMLX,
  and MTPLX.

## Evidence and recommendations

- Distinguish upstream behavior, a starting configuration, and a result from a
  setup I've used. Do not call a setting tested, recommended, or optimized without
  evidence for the stated model, artifact, runtime, machine, and workload.
- Do not fabricate model recommendations or document runtime/model combinations
  that were not used here. Omit them or label limited reference information
  clearly; omission is preferred for speculative combinations.
- Keep useful conclusions such as "this artifact worked on this Mac" without
  preserving large measurement tables or experiment transcripts.

## Scope boundaries

- Do not add benchmark infrastructure, benchmark reports, qualification
  workflows, tuning campaigns, exhaustive metrics, or cross-runtime comparison
  tables.
- Do not create hardware profiles merely because a new machine exists. Add a
  machine index only when it can link to a concrete setup I've used.
- Do not add wrappers around already-friendly native CLIs. MTPLX and oMLX should
  use their upstream workflows directly.
- Preserve the useful MLX and llama.cpp launchers, but keep them thin and
  beginner-friendly. Hardware-specific presets do not belong in the basic
  path. An explicit, opt-in preset may remain when the maintainer uses it
  repeatedly and its parameters are backed by a documented machine/model
  result; warn on hardware mismatches and never present it as a universal
  default. Concrete machine/model facts still belong in `local-models/`.
- Prefer deleting obsolete complexity over preserving it indefinitely. Git
  history is the archive.

## Adding content

When adding a model, record the exact artifact, variant or quantization, runtime,
machine, memory, experience, compatibility caveats, and source link only when
those facts are known from actual use. Add a machine-index row that links to the
model page instead of duplicating configuration blocks.

When adding a runtime, start with its README and native install/model/server
workflow. Add deeper runtime docs only when an actual recurring question makes
them useful. Do not create common launchers, abstractions, or directory trees
just for symmetry.

## Validation

For documentation changes:

- check every relative Markdown link and search for stale paths;
- search for benchmark, qualification, and comparison terminology that no longer
  belongs in the maintained scope;
- run `git diff --check`.

For changed shell scripts, run `bash -n <script>`. Do not install runtimes,
download models, start servers, or run inference merely to validate documentation
or launcher cleanup.
