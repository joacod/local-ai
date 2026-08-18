# Repository guidance

## Purpose and boundaries

This repository is a practical toolkit and notebook for running open-source
language models locally on Apple Silicon and finding useful configuration for a
specific model, runtime, machine, and workload. Models, runtimes, and Macs
will change; the structure should support additive notes without repeated
reorganization. The maintained runtime scope is MLX, `llama.cpp`, oMLX, and
MTPLX.

It is not a model-quality leaderboard, cross-runtime benchmark project,
benchmark archive, model research repository, or collection of speculative
best settings.

## Information ownership

Use one canonical location for each fact:

```text
README.md              repository entry point and short navigation
docs/                  concepts shared across runtimes
local-models/          model/family and artifact-specific operational facts
<runtime>/             runtime installation, commands, flags, and behavior
<runtime>/hardware/    machine-specific starting and measured profiles
```

- Put cross-runtime terminology, Hugging Face and artifact concepts, generic
  qualification methodology, benchmark terminology, and general tuning
  principles in `docs/`. Keep runtime-specific flags out of it.
- Put artifact requirements, tokenizer or chat-template requirements, MTP
  heads, compatibility caveats, runtime artifact variants, and model-specific
  serving behavior in `local-models/`. Do not put generic tuning methodology
  or machine-specific performance recommendations there.
- Put installation, updates, model loading, cache semantics, server commands,
  smoke tests, launchers, troubleshooting, and qualification tools in the
  runtime directory that requires them. Follow an existing runtime's profile
  path rather than moving files just to match this conceptual layout.
- Put machine-specific settings in the relevant runtime hardware profile. A
  useful profile identifies the runtime, hardware, model or artifact, workload,
  configuration, and evidence. A result from one Mac is never silently a
  generic runtime default.

Before adding an explanation, check whether it belongs in `docs/`; check
`local-models/` before adding model facts to a runtime guide; and check a
hardware profile before adding machine values to a model note. Prefer one
canonical explanation plus a link over copied sections.

## Evidence and wording

Keep these classifications distinct:

- **Upstream/default:** documented or selected by the runtime itself.
- **Starting configuration:** a reasonable value to try, clearly labeled as
  unqualified for the stated machine and workload.
- **Measured/qualified profile:** actually tested for a stated machine,
  runtime/version, model/artifact/revision, workload, and relevant
  context/cache/concurrency conditions.

Record defaults, starting recommendations, and machine-tested values
separately. Call a setting measured, qualified, recommended, or optimized only
when the repository has evidence supporting that wording. Never invent values
or imply that a starting configuration was measured.

## Experiment and qualification workflow

Use this lifecycle:

```text
new model/runtime/machine
  → get one configuration working
  → smoke test
  → choose one useful tuning question
  → measure
  → decide whether the result is reusable
  → promote the useful current result
```

Use `docs/tuning.md` as the canonical generic methodology. Before tuning,
confirm that the model loads, the health endpoint works, the model list returns
the expected model, and one small inference or chat request completes. Also
check disk and unified-memory headroom.

During qualification, freeze the model/artifact and workload, change one
meaningful variable at a time, distinguish cold, warm, and cache-restored
conditions, and record enough context to reproduce the result. Optimize for
the stated workload, not a universal best result. Raw exploratory output stays
outside the repository unless a compact result is needed to justify the
current profile. Keep checked-in profiles and tuning reports latest-only; Git
history is the historical archive.

## Preserve working runtime workflows

Treat established automation conservatively. In particular, MLX and
`llama.cpp` have working launcher workflows: do not rewrite, rename, or
simplify them as documentation cleanup. Preserve public commands and flags
unless changing them for a documented, concrete reason. Newer runtimes such as
oMLX and MTPLX do not need artificial symmetry with MLX. Do not create
launchers, benchmark frameworks, profiles, or abstractions solely to make
runtime directories look alike; add them when actual use justifies them.

## Adding models, machines, and runtimes

- **Model:** determine the required runtime and artifact; add a
  `local-models/` note only for useful model-specific facts. Put runtime
  commands, including exact model names, revisions, and quantizations when
  needed, in runtime documentation; put measured machine settings in a
  hardware profile. Keep temporary experiments outside permanent notes, keep
  the root README model-agnostic, and do not add model reviews or rankings.
- **Machine:** do not replace existing profiles just because the maintainer
  changed Macs. Qualify the runtime, model, and workload on the new machine,
  add or update an appropriately named profile, and add a launcher preset only
  when repeated use makes it worthwhile. Never copy another machine's values
  and label them optimized without measurement. Changing Macs should not
  require changes to generic model documentation.
- **Runtime:** normally start with `<runtime>/README.md`. Add scripts, profiles,
  deeper guides, or qualification tooling only when actual use demonstrates
  their value. Prefer consistent concepts over identical directory trees.

## Change discipline and version-sensitive claims

Verify installation commands, flags, supported formats, context limits, cache
behavior, compatibility, and new features against official upstream
documentation or source when practical. Do not replace a known-working
repository command from memory; document intentional differences from upstream.

Prefer focused changes: do not opportunistically reorganize documentation,
rename directories, rewrite mature guides, refactor working scripts, or create
abstractions for hypothetical needs. Learn something, put it in the smallest
canonical location, and link to it instead of duplicating it.

## Safety and local resources

- Documentation-only work must not run setup commands, install packages,
  download models, start servers, or run benchmark workloads.
- Bind local servers to localhost by default. MLX and `llama.cpp` use port
  `8080`; oMLX and MTPLX use `8000`. Run only one large backend at a time when
  ports or resources conflict.
- Check disk and unified-memory headroom before inference experiments, and
  stop when memory pressure or swap makes the machine unstable.
- Keep virtual environments, model caches, Python bytecode, logs, secrets,
  private keys, and generated artifacts untracked.

## Validation

For documentation changes, check relative Markdown links and stale references
after moving or deleting files, then run:

```sh
git diff --check
```

For changed scripts use `bash -n <script>` for shell and
`python3 -m py_compile <file>` for Python. Use additional focused static checks
when appropriate. Do not run a model server merely to validate documentation.
