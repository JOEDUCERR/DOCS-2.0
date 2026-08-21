### To test on one gpu
./build/bin/llama-cli \
  --hf-repo Qwen/Qwen2.5-0.5B-Instruct-GGUF \
  --hf-file qwen2.5-0.5b-instruct-q4_k_m.gguf \
  --device CUDA0 \
  --gpu-layers all \
  -p "Explain what llama.cpp is in one paragraph." \
  -n 100
### To test on all 4 gpus
./build/bin/llama-cli \
  --hf-repo Qwen/Qwen2.5-0.5B-Instruct-GGUF \
  --hf-file qwen2.5-0.5b-instruct-q4_k_m.gguf \
  --device CUDA0,CUDA1,CUDA2,CUDA3 \
  --gpu-layers all \
  --split-mode layer \
  -p "Explain what llama.cpp is in one paragraph." \
  -n 100
  
------------------------------------------
## Running qwen3.6-27B-GGUF-Q4
Features: Text, vision, video

### Terminal Test (262K):
~/Documents/llama.cpp/build/bin/llama-cli \
  --hf-repo ggml-org/Qwen3.6-27B-GGUF \
  --hf-file Qwen3.6-27B-Q4_K_M.gguf \
  -p "Explain what a neural network is in 5 sentences."
1. On all GPUs
	1. Prompt processing: 13.4 tok/s
	2. Generation: 10 tok/s
2. On 2 GPUs
	1. 4.5 (slow)
	2. 4.6 (slow)
3. On 3 GPUs
	1. 31.3 tok/s
	2. 12.2 tok/s

### Terminal Test (8K)
CUDA_VISIBLE_DEVICES=0,1,2 \
~/Documents/llama.cpp/build/bin/llama-cli \
  --ctx-size 8192 \
  --hf-repo ggml-org/Qwen3.6-27B-GGUF \
  --hf-file Qwen3.6-27B-Q4_K_M.gguf \
  -p "Explain what a neural network is in 5 sentences."
5. On 2 GPUs
	1. prompt procession:  26.8 tok/s
	2. generation: 10.2 tok/s
6. On 3 GPUs
	1. 33 tok/s
	2. 12 tok/s
------------------------------------------
## Running Qwen3.8:27B-GGUF
### 8192 Test
~/Documents/llama.cpp/build/bin/llama-cli \
  --hf-repo ggml-org/Qwen3.8-27B-GGUF \
  --hf-file Qwen3.8-27B-Q4_K_M.gguf \
  --ctx-size 8192 \
  -p "Explain the difference between TCP and UDP in networking."
  1. On all GPUs
	  1. prompt processing: 47.1 tok/s
	  2. generation: 11.6 tok/s
### 16K Test
~/Documents/llama.cpp/build/bin/llama-cli \
  --ctx-size 32768 \
  --hf-repo ggml-org/Qwen3.8-27B-GGUF \
  --hf-file Qwen3.8-27B-Q4_K_M.gguf \
  -p "Explain the difference between TCP and UDP in networking."
  1. On all GPUs
	  1. prompt processing: 8.5 tok/s
	  2. generation: 3.6 tok/s
2. On all GPUs Coding test
	1. 14.6 tok/s
	2. 12.5 tok/s
### 32K Test
1. On all GPUs coding
	1. 14.7
	2. 12.2
2. On 3 GPUs (works better due to low communication overhead but only better for prompt processing and not generation.)
	1. 65
	2. 12
3. 3 GPU further coding backend
	1. 14.8
	2. 12
### 65K Test
1. On all GPUs
	1. ///////////////////// 
### 262K Test
~/Documents/llama.cpp/build/bin/llama-cli \
  --hf-repo ggml-org/Qwen3.8-27B-GGUF \
  --hf-file Qwen3.8-27B-Q4_K_M.gguf \
  -p "Explain the difference between TCP and UDP in networking."
  Well thought out answers on more context.
  1. On all GPUs
	  1. prompt processing: 16.7 tok/s
	  2. generation: 5.1 tok/s (slowest)

## Trying Qwen3-8B-GGUF on 4th gpu while first 3 are full with 27B
techlead@techlead-ai:~$ CUDA_VISIBLE_DEVICES=3 \
~/Documents/llama.cpp/build/bin/llama-cli \
  --ctx-size 16384 \
  --hf-repo Qwen/Qwen3-8B-GGUF \
  --hf-file Qwen3-8B-Q4_K_M.gguf \
  -p "Explain what TCP is in networking in 5 sentences."
  1. On GPU 3. Simple QnA
	  1. prompt: 277.6 tok/s
	  2. generation: 45.4 tok/s
2. Continued
	1. 721
	2. 47.4
## Testing both models at once
QWen3:8B at 16K context and Qwen3.8:27B at 32K context
8B:
CUDA_VISIBLE_DEVICES=3 ~/Documents/llama.cpp/build/bin/llama-cli   --ctx-size 16384   --hf-repo Qwen/Qwen3-8B-GGUF   --hf-file Qwen3-8B-Q4_K_M.gguf   -p "Write a Python function that validates an IPv4 address and returns whether it is private, public, loopback, or invalid. Include test cases."
	1st:
		Prompt: 377.9 t/s
		Generation: 40.3 t/s
	2nd:
		Prompt: 878 t/s
		Generation: 42.7 t/s
27B:
CUDA_VISIBLE_DEVICES=0,1,2 ~/Documents/llama.cpp/build/bin/llama-cli   --ctx-size 32768   --hf-repo ggml-org/Qwen3.8-27B-GGUF   --hf-file Qwen3.8-27B-Q4_K_M.gguf   -p "Create a FastAPI endpoint that accepts a file upload, calculates its SHA256 hash, stores the metadata in SQLite, and returns the file information as JSON. Include proper error handling."
	1st:
		61.8
		11.8
## Trying Agentic using llama server
CUDA_VISIBLE_DEVICES=0,1,2 \
~/Documents/llama.cpp/build/bin/llama-server \
  --ctx-size 32768 \
  --jinja \
  --tools all \
  --alias qwen3.8-27b \
  --host 0.0.0.0 \
  --port 9000 \
  --hf-repo ggml-org/Qwen3.8-27B-GGUF \
  --hf-file Qwen3.8-27B-Q4_K_M.gguf

Prompt: 
You are an autonomous coding agent.

Work only inside ~/llama-agent-test.

Your task:

1. Inspect all files in the project.
2. Read README.md, app.py, and test_app.py.
3. Identify the bug described in README.md.
4. Modify app.py to correctly handle an empty list.
5. Add a test covering the empty-list case.
6. Run the test suite.
7. If the tests fail, inspect the failure and fix the implementation.
8. Run the tests again.
9. Do not just explain what commands I should run. Use your available tools to actually inspect, modify, and test the project.
10. Do not modify files outside ~/llama-agent-test.

Start by inspecting the project.

Test1:
	* Started reasoning fast.
	* Read the files correctly.
	* 1,338 tokens
	* 1m 55s
	* 11.59 t/s
	* Did changes easily
Test2:
Prompt: You are working as an autonomous senior software engineer.

Work ONLY inside ~/llama-agent-test.

This is a deliberately broken multi-file Python project.

Your objective is to make the project correct, robust, and fully tested.

Follow this workflow:

1. Inspect the complete project structure.
2. Read every source file.
3. Read every existing test.
4. Read README.md.
5. Run the existing test suite BEFORE making changes.
6. Analyze every failure rather than blindly patching it.
7. Identify additional bugs that are not necessarily exposed by the existing tests.
8. Fix the implementation.
9. Add appropriate tests for:
   - normal input
   - empty input
   - invalid input
   - report generation
   - edge cases you discover
10. Run the complete test suite.
11. If anything fails, investigate the actual failure and fix it.
12. Run the complete test suite again.
13. Review your changes for regressions.
14. Run the tests one final time.

Important constraints:

- Do not merely tell me what commands I should execute.
- Actually use the available tools to inspect, modify, and execute the project.
- Do not modify anything outside ~/llama-agent-test.
- Do not delete tests simply because they fail.
- Preserve the existing functionality where it is correct.
- Prefer clean, maintainable Python rather than hacks.
- Do not stop after the first successful test.
- Continue until the complete test suite passes.

At the end, report:
1. Bugs discovered.
2. Files changed.
3. Tests added.
4. Final test result.
5. Any remaining concerns.

Result:
* 14,808 tokens used
* 25m spent
* went from 11 t/s to 9.8 t/s
* Did multiple inferencing and problem solving in one prompt.

Improvements: We need to give it a reasoning budget. So that it does not go off the rails.
CUDA_VISIBLE_DEVICES=0,1,2 \
~/Documents/llama.cpp/build/bin/llama-server \
  --ctx-size 32768 \
  --reasoning-budget 4096 \
  --jinja \
  --tools all \
  --alias qwen3.8-27b \
  --host 0.0.0.0 \
  --port 9000 \
  --hf-repo ggml-org/Qwen3.8-27B-GGUF \
  --hf-file Qwen3.8-27B-Q4_K_M.gguf
  prompt:
  You are an autonomous senior software engineer.

Continue working ONLY inside ~/llama-agent-test.

We are now testing your ability to handle a realistic multi-file debugging and refactoring task.

First inspect the current state of the project and run the existing tests. Do not assume the previous implementation is correct.

Your task is to turn this project into a small, robust statistics library.

Requirements:

1. Inspect every source file and every test before making changes.
2. Run the complete test suite and establish the current baseline.
3. Review the implementation for:
   - empty input
   - invalid input
   - non-numeric values
   - integer and floating-point values
   - negative numbers
   - very large values
   - division-by-zero
   - incorrect min/max behavior
   - formatting problems
   - inconsistencies between calculator.py and report.py
4. Define sensible behavior for invalid and empty input based on the existing project structure and README. Do not invent unnecessary APIs.
5. Fix the implementation while keeping the code simple and maintainable.
6. Improve the existing test suite substantially.
7. Add tests for all important edge cases you discover.
8. Make sure the report generator remains compatible with the calculator API.
9. Run the complete test suite.
10. If anything fails, investigate the actual failure rather than simply removing or weakening the test.
11. Fix any problems you discover.
12. Run the complete test suite again.
13. Review the final implementation for regressions and unnecessary complexity.
14. Run the tests one final time.

Important:

- Work autonomously.
- Actually inspect files and execute commands using your tools.
- Do not merely give me instructions.
- Do not stop after the first successful test run.
- Do not delete existing tests to make the suite pass.
- Do not modify anything outside ~/llama-agent-test.
- Keep the public API reasonably stable unless a change is genuinely necessary.
- Prefer clear production-quality Python over clever solutions.

At the end, provide a concise engineering report containing:

1. Initial test result.
2. Bugs discovered.
3. Design decisions made.
4. Files modified.
5. Tests added.
6. Final test result.
7. Any remaining limitations.
Test 3:

















-----------------------------------
## Conclusion on Qwen3.8-27B Q4
Qwen3.8-27B Q4_K_M
===================
8K
  + Fast
  + 11.6 tok/s generation
  - Limited agent working space

32K
  + Stable
  + Good coding performance
  + ~12.2–12.5 tok/s generation
  + Better agent working space
  → DEFAULT

65K
  - / generation failure
  → AVOID

262K
  + Maximum working space
  + Coherent long-context behavior
  - ~5.1 tok/s generation
  → SPECIALIZED LONG-CONTEXT MODE
  --------------------------------------
  