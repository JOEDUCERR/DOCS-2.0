### **Qwen 3.8:27B**
```
CUDA_VISIBLE_DEVICES=0,1,2,3 /home/techlead/Documents/llama.cpp/build/bin/llama-server \
  -m /home/techlead/.cache/huggingface/hub/models--ggml-org--Qwen3.8-27B-GGUF/snapshots/0669b98607d47046c7c2b3f801011d54a08cfccf/Qwen3.8-27B-Q4_K_M.gguf \
  --ctx-size 65536 --gpu-layers all --split-mode layer \
  --flash-attn on --cache-type-k q8_0 --cache-type-v q8_0 \
  --batch-size 2048 --ubatch-size 512 --threads 4 \
  --parallel 1 --cache-reuse 256 \
  --reasoning-budget 8192 --jinja \
  --alias qwen3.8-27b --host 0.0.0.0 --port 8000
```
### Qwen 3:8B
```
CUDA_VISIBLE_DEVICES=0 /home/techlead/Documents/llama.cpp/build/bin/llama-server \
  -m /home/techlead/.cache/huggingface/hub/models--Qwen--Qwen3-8B-GGUF/snapshots/7c41481f57cb95916b40956ab2f0b139b296d974/Qwen3-8B-Q4_K_M.gguf \
  --ctx-size 32768 --gpu-layers all --flash-attn on \
  --cache-type-k q8_0 --cache-type-v q8_0 \
  --parallel 1 --cache-reuse 256 \
  --reasoning-budget 4096 --jinja \
  --alias qwen3-8b --host 0.0.0.0 --port 8001
```