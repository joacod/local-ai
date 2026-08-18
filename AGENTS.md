# Repository Instructions

## Project
- **Name:** Local AI
- **Goal:** Maintain practical notes for running local language-model servers and inference tools on Apple Silicon.
- **Scope:** `MLX`, `llama.cpp`, `oMLX`, `MTPLX`, focused runtime-configuration tuning, and model-specific operational notes.
- **Repository identity:** Keep the root README and repository-wide guidance focused on local inference workflows, not on a particular model, experiment, comparison, or point-in-time result.

## Working rules
- Documentation-only work must not run setup commands, install packages, download models, start servers, or run benchmark workloads.
- Keep the root README model-agnostic. Put model names, revisions, quantizations, temporary experiments, and tuning results in runtime or `local-models/` notes.
- This is not a cross-runtime comparison project, model-quality benchmark, leaderboard, or model research repository.
- Run focused tuning only after a server and model pass their health, model-list, and small chat smoke tests. Tune one runtime configuration at a time for a stated workload.
- Record defaults, recommendations, and machine-tested values separately. Do not present a result from one Mac as universally optimal.
- Keep hardware profiles and tuning reports latest-only. Do not append superseded settings or historical result tables.
- Keep virtual environments, model caches, Python bytecode, logs, secrets, and private keys untracked.
- The MLX and `llama.cpp` launchers use local port `8080`; oMLX and MTPLX use `8000`. Run only one large backend at a time on a shared port.
- Use focused static checks such as `git diff --check`, `bash -n`, and `python3 -m py_compile` for documentation and script changes.
