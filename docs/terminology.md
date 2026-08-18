# Terminology

Use these terms consistently when adding notes or profiles.

## Models and artifacts

| Term | Meaning |
| --- | --- |
| **Model family** | A related line of models that share an architecture or release identity. |
| **Model** | A particular released checkpoint or model definition in that family. |
| **Artifact / conversion** | The files prepared for a loader or runtime. A family can have MLX, GGUF, or other runtime-specific artifacts. |
| **Runtime / server** | The runtime is the software that loads and executes an artifact. A server is the running process and API exposed by that runtime. |
| **Hugging Face repository** | A hosted repository containing model files, metadata, revisions, and a model card. It is not automatically a compatible artifact for every runtime. |
| **Revision** | A commit, tag, or other immutable point in a repository. Record it when a result must be reproducible. |
| **Quantization** | A representation that stores model weights with fewer or different numeric values to reduce size and memory use. The exact quantization scheme matters. |

A model name or family name does not identify one interchangeable set of
files. The repository, revision, file layout, tokenizer, chat template, and
runtime support determine what can load.

## Architecture and memory

| Term | Meaning |
| --- | --- |
| **Dense** | A model where the full set of layers/parameters participates for each token. |
| **MoE** | Mixture of Experts: a model with multiple expert pathways where only a subset is active for a token. |
| **Total parameters** | The full parameter count, including inactive experts in an MoE model. It is useful for understanding storage and capacity. |
| **Active parameters** | The parameters used for one token. They are useful for understanding per-token compute, but do not describe the whole artifact's storage requirement. |
| **Context window** | The maximum prompt and conversation span the model can consider for a request, subject to runtime and hardware limits. |
| **KV cache** | Runtime state retained for the attention keys and values of a prompt or generated sequence. It grows with active context and is separate from model weights. |
| **Prompt/session cache** | Reusable state for a prior prompt or session. Each runtime may implement and name this differently; it is not the same as the model download cache. |

Model file size is not peak runtime memory. Runtime memory also includes
allocations, temporary buffers, active and retained cache state, the server,
macOS, and other applications. An advertised context window is not a promise
that the whole range is practical on a particular Mac.

## Workloads and profiles

| Term | Meaning |
| --- | --- |
| **Workload** | The use case and conditions being optimized, such as one interactive coding agent, concurrent clients, or long-context analysis. |
| **Hardware profile** | A named configuration for a runtime + hardware + workload, with the model/model family included when the setting depends on it. |
| **Qualification** | The process of confirming a working runtime/model pair and measuring useful settings on the target machine. |
| **Benchmark** | A measurement run or tool used as evidence during qualification. It is not the repository's purpose. |
| **Cold run** | A run without the relevant reusable prompt/session state. |
| **Warm/cached run** | A run that reuses in-memory or runtime cache state. |
| **Disk-restored run** | A run restored from a runtime's disk-backed cache. Keep it separate from cold and in-memory cached results. |
| **TTFT** | Time to first token: request start until the first generated content token. It can include tokenization, prefill, and first-token work. |
| **Prefill** | Processing the input prompt before generation. |
| **Decode rate** | The rate at which generated tokens are produced after the first token, normally reported in tokens per second. |

A profile is not a universal default. A result from another Mac, model revision,
quantization, cache state, or workload is evidence for that exact scope only.

See [Hugging Face and model artifacts](./hugging-face.md) for repository and
conversion terms, and [Runtime tuning and qualification](./tuning.md) for how
to measure a profile.
