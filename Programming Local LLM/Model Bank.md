(P.S. Combined memory here means RAM + VRAM.)

Qwen3.8:27B - 
* Taking a long time to even start thinking on OpenWebUI
* GPUs are not getting filled up
* If qwen3.6 runs but 3.8 hangs, then this is ollama config on pascal gpu problem.

Qwen3:8B : Suprisingly good for lightweight coding and reasoning tasks on a budget 6-8 GB GPU.

Qwen3-coder-next:80B : with 3B active parameters per token (3.7%) built for agentic coding and requires at least 45GB vram or combined memory.

[TO BE RELEASED IN AUG (try 3.7 till then)] Qwen3.8:27B : Runs on a machine with about 24 GB for average use (Q6_K Context - limited) and 55 GB for BF16 Precision. For the average user Q4_K_M (24GB system) is a mainstream sweet spot but leaves you wanting on hard reasoning and long agentic tasks.

(For more on Qwen3.8:27B - https://www.alibabacloud.com/blog/what-it-actually-takes-to-run-qwen3-8-27b-locally_603428)

Qwen3.6:35B : MoE model with only 3B active parameters per token and 250k token context window. Can run on 40 GB VRAM. or 24GB (RTX 3090/4090.36GB MAC) for Q_4 versions with 32 GB RAM. For high accuracy it needs 40 GB VRAM (2x RTX 3090/64GB MAC) and 64 GB of RAM to use Q_8. Also you could use Apple unified memory of 32 GB - 64 GB for Q4 - Q8. 80GB VRAM (Nvidia A100/H100) for enterprise level.

Deepseek-V4-Flash-0731:284B : with 13B active parameters and 1M token context window this needs at least 110 GB combined memory (Quad 24 GB cards). For full precision it needs about 162GB memory.

GLM-5.2:753B : Suitable for multi step SE workflows approaching close to models like Claude Opus 4.8 and requires at least 411 GB vram for a production standard work (preferrably 8 Ampere A100s).

Kimi K3:2.8T : Buit for agentic coding with 1M token context window. 1.5TB Vram. Minimal boots require at least 640 GB memory.

----------------

(Note on multi GPU setups:
 For multi-GPU setups, there are two main types of distributed processing, tensor parallel and pipeline parallelism.

If inter-GPU communication speeds are low, tensor parallel can become more than 2x as slow. Additionally, if the GPUs have different VRAM capacities, it defaults to the lowest one across all cards. On the other hand, while pipeline parallelism doesn't suffer as much from communication latency, it processes sequentially, GPU utilization drops. It means one GPU sits idle while another is working.

PCIe is very slow. )

(Note on kv cache:
A KV (Key-Value) cache is a memory optimization technique used in Transformer-based AI models (like Large Language Models) during text generation. By storing and reusing the key and value vectors computed for past tokens, the model avoids recalculating attention states from scratch for every new word, vastly accelerating inference speed.)

(Note on MoE
Mixture of Experts (MoE) is a machine learning design where a large neural network splits tasks among smaller sub-networks called "experts". A router or gating network decides which expert handles each piece of data. This makes models massive in total size but fast and cheap to run.
)	

(Note on YaRN
YaRN (Yet another RoPE extensioN) is a memory- and compute-efficient method used to extend the context window of transformer-based large language models that use Rotary Position Embeddings (RoPE). It rescales rotation frequencies unevenly and adjusts attention temperature, allowing models trained on short texts to handle massive lengths with minimal extra fine-tuning.
)

Note on llama
Think of llama.cpp as the powerful, manual transmission engine, and Ollama as the automatic, daily-driver car wrapper built around it.

--------------------------------------------------------------

Goals:
* Inhouse Local LLM
* Shared to employees without losing compute. Sol: Using vLLM instead of Ollama or llama.cpp
* Ready to commit compute upto: 128GB RAM, <unknown> GPU
* Model :
- Agentic abilities
- MoE Mixture of Experts
- Good Token Context length

TODO:
* Search for capable models
- Qwen-coder-next:latest - 80B
- deepseek-ai/DeepSeek-V4-Flash-0731 - 304B
* Setup vLLM for multiple users at once.


Claude response:
* vLLM was a positive approach
* Multi GPU setup needs tensor paralellism. PCIe is considered slow so lets see.
* model size vs context length vs number of concurrent users, because all three compete for the same VRAM (weights + KV cache).
* Suggests api-key support more as anyone on the LAN can hammer away the one GPU server
* Suggest more VRAM for 15+ devs concurrently on a 32B class model. 2x

vLLM config pieces you'll need regardless of model choice
--tensor-parallel-size N — matches GPU count if you go multi-GPU.
--max-model-len — cap this deliberately (e.g. 32768) rather than defaulting to max; it directly controls per-session KV cache reservation.
--gpu-memory-utilization 0.90 (tune down if you're sharing the box with anything else).
--tool-call-parser — must match your chosen model family (e.g. hermes or qwen depending on version) for agentic tool-calling to actually parse correctly. This is a common silent-failure point — worth testing explicitly before rollout, not assuming it works.
--enable-auto-tool-choice alongside the parser flag.
--api-key for basic access control, sitting behind nginx/Caddy for TLS + per-team logging on your LAN.

* Context will be dependent solely on the amt if memory left after model is on the memory itself.
* can be upgraded later.
* 


15 developers using ChatGPT-style chat

is significantly easier than:

15 developers running autonomous coding agents simultaneously.

Points:
1. You can't beat claude. You can come close to it.
2. Asked devs about use cases: Coding, reviewing PR, having whole project context: Hence, we might need agentic capabilities.

When using smaller 8B model, the responses are fast but it is unable to perform Agentic tasks and tool calling. Also limited in writing detailed code.

When using the 35B parameter model, the model loads slow, but it is able to perform agentic tasks. 

* Implement RAG
* Add guardrails

Now we have the actual root cause.

This is **not primarily an OpenCptr problem and not primarily a 48K-context problem**.

Your Ollama runner is **crashing/stalling during GPU discovery**.

The decisive evidence is:

ALSO

We now know that at the time of the failure:

llama-server = gone

Ollama /api/ps = no models

GPU memory = still allocated temporarily

So this was an actual runner failure, not merely OpenCptr waiting.

***** There is **no NVLink, PIX, or PXB path** between the GPUs.

Why does a trivial request take ~2 minutes 19 seconds before completing?

ALSOOOOOOOOOOOOOO

So every time the inference engine needs to synchronize/interact between shards, you're paying the cost of PCIe communication.

For a 35B MoE model this can still work, but it explains why:

> "It fits in 44 GB"

does **not** mean:

> "It will run like a 35B model on one fast 44 GB GPU."

*** BACKEND IS HEALTHY AT 32K CONTEXT LENGTH

(On direct api test)
- Model loading: **~0.5 s**
- Prompt processing: **~0.5 s**
- Generation: **~9.9 s**
- Generation speed: **~14.6 tok/s**
- Context: **32,768**
- GPU offload: **97%**
- Model resident: **28 GB**
- Keep-alive: **Forever**

# This also explains why 48K felt worse

At 48K you gave the agent even more room to accumulate context.

But the problem isn't that you **need** 48K.

The problem is that OpenCptr is already consuming almost the entire **32K** window for a trivial request.