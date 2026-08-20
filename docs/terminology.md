# Terminology

These are the terms most likely to appear while choosing an artifact or using a
local server. The definitions are deliberately practical rather than academic.

## Models and artifacts

| Term | Meaning |
| --- | --- |
| **Model family** | A related line of models that share an architecture or release identity. |
| **Model** | A released model definition or checkpoint in that family. |
| **Checkpoint** | A saved set of model weights for one release or training state. |
| **Parameters** | The learned numeric values in a model. Parameter count describes model scale; it is not the same as file size or active compute. |
| **Artifact / conversion** | Files prepared for a particular loader or runtime. A family can have MLX, GGUF, or runtime-specific artifacts. |
| **Runtime / inference server** | Software that loads and executes an artifact. The server is the running process and API exposed by that runtime. |
| **Hugging Face repository** | A hosted collection of model files, metadata, revisions, and documentation. It is not automatically compatible with every runtime. |
| **Revision** | A commit, tag, or other immutable repository state. Record it when a run must be reproducible. |
| **Quantization** | A representation that uses fewer or different numeric values for model weights to reduce size and memory use. The exact scheme matters. |
| **GGUF** | A model-file format used by llama.cpp. A GGUF repository can contain several quantizations and files. |
| **MLX** | Apple's framework for Apple Silicon. In this repository, MLX artifacts are normally loaded by `mlx-lm` or an MLX-based server. |

A model or family name does not identify one interchangeable set of files. The
repository, revision, file layout, tokenizer, chat template, and runtime
support determine what can load.

## Architecture and memory

| Term | Meaning |
| --- | --- |
| **Dense** | A model where the full set of layers and parameters participates for each token. |
| **MoE** | Mixture of Experts: several expert pathways exist, but only some are active for a token. |
| **Total parameters** | The full parameter count, including inactive experts in an MoE model. It helps explain storage requirements. |
| **Active parameters** | The parameters used for one token. They help explain compute cost but do not describe the whole artifact's memory requirement. |
| **Context window** | The maximum prompt and conversation span a model can consider, subject to runtime and hardware limits. |
| **KV cache** | Runtime state for attention keys and values. It grows with active context and is separate from model weights. |
| **Prompt/session cache** | Reusable state for a previous prompt or session. It is different from the cache holding downloaded model files. |

Model file size is not peak runtime memory. Runtime memory also includes
allocations, temporary buffers, active and retained cache state, the server,
macOS, and other applications. An advertised context window is not a promise
that the whole range is practical on a particular Mac.

## Generation and serving

| Term | Meaning |
| --- | --- |
| **Prefill** | Processing the input prompt before generation begins. |
| **Decode** | Generating output tokens after the prompt has been processed. |
| **Tokens/sec** | A rate of token processing or generation. Always check whether it means prefill or decode. |
| **TTFT** | Time to first token: request start until the first generated content token. It can include tokenization, prefill, and first-token work. |
| **MTP / speculative decoding** | A serving method where extra predictions are proposed ahead and verified by the target model. MTP uses native prediction heads when the artifact includes them; other runtimes may use a separate draft model. |
| **API model ID** | The identifier returned by the server's model-list endpoint. It may differ from the Hugging Face repository name. |

## Profiles and optional tuning

| Term | Meaning |
| --- | --- |
| **Workload** | The use case and conditions, such as one interactive coding agent, concurrent clients, or long-context analysis. |
| **Hardware profile** | A named starting or known-working configuration for a runtime, Mac, artifact, and workload. |
| **Cold run** | A run without relevant reusable prompt or session state. |
| **Warm/cached run** | A run that reuses in-memory or runtime cache state. |
| **Disk-restored run** | A run restored from a runtime's disk-backed cache. Keep it separate from cold and in-memory cached runs. |
| **Qualification** | Optional confirmation and measurement of useful settings for one fixed runtime, artifact, Mac, and workload. |

A profile is not a universal default. Evidence from another Mac, model revision,
quantization, cache state, or workload applies only to that exact scope.

See [Hugging Face and model artifacts](./hugging-face.md) for repository and
conversion details, and [optional runtime tuning](./tuning.md) for changing
settings after the server works.
