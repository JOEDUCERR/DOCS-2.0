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