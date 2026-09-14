### Starting the server for 27B
CUDA_VISIBLE_DEVICES=0,1,2 \
/home/techlead/Documents/llama.cpp/build/bin/llama-server \
    -m /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/Qwen3.8-27B-Q4_K_M.gguf \
    --ctx-size 32768 \
    --reasoning-budget 4096 \
    --jinja \
    --tools all \
    --alias qwen3.8-27b \
    --host 0.0.0.0 \
    --port 8000
    
### Optimized server for 27B
```
CUDA_VISIBLE_DEVICES=0,1,2 \
/home/techlead/Documents/llama.cpp/build/bin/llama-server \
    -m /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/Qwen3.8-27B-Q4_K_M.gguf \
    --ctx-size 32768 \
    --gpu-layers all \
    --split-mode layer \
    --flash-attn on \
    --batch-size 2048 \
    --ubatch-size 512 \
    --reasoning-budget 1024 \
    --jinja \
    --tools all \
    --alias qwen3.8-27b \
    --host 0.0.0.0 \
    --port 8000
```
## More optimized 27B
```
CUDA_VISIBLE_DEVICES=0,1,2 /home/techlead/Documents/llama.cpp/build/bin/llama-server -m /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/Qwen3.8-27B-Q4_K_M.gguf --mmproj /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/mmproj-Qwen3.8-27B-Q8_0.gguf --ctx-size 70000 --gpu-layers all --split-mode layer --flash-attn on --cache-type-k q4_0 --cache-type-v q4_0 --batch-size 2048 --ubatch-size 512 --threads 4 --reasoning-budget 1024 --jinja --tools all --alias qwen3.8-27b --host 0.0.0.0 --port 8000
```
### Starting the server for 8B
CUDA_VISIBLE_DEVICES=3 \
/home/techlead/Documents/llama.cpp/build/bin/llama-server \
    -m /home/techlead/.cache/huggingface/hub/models--Qwen--Qwen3-8B-GGUF/snapshots/7c41481f57cb95916b40956ab2f0b139b296d974/Qwen3-8B-Q4_K_M.gguf \
    --ctx-size 16384 \
	--reasoning-budget 4096 \
    --jinja \
    --tools all \
    --alias qwen3-8b \
    --host 0.0.0.0 \
    --port 8001

------------------------------------------
# Testing
* First showing the 27B model + Running tests
* Showing 8B side by side + Tests
* Clearly 27B takes a while and 8B takes few seconds to finish with the same settings.
* Both are working fine in normal chat and basic agentic. If you prompt it to create a whole application it fails after sometime with "/////" or "?????"
* **Main issue is these models are optimized for one user now, main issue is if these models are to be divided and provided to many users, the context would fill up fast (it is already getting filled during 3-4 agentic prompts. [A little more than Claude free version])**
* Hence, we would need a better RIG for multiple users.
Sol: Better hardware for multi users, diff macs or cloud gpus
## Prompts for testing
### Basic QnA
* Hi
* Can you tell me how far is the earth from the sun?
* A server has 4 GPUs. A 27B Q4_K_M model is split across GPUs 0, 1, and 2, while GPU 3 is unused. Explain why using 3 GPUs could potentially have better prompt-processing performance than using all 4 GPUs.
### Code generation
```
```Write a Python function called `find_duplicate_ips(ips)` that takes a list of IPv4 address strings and returns a dictionary mapping each duplicated IP address to its occurrence count.

Requirements:
- Validate IPv4 addresses.
- Ignore invalid addresses.
- Only include IPs appearing more than once.
- Preserve the order in which duplicated IPs first appear.
- Do not use external libraries.
- Include the implementation and 3 test cases.
```

```
Write a Python script that reads a CIDR network such as `192.168.10.0/24` and returns all usable IPv4 addresses in that subnet.

Requirements:
- Do not use the `ipaddress` module.
- Correctly calculate network and broadcast addresses.
- Support /8 through /30.
- Reject invalid CIDR input.
- Explain the bitwise logic briefly.
- Include tests for /24, /30, and invalid input.
```

```
import threading
import time

counter = 0

def worker():
    global counter
    for _ in range(100000):
        counter += 1

threads = []

for _ in range(10):
    t = threading.Thread(target=worker)
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print(counter)

Analyze this Python program.

1. Explain whether it is guaranteed to print 1,000,000.
2. Identify the concurrency problem.
3. Explain why the behavior can be problematic despite Python's GIL.
4. Provide two correct solutions.
5. Compare the two solutions in terms of correctness, performance, and scalability.
```

### Agnetic file access and tool calling (Tokens finished fast on 27B and 8B denied access to file system so had to specify)
```
Check and analyze all the files (just read them till you just know their coding language or basic structure) in this folder structure. Do not waste too many tokens reading each and every line
```

 ```
 you have access to inspect the files. I have also given you access to the folder llama-agent-test
 ```

![[Pasted image 20260825162937.png]]

Limit finished for 8B

--------------------------------------------------------------
### Basic generation
time curl -s http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3.8-27b",
    "messages": [
      {
        "role": "user",
        "content": "Explain what a reverse proxy is in exactly 3 concise bullet points."
      }
    ],
    "temperature": 0.2,
    "max_tokens": 256,
    "stream": false
  }' | python3 -m json.tool
  
```
"created": 1787651679,
    "model": "qwen3.8-27b",
    "system_fingerprint": "b10532-70aff2525",
    "object": "chat.completion",
    "usage": {
        "completion_tokens": 201,
        "prompt_tokens": 67,
        "total_tokens": 268,
        "prompt_tokens_details": {
            "cached_tokens": 0
        }
    },
    "id": "chatcmpl-qWoHUUTc6OcBZcEIMPxFiLhiCWiJ9c6V",
    "timings": {
        "cache_n": 0,
        "prompt_n": 67,
        "prompt_ms": 1632.694,
        "prompt_per_token_ms": 24.368567164179105,
        "prompt_per_second": 41.036471010489414,
        "predicted_n": 201,
        "predicted_ms": 17346.688,
        "predicted_per_token_ms": 86.73343999999999,
        "predicted_per_second": 11.52957844171752
    }
}

