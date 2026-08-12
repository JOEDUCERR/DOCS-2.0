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
* One AI Workstation for 5-10 Devs separate for each row of a total of 500 devs (Apple unified memory). Keeping a main server in the IT room for non power users (people who just want a replacement to claude cowork.).
* If you think the system is unusable or too costly, we can host models on cloud need 1 day for R&D.
* Having dumber fallback models with continued context.
* Openrouter

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
