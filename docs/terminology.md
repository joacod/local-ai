# Terminology

These short definitions cover the terms you need when choosing a local model.

## Models and artifacts

- **Local model:** model files stored on your Mac rather than accessed through a
  hosted API.
- **Model family:** a related line of releases that share an architecture or
  name, such as a family with 4B, 27B, and 35B variants.
- **Checkpoint:** a saved set of weights for one release or training state.
- **Model artifact:** files prepared for a particular loader or runtime. An
  artifact may include weights, configuration, tokenizer files, and a chat
  template.
- **Conversion:** a copy of a checkpoint prepared for another format or runtime,
  such as MLX or GGUF. A conversion is related to the original checkpoint but
  is not automatically interchangeable with it.
- **Hugging Face:** a hosting service where publishers share model repositories,
  files, metadata, documentation, and revisions.

## Size and representation

- **Parameters:** learned numeric values in a model. Parameter count describes
  model scale; it is not the same as file size or runtime memory.
- **Quantization:** representing weights with fewer or different numeric values
  to reduce file size and memory use. The exact quantization scheme matters.
- **GGUF:** a model-file format commonly loaded by llama.cpp.
- **MLX:** Apple's machine-learning framework for Apple Silicon. In this
  repository, MLX artifacts are normally loaded by `mlx-lm` or an MLX-based
  server.
- **MoE:** Mixture of Experts. Several expert pathways exist, but only some are
  active for each token.
- **Total parameters:** all parameters in a model, including inactive experts.
- **Active parameters:** the parameters used for one token. They help explain
  compute cost, but the full artifact still affects storage and memory.

## Context and serving

- **Inference server/runtime:** software that loads an artifact and generates
  text. The server is the running process and local API exposed by the runtime.
- **Context window:** the maximum prompt and conversation span a model or
  runtime accepts. The advertised maximum may not be practical on every Mac.
- **KV cache:** runtime state for attention keys and values. It grows as the
  active context grows and is separate from the downloaded model files.
- **Model cache:** downloaded artifact files kept locally for reuse. It is not a
  new format and does not make an artifact compatible with another runtime.
- **MTP:** Multi-Token Prediction. A complete artifact with native MTP heads can
  let a compatible runtime propose and verify several tokens during serving.
- **API model ID:** the identifier returned by a server's model-list endpoint.
  It may differ from the Hugging Face repository name.

## Why there are many variants

One family may be published as an original checkpoint, an MLX conversion, a
GGUF repository, and a runtime-specialized artifact. Each may differ in file
format, quantization, tokenizer packaging, chat template, architecture support,
or extra components.

That is why an artifact that loads in one runtime may fail in another. Check the
runtime README, the model card, and any [local model note](../local-models/)
before starting a large download.