real	0m18.999s
user	0m0.036s
sys	0m0.018s

```

### Reasoning
time curl -s http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3.8-27b",
    "messages": [
      {
        "role": "user",
        "content": "A server has 4 GPUs. A 27B Q4_K_M model is split across GPUs 0, 1, and 2, while GPU 3 is unused. Explain why using 3 GPUs could potentially have better prompt-processing performance than using all 4 GPUs."
      }
    ],
    "temperature": 0.2,
    "max_tokens": 512,
    "stream": false
  }' | python3 -m json.tool
  
```
    "created": 1787651785,
    "model": "qwen3.8-27b",
    "system_fingerprint": "b10532-70aff2525",
    "object": "chat.completion",
    "usage": {
        "completion_tokens": 512,
        "prompt_tokens": 110,
        "total_tokens": 622,
        "prompt_tokens_details": {
            "cached_tokens": 42
        }
    },
    "id": "chatcmpl-lPXmvJIRW0Zy62Ufge18v5NHqTiKYkmI",
    "timings": {
        "cache_n": 42,
        "prompt_n": 68,
        "prompt_ms": 1633.724,
        "prompt_per_token_ms": 24.025352941176468,
        "prompt_per_second": 41.62269759151485,
        "predicted_n": 512,
        "predicted_ms": 40589.263,
        "predicted_per_token_ms": 79.43104305283757,
        "predicted_per_second": 12.589536301755468
    }
}

real	0m42.899s
user	0m0.039s
sys	0m0.016s

```
### Streaming (Try during presentation: to see all details)
curl -N http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3.8-27b",
    "messages": [
      {
        "role": "user",
        "content": "Explain CUDA GPU memory offloading in llama.cpp."
      }
    ],
    "temperature": 0.2,
    "max_tokens": 512,
    "stream": true
  }'
```
ata: {"choices":[{"finish_reason":"length","index":0,"delta":{}}],"created":1787651861,"id":"chatcmpl-zzz2RFqhlXeWUMEDUi7Go3nZ0g9sV2CX","model":"qwen3.8-27b","system_fingerprint":"b10532-70aff2525","object":"chat.completion.chunk","timings":{"cache_n":42,"prompt_n":21,"prompt_ms":1399.085,"prompt_per_token_ms":66.62309523809525,"prompt_per_second":15.009809982953144,"predicted_n":512,"predicted_ms":43256.998,"predicted_per_token_ms":84.65165949119374,"predicted_per_second":11.81311749835252}}

data: [DONE]
```

----------------------------------------------------
# Testing creating large projects with 27B

```
CUDA_VISIBLE_DEVICES=0,1,2 \
/home/techlead/Documents/llama.cpp/build/bin/llama-server \
-m /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/Qwen3.8-27B-Q4_K_M.gguf \
--ctx-size 70000 \
--gpu-layers all \
--split-mode layer \
--flash-attn on \
--cache-type-k q4_0 \
--cache-type-v q4_0 \
--batch-size 2048 \
--ubatch-size 512 \
--threads 4 \
--threads-batch 4 \
--reasoning-budget 1024 \
--jinja \
--tools all \
--alias qwen3.8-27b \
--host 0.0.0.0 \
--port 8000
```

Prompt 1:
Create one single HTML page "Voxelcraft" with Three.js (CDN) with hero section that feels like small Minecraft.

- Generate terrain made of colored cubes (gress, stone, water)
- Make it looks impressive
- Output everything in one file, no external assets except Three.js
- Smooth 60 fps performance. Clean modern code.

Hiccup: Stopped due to lack of image/video input. adding --mmproj

```
CUDA_VISIBLE_DEVICES=0,1,2 \
/home/techlead/Documents/llama.cpp/build/bin/llama-server \
-m /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/Qwen3.8-27B-Q4_K_M.gguf \
--mmproj /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/mmproj-Qwen3.8-27B-Q8_0.gguf \
--ctx-size 70000 \
--gpu-layers all \
--split-mode layer \
--flash-attn on \
--cache-type-k q4_0 \
--cache-type-v q4_0 \
--batch-size 2048 \
--ubatch-size 512 \
--threads 4 \
--reasoning-budget 1024 \
--jinja \
--tools all \
--alias qwen3.8-27b \
--host 0.0.0.0 \
--port 8000
```

* It runs!!!!!
* run: joeducer@techlead-H81M-S:~/Documents/voxelcraft-src/voxelcraft/dist$ ./Voxelcraft-1.0.0-linux-x86_64.AppImage --no-sandbox

![[Voxelcraft Preview.png]]

