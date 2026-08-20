1. Try llama cpp and adjust parameters (settings which are hidden in Ollama):
	- Confirm all gpus are detected.
	- Do explicit tensor split accross 4 cards.
2. Check open web uis tool calling mode (on legacy the context is self-inflating. every turn re-injects the full tool-description prompt and kills KV cache reuse): IT WORKS
3. Try a Dense qwen3-coder:14b tuned model at Q4 (non MoE due to heavy overhead).: WE ARE AVOIDING DENSE MODELS DUE TO PARAMETER ACTIVATION OVERHEAD.
-------------------------------------------
1. TODO:
2. Trying a different agentic system first. Open Hands.
	1. OpenHands runs agents that complete entire engineering tasks, taking actions across entire codebases, running tasks in parallel, and executing changes in real environments.: Stuck in implementation
	2. Test Qwen3-Coder-30B-A3B with the settings that worked for qwen3.8 and 3.6. (Due to less active parameters per token and 17-20GB allocation only. Also due to its 250K context window which is expandable as well).
		1. Also we cannot use dense models as well which would have been good options at lower parameter count, but they activate all parameters at once and without FA enabled, it makes things much slower. [Requires GPU upgradation].
	3. Try out llama.cpp today.
	4. Also test mistral and qwen3:8B.
-------------------------------------------
4. Implemented RAG and knowledgebase: Done. RAG speed is average while model tends to scrape through all of the knowledgebase at once (it seems) and makes it extremely slow.
5. Finetuned the model to responds much faster for a single user with minimal token usage: This was done with Qwen3.6 on coding tasks which averaged around 4m and still failed with Agentic 3 prompts.
6. Large code generation is still slow but workable
7. Due to Fast Attention off (On Pascal GPUs), we cannot use Smaller but Denser models. We have to go for models with smaller active parameters per token.
	1. **High memory traffic:** Standard attention reads and writes massive N × N matrices back and forth to High Bandwidth Memory (HBM).
	2. **Quadratic slowdown:** Memory usage and time scale quadratically (O(N²)) as text length grows.
	3. **Hardware bottleneck:** Modern graphics cards calculate math fast, but waiting on memory transfers slows them down.
-------------------------------------------
TODO:
1. Make a smaller model work.
	1. Qwen3:8B
	2. Qwen3.6:27B
2. Make a report when that smaller model works.
3. Qwen3.8:27B should be 18GB but shows as 30GB check why.