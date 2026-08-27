
![[Final Architecture.png]]

-----------------------------------------

https://claude.ai/share/28c1abda-e433-4b7c-b80a-a9fffd69fddb (for reference)
sudo systemctl daemon-reload sudo systemctl enable --now llama-27b llama-8b sudo systemctl status llama-27b llama-8b

* First we made used two commands each which first setup 27B on first 3 gpus and 8B on the third gpu.
```
# 27B — GPUs 0,1,2, localhost:8000, 32K ctx, 4K reasoning budget
CUDA_VISIBLE_DEVICES=0,1,2 ~/Documents/llama.cpp/build/bin/llama-server \
  --ctx-size 32768 \
  --reasoning-budget 4096 \
  --jinja \
  --tools all \
  --alias qwen3.8-27b \
  --host 127.0.0.1 \
  --port 8000 \
  --hf-repo ggml-org/Qwen3.8-27B-GGUF \
  --hf-file Qwen3.8-27B-Q4_K_M.gguf

# 8B — GPU 3, localhost:8001, 16K ctx
CUDA_VISIBLE_DEVICES=3 ~/Documents/llama.cpp/build/bin/llama-server \
  --ctx-size 16384 \
  --jinja \
  --tools all \
  --alias qwen3-8b \
  --host 127.0.0.1 \
  --port 8001 \
  --hf-repo Qwen/Qwen3-8B-GGUF \
  --hf-file Qwen3-8B-Q4_K_M.gguf
```
* Then we made both models be systemd services which are on at every startup. (stored in /etc/systemd/system/)
```
sudo systemctl daemon-reload
sudo systemctl enable --now llama-27b llama-8b
sudo systemctl status llama-27b llama-8b
```
* Configuring LightLLM proxy. Made lightllm proxy as a systemd service too.
* Litellm is exposing both models through 127.0.0.1:4000/v1/models
```
Nginx       :80
Open WebUI  :3000
Ollama      :11434
LiteLLM     :4000
llama.cpp   :8000 / :8001
```
* Now we created nginx https gateway with IP allowlist
* We created a cert at /etc/nginx/certs using openssl
* We created site file at sudo nano /etc/nginx/sites-available/ai-gateway
```
server {
    listen 443 ssl;
    server_name 20.20.20.152;

    ssl_certificate     /etc/nginx/certs/gateway.crt;
    ssl_certificate_key /etc/nginx/certs/gateway.key;

    allow 90.90.91.198;
    deny all;

    location / {
        proxy_pass http://127.0.0.1:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_buffering off;
        proxy_read_timeout 300s;
    }
}
```
* curl https://20.20.20.152/v1/models -k -H "Authorization: Bearer sk-<your-key>" (for testing)

```
curl -s http://127.0.0.1:4000/v1/models \
  -H "Authorization: Bearer sk-YOUR_NEW_KEY" | jq
```