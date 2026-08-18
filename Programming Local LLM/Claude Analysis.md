TODO:
1. Try llama cpp and adjust parameters (settings which are hidden in Ollama):
	- Confirm all gpus are detected.
	- Do explicit tensor split accross 4 cards.
2. Check open web uis tool calling mode (on legacy the context is self-inflating. every turn re-injects the full tool-description prompt and kills KV cache reuse)
3. Try a Dense qwen3-coder:14b tuned model at Q4 (non MoE due to heavy overhead).

LLama Cpp advantages:
* llama cpp has much better multi gpu control
* llama cpp has also excellent kv cache control
* better control over parallel requests
(Ollama is llama.cpp underneath (same GGML/GGUF engine, same CUDA kernels). Switching doesn't give you a different inference engine — it gives you the same engine with the wrapper removed. So the question isn't "which is faster," it's "do you need the knobs Ollama hides.")

Stuff to customize in llama.cpp:
1. Tensor splitting (parallel processing)
2. Quanization (Q4)
3. Context Size
4. KV Cache adjusting: --cache-type-k / --cache-type-v
5. Batching: -ub/-b (Batch size tuning to affect prompt speeds)
6. GPU model split: --tensor-split (How the model is divided across 4 GPUs)

Hardware Limitations:
1. Fast Attention is off on Pascal GPUs. So KV cache is fp16 and not q4/q8 which requires FA. So KV cache becomes costly and 32K context eats VRAM fast. Increasing it also does not work as VRAM fills up with 18-20GB of Q4 MoE weights.
2. We cannot use OpenClaw as it is tuned for a much bigger and heftier agentic loop which our current hardware cannot bear. So, we skip OpenClaw and Hermes agent for now.
3. M0E models front load is heavy for the 4 core CPU handling everything Non-GPU.

Why not qwen3.8:27B?
Bottom line: it's not too big for your VRAM, but dense 27B on 4x Pascal over PCIe is going to be slow at both load and prefill — likely slower than your 35B-A3B MoE, not faster, despite being "smaller." This model doesn't fix your speed problem; it's a different tradeoff, not a strict upgrade for your rig. I'd hold off on it until quants mature and stick with your llama.cpp tuning plan on the MoE model, or try the dense 14B-class idea I mentioned instead if you want to test dense-vs-MoE properly.

Implementation:
1. As soon as I changed the tool calling from Legacy to Native, Turned off Image generation capabilities. The model started THINKING fast. That works.
	1. First test was basic QnA and it passed with fast responses. Token usage Average: 5K.
	2. Second test is website code generation (non agentic). I can observe slow code generation (Line by line, a little faster than a human coder).
		1. 1st token: 15K(11+3), 3.3m
		2. 2nd token: 13K(8+4), 6.1m
		3. 3rd token: 16K(11+4), 4.4m
		4. 4th Token: 17K(13+3), 3.5m
	3. Testing RAG. Gave it my resume.
		1. 1st token: 9K(6+2), 2.3m
	4. Testing Agentic. Started thinking faster. It thinks multiple times due to its agentic nature.
		1. 1st token: 3.8K(3+0.1), 30s
		2. 2nd token: 14K(14+0.1), 4m FAILED: "Can you remove all texts with emojis in the website. I do not want it to look vibe coded".
		3. 3rd token: , Testing after prompting the agent to use grep instead of manual reading.

Further Claude Analysis:
1. Line-by-line codegen speed is roughly what you'd expect from a 35B-A3B on Pascal without Flash Attention — not fast, but not the bug.
2. Full agentic work needs more than 32K ceiling which is acting as a constraint for now. We need to switch to llama.cpp to have more control.

TODO:
1. Trying a different agentic system first. Open Hands.
	1. OpenHands runs agents that complete entire engineering tasks, taking actions across entire codebases, running tasks in parallel, and executing changes in real environments.

Review:
1. By EOD will have agentic framework setup for development work. Running automations all synced with GitHub codebases.
2. Implemented RAG and knowledgebase.
3. Finetuned the model to responde much faster for a single user with minimal token usage.
4. Large code generation is still slow but workable


New Structure for Open Hands
curl
 │
 │ API key
 ▼
Nginx :11435
 │
 │ authenticated
 ▼
Ollama :11434
 │
 ▼
Qwen

How to run?
Install openhands on pc using uv
cd ~/projects
openhands serve --mount-cwd (opens openhands on the project folder)