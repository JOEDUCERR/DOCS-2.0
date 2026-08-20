TODO:
1. Try llama cpp and adjust parameters (settings which are hidden in Ollama):
	- Confirm all gpus are detected.
	- Do explicit tensor split accross 4 cards.
2. Check open web uis tool calling mode (on legacy the context is self-inflating. every turn re-injects the full tool-description prompt and kills KV cache reuse): IT WORKS
3. Try a Dense qwen3-coder:14b tuned model at Q4 (non MoE due to heavy overhead).: WE ARE AVOIDING DENSE MODELS DUE TO PARAMETER ACTIVATION OVERHEAD.

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
1. Fast Attention is off on Pascal GPUs. **So KV cache is fp16 and not q4/q8 which requires FA. So KV cache becomes costly and 32K context eats VRAM fast.** Increasing it also does not work as VRAM fills up with 18-20GB of Q4 MoE weights.
2. We cannot use OpenClaw as it is tuned for a much bigger and heftier agentic loop which our current hardware cannot bear. So, we skip OpenClaw and Hermes agent for now.
3. M0E models front load is heavy for the 4 core CPU handling everything Non-GPU.

Why not qwen3.8:27B?
Bottom line: it's not too big for your VRAM, but dense 27B on 4x Pascal over PCIe is going to be slow at both load and prefill — likely slower than your 35B-A3B MoE, not faster, despite being "smaller." This model doesn't fix your speed problem; it's a different tradeoff, not a strict upgrade for your rig. I'd hold off on it until quants mature and stick with your llama.cpp tuning plan on the MoE model, or try the dense 14B-class idea I mentioned instead if you want to test dense-vs-MoE properly. 
**Update**: Finally got it to work but due to its dense nature and all params being active at once, it lags heavily on basic code generation tasks.

Implementation:
1. Qwen3.6:35B: As soon as I changed the tool calling from Legacy to Native, Turned off Image generation capabilities. The model started THINKING fast. That works. **Verdict**: Good for basic QnA (kind of overkill), Fine speed in code generation responses, stalls in agentic.
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
2. Qwen3.8:27B: Same settings as qwen3.6. lets see. Model takes total of 5 minutes on first load. One second prompt it was way faster  in thinking, but responses are shown laggy, maybe a hardware limitation as model just came out. Large input token wastage is not happening yet. Also the model takes up 30GB space. MODEL IS DRAWING ALMOST FULL POWER FROM ALL GPUs. **Hence speed of response is totally based on power throttling/limitation.**
	1. Basic QnA. Asked about networking
		1. 1st: 5.7K(5.7+0.02), 5m
		2. 2nd: 6.4K(5.7+0.6), 1.5m (wayy better + thoughtful responses [claude feel])
		3. 3rd: 8K(2.4+1.6), 3.2m
	2. Basic Code writing. Started thinking right away as model was already loaded.
		1. 1st: 9K(5+3), 6m
		2. 2nd: 12K(9+2), 6m
		3. 3rd: 13K(11+2), 4.5m (Input context size is increasing)
		4. 4th: 17K(13+3), 7m
	3. RAG:
		1. Working but slow af
3. Qwen3-Coder-30b. It was an epic fail. No responses just "/////////////////"
4. Mistral:7B. Good for basic knowledge and automating non tech tasks.
	1. Basic QnA
		1. 1st: 5K(5+0.2), 2.3m
		2. 2nd: No Output
		3. 3rd ad 4th outputs came averaging out to 6K tokens and 30s responses
		4. 5th: 7K(6+0.4), 1.5m: Reloaded the model after emptying GPU. its fast alright.
	2. Knowledgebase Test.
		1. 1st: 7.8K(7+0.4), 11m: That's crazy.
		2. 2nd: 8.2K(7+0.4), 15.5m: nahh
5. Qwen3:8B
	1. Basic QnA
		1. 1st: 4.7K(4+0.1), 2.1m
		2. 2nd: 5K(4+0.5), 48s
		3. 3rd: Failure with "///////////////////////////////"
	2. Basic QnA with 8K context and multiple changed
		1. 1st: 4K(4+0.001), 1m
		2. 2nd: 4.9K(4+0.1), 21s
		3. 3rd: 4.9K(4+0.1), 20s
	3. Basic Coding
		1. 1st: 5.3K(4.7+0.5), 3.1m
		2. 2nd: Cannot see token usage after getting output. Maybe it got locked by the limits set.
		3. 3rd: Model responds with "????????????????????????"
		4. 4th: again "??????????????????????"

Further Claude Analysis:
1. Line-by-line codegen speed is roughly what you'd expect from a 35B-A3B on Pascal without Flash Attention — not fast, but not the bug.
2. Full agentic work needs more than 32K ceiling which is acting as a constraint for now. We need to switch to llama.cpp to have more control.

TODO:
1. Trying a different agentic system first. Open Hands.
	1. OpenHands runs agents that complete entire engineering tasks, taking actions across entire codebases, running tasks in parallel, and executing changes in real environments.: Stuck in implementation
	2. Test Qwen3-Coder-30B-A3B with the settings that worked for qwen3.8 and 3.6. (Due to less active parameters per token and 17-20GB allocation only. Also due to its 250K context window which is expandable as well).
		1. Also we cannot use dense models as well which would have been good options at lower parameter count, but they activate all parameters at once and without FA enabled, it makes things much slower. [Requires GPU upgradation].
	3. Try out llama.cpp today.
	4. Also test mistral and qwen3:8B.

Review:
1. By EOD will have agentic framework setup for development work. Running automations all synced with GitHub codebases.
2. Implemented RAG and knowledgebase.
3. Finetuned the model to responds much faster for a single user with minimal token usage.
4. Large code generation is still slow but workable
5. Due to Fast Attention off (On Pascal GPUs), we cannot use Smaller but Denser models. We have to go for models with smaller active parameters per token.
	1. **High memory traffic:** Standard attention reads and writes massive N × N matrices back and forth to High Bandwidth Memory (HBM).
	2. **Quadratic slowdown:** Memory usage and time scale quadratically (O(N²)) as text length grows.
	3. **Hardware bottleneck:** Modern graphics cards calculate math fast, but waiting on memory transfers slows them down.

-------------------------------------------------------

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