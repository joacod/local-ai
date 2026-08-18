# Repository Instructions

## Project
- **Name:** Local AI
- **Goal:** Maintain standalone notes for local language-model inference on Apple Silicon.
- **Scope:** MLX, `llama.cpp`, oMLX, MTPLX, and their shared comparison plan.

## Working rules
- Documentation-only work must not run setup commands, install packages, download models, start servers, or run benchmark workloads.
- Keep hardware profiles and benchmark reports latest-only. Do not append superseded settings or historical result tables.
- Keep virtual environments, model caches, Python bytecode, logs, secrets, and private keys untracked.
- The MLX and `llama.cpp` launchers use local port `8080`; oMLX and MTPLX use `8000`. Run only one large backend at a time on a shared port.
- Use focused static checks such as `git diff --check`, `bash -n`, and `python3 -m py_compile` for documentation and script changes.
