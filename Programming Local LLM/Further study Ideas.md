Further study + Ideas

* Stuff that fills the RAM:
model weights
+
KV cache
+
temporary buffers
+
CUDA/driver overhead

* Stuff affecting the CPU:
request management
tokenization
networking
KV/cache management
scheduling
multiple concurrent requests
feeding the GPUs

(And with four old Pascal GPUs, you don't want the CPU becoming another bottleneck.)

* 4x GTX 1080 ti can run 30B models for this usecase if unified. But that usecase is slow.

Features:
* Alternative Idea: One AI Workstation for 5-10 Devs separate for each row of a total of 500 devs (Apple unified memory). Keeping a main server in the IT room for non power users (people who just want a replacement to claude cowork.).
* Having dumber fallback models with continued context.
* Keep the dumber model for non tech people.
* Add company data as knowledgebase.
* Add system prompts as security walls where the user cannot request critical data or make the model do unethical tasks.
* Add router functionalities for having multiple models as options on same context.
* Web Search implementation.

----------------------------------------------------------

                 ┌─────────────────────────┐
                 │      AI GATEWAY          │
                 │ Auth / Routing / Limits  │
                 └────────────┬────────────┘
                              │
       ┌──────────────────────┼─────────────────────┐
       │                      │                     │
       ▼                      ▼                     ▼
 ┌────────────┐        ┌─────────────┐       ┌────────────┐
 │ AI PODS    │        │ CENTRAL AI  │       │ OpenRouter │
 │            │        │ SERVER      │       │            │
 │ 5–10 users │        │             │       │ Cloud      │
 │ each       │        │ Large model │       │ fallback   │
 │            │        │ RAG         │       │            │
 │ 8B/14B     │        │ Agents      │       │ Premium    │
 └────────────┘        └─────────────┘       └────────────┘
       │                      │                     │
       └──────────────────────┼─────────────────────┘
                              ▼
                     CANONICAL SESSION
                          STATE
                              │
                ┌─────────────┼─────────────┐
                │             │             │
             Messages       Summary       Tools/RAG

----------------------------------------------------------

Realistic expectations:

Model: 13B
GPU: 48 GB
Users: 7

* 13B parameter model has about 26 GB of weights to load onto the GPU. This is because its a 16 bit model

Free: 22 GB

* At every request, the model stores KV cache (stores attention computations) on the GPU.
* For a 13B model with 4k context window = 3GB per request.
* 1 user = 3GB, 3 users = 9 GB, 7 users = 21 GB (only for KV cache)

* output tokens are also cached.

----------------------------------------------------------

Solution?
* Use vLLM for KV cache optimization. (10x faster than usual methods).
* Or use GGUF with ollama (ollama run hf.co/{username}/{repository}:{quantization})
* Use a lower quantization model (Dumber)
* We can send the KV cache to RAM but it will be 5x slower

----------------------------------------------------------

KV = sequence length X active users

----------------------------------------------------------

**Qwen3:8B as a starting point:** Yes, good choice. It's much lighter than a 13B (weights ~16GB at FP16, less than half at quantized), leaves real headroom for KV cache across multiple users, and is a capable, fairly recent model. Solid place to start before scaling up.

**Finding a quantized version — you don't need to quantize it yourself:**

- Easiest: `ollama pull qwen3:8b` — Ollama serves a pre-quantized GGUF (Q4_K_M) by default.
- Or browse Hugging Face for pre-made GGUFs: search `Qwen3-8B GGUF` (e.g. `bartowski/Qwen3-8B-GGUF`, `unsloth/Qwen3-8B-GGUF`), pick a quant level (Q4_K_M is the usual sweet spot), then `ollama run hf.co/<repo>:<quant>`.
- Only quantize yourself if you need a custom quant level: use `llama.cpp`'s `convert_hf_to_gguf.py` + `llama-quantize` tool.

**On the vLLM/Pascal issue — ChatGPT is right.** I checked: vLLM officially supports GPUs with compute capability 7.0 and above, and the 1080 Ti is compute capability 6.1. vLLM doesn't officially work on the 1080 Ti — some users patch it for Volta, but Pascal patches are scarce and break with updates. Don't fight this.

**What to use instead:** Ollama or llama.cpp directly — both are Pascal-native and this is the standard path for your hardware. llama.cpp implements its own efficient attention that does benefit Pascal, unlike Flash Attention 2 (Ampere-only).

Setup outline:

1. Install Ollama (or build llama.cpp with `-DGGML_CUDA=ON`).
2. Pull/run `qwen3:8b` — it auto-splits across your 4 GPUs.
3. For multi-user concurrency, set `OLLAMA_NUM_PARALLEL` (e.g. 4-7) and `OLLAMA_MAX_LOADED_MODELS=1` so requests batch instead of each spawning a new model instance.
4. Test actual concurrent load before promising 7 users — Pascal's weak FP16 throughput means real tok/s under load matters more than VRAM math alone.